# Backstage tech details and security considerations

Backstage consist of 4 main components:

- FrontEnd (JS/TS) that interacts with Users
- BackEnd (Node.js)
- DB (sqlite3/PostgreSQL)
- Plugins that provides both UI extensions and backend functionality

Both FE and BE use `yarn`, `npx` to manage `ts` and `node.js` modules/dependencies

Entrypoints:
- FrontEnd (if run separately usually exposed at **tcp:3000**)

- Backend usually exposed at **tcp:7007**, if run in ‘together’ mode - both FE and BE are listening there
- DB: either sqlite3 in dev mode or PostgreSQL in production mode, **tcp:5432**
- Config files: in ./app/ folder `app-config.yaml`, `app-config.production.yam` for prod and `app-config.local.yaml` for dev env

When Plugin or Provider is added it has to be added to both backend and frontend.
Ex: GitHub OAUTH provider:
`./packages/backend/src/index.ts` - **backend** **TS** file # .ts means it is Server-side component, backend Node.js app code using `plain` TypeScript
`./packages/app/src/App.tsx`- **front end TSX** file # .tsx means that it includes JavaScript XML extension (in-line html tags for better coding exp), react components, front-end, JSX is compiled.

Open image-20251215-104732.png

![image-20251215-104732.png](assets/Backstage%20OSS%20Security%20considerations%20-%20image-20251215-104732.png)

## Difference between development and prod environments - dev environment uses webpack

Q: What is webpack in backstage?
A: It is development only **bundler and dev server** that builds and serves the **frontend UI** (the Backstage app itself) when you run ‘yarn start’ or ‘yarn dev’.
It runs **two things in parallel**:

1. **Frontend (webpack dev server)**
2. **Backend (Node.js API server)**

### Webpack(FrontEnd):

- It takes code from `packages/app` (fe)
- Uses `@backstage/cli`’s webpack configuration (created by `npx @backstage/create-app` )
- Starts a dev server (usually on `http://localhost:3000`)

Log messages from frontEnd are starting with `[app]`:

`[app]: Loaded config from app-config.yaml, app-config.local.yaml [app]: <i> [webpack-dev-server] Project is running at: [app]: <i> [webpack-dev-server] Loopback: http://localhost:3000/, http://127.0.0.1:3000/ [app]: <i> [webpack-dev-server] Content not from webpack is served from '/Users/work/platform-backstage/packages/app/public' directory [app]: <i> [webpack-dev-server] 404s will fallback to '/index.html' [app]: <i> [webpack-dev-middleware] wait until bundle finished: /`

### Backend (Node server):

At the same time:

- `packages/backend` is started via Node
- Exposes APIs (`/api/catalog`, `/api/auth`, etc.)
- Usually runs on `http://localhost:7007`

Webpack **proxies API requests** to the backend.
Log messages from backend are starting with [backend]:

`[backend]: 2026-01-20T11:15:46.504Z search info Collating documents for software-catalog succeeded documentType="software-catalog" [backend]: 2026-01-20T11:15:53.535Z catalog info Read 96 GitHub repositories (90 matching the pattern) target="github-provider:OrgNameRepos" class="GithubEntityProvider" taskId="github-provider:OrgNameRepos:refresh" taskInstanceId="192171c1-65a1-4ac7-ae82-b052abba57e8"`

## Why Backstage uses webpack

Backstage uses webpack (via `@backstage/cli`) because it needs:

- Module federation–like plugin loading
- Fast local dev with HMR
- Support for many internal Backstage plugins
- Controlled build setup across thousands of companies

You **don’t manage webpack directly** — Backstage hides it behind `@backstage/cli`

## webpack config

there is no `webpack.config.js` instead:

- Webpack config is generated internally by:
  `node_modules/@backstage/cli/config/webpack.config.js`
- You only influence it via:
  - `app-config.yaml`
  - `app-config.local.yaml`
  - environment variables
  - Backstage plugin setup

This is **intentional** so upgrades don’t break things.

Q: What runs in production?

A: Webpack is **ONLY for development**.

In production: `yarn build`

- Webpack builds **static assets**
- Output goes to:packages/app/dist/
- Backend serves these static files (one can put them behind CloudFront / S3 / Nginx)

No **webpack dev server** in prod.
When backstage runs as single container (e.g. on AWS ECS cluster) it runs like below
`yarn build`
`yarn start-backend`

typical production flow:
`Docker build`
`├─ yarn install`
`├─ yarn build #webpack runs once, static assets are built`
`└─ node packages/backend`

From version 1.24 and up there is New Backend which is more protected (e.g. now could be exposed to public internet not as before).

Upgrade process: [Extremely complicated](https://www.youtube.com/watch?v=5q6yCSSXIXM) :cry: , will very likely break 3pv plugins.
Upgrade cycle: every month :cry:

## Sources:

[![](https://backstage.io/img/favicon.ico)Backstage Threat Model | Backstage Software Catalog and Developer Platform](https://backstage.io/docs/overview/threat-model/#operator-responsibilities)

[![](https://backstage.io/img/favicon.ico)Architecture overview | Backstage Software Catalog and Developer Platform](https://backstage.io/docs/overview/architecture-overview/)

[![](https://backstage.github.io/upgrade-helper/favicon.ico)Upgrade Backstage applications](https://backstage.github.io/upgrade-helper/?yarnPlugin=0&from=1.32.4&to=1.46.0-next.2) - Upgrade helper :exploding_head:
