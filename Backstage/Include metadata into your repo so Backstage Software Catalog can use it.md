
# Include metadata into your repo so Backstage Software Catalog can use it

Backstage Software Catalog Hierarchy & Best-Practice Structure
Related: [Software Catalog]



![image-20251219-135826.png](assets/Include%20metadata%20into%20your%20repo%20so%20Backstage%20Software%20Catalog%20can%20use%20it%20-%20image-20251219-135826.png)

Backstage uses **YAML entities** to describe all parts of your system. These entities form a **hierarchy**:
Kind (strict schema):

`Domain → System → Component → (Resource / API / Template)`

they are linked to each other through catalog-info.yaml entry like (all in spec.xxx):

`spec: dependsOn: NB: Systems should not dependOn Component, but include them -resource:<resource1Name> -resource:<resource2Name> dependencyOf: -component:default/checkout-ui #(reverse of dependsOn - rarely used directly) providesApi: -<ApiName> consumesApis: -api:default/auth-api system: payments # part of the Payment system subcomponentOf:component:default/platform domain:commerce owner:platform-team`

and then displayed like dependency diagram on Backstage Overview tab or its own Graph view like below
System →`hasPart`→ Component(s) `→ dependsOn` → Resource(s)

Open image-20260127-135412.png

![image-20260127-135412.png](assets/Include%20metadata%20into%20your%20repo%20so%20Backstage%20Software%20Catalog%20can%20use%20it%20-%20image-20260127-135412.png)

Component ->`dependsOn` → Resource(s)

Open image-20260127-133031.png

![image-20260127-133031.png](assets/Include%20metadata%20into%20your%20repo%20so%20Backstage%20Software%20Catalog%20can%20use%20it%20-%20image-20260127-133031.png)

Cheat sheet of possible relationship entries

| spec field | creates relation |
| --- | --- |
| `spec.system` | `partOf` |
| `spec.domain` | `partOf` |
| `spec.dependsOn` | `dependsOn` |
| `spec.providesApis` | `providesApi` |
| `spec.consumesApis` | `consumesApi` |
| `spec.owner` | `ownedBy` |

Recommended minimum

`spec: owner: system: dependsOn: providesApis: consumesApis:`

#### **Top-Level: Domain**

A **Domain** is a broad business/technical grouping.

Examples:

- `payments`
- `travel`
- `analytics`
- `platform`
- `observability`

#### **When to use:**
If your organization has 5–10+ major "areas of responsibility".

**System (a logical group of services)**

A **System** defines ONE product / ONE project made of many repos.

example:

`anthony-travel-system`

Includes:

- frontend Next.js
- backend API
- DB schema / migrations repo
- Terraform infra repo (Component of type: infrastructure)
- Observability repo (dashboards, logs, SLOs)
- CI/CD workflows repo
- IaC for S3 buckets, EKS namespaces, roles
- etc.

NB: **System** will have in Backstage interface its own Code Quality Tab pulled from SonarQube projects, potentially with aggregated code quality or at least links of all components Code Quality) while **Component** will have only Code Quality Section on Overview Tab (e.g. no its own Code Quality Tab).

#### **Components (your repos/services)**

Each repo becomes one **Component**, with a `type`.

Common component types (could be anything, no strict schema):

- `service` (backend/API)
- `website` (frontend) - :warning: by default - won’t show CI/CD Tab, Jira Dashboard in Backstage even if annotations do exists :warning:
- `infrastructure` (Terraform)
- `library`
- `documentation`
- `resource` (AWS resources DBs, queues, buckets)

Types

#### **Resources (DBs, queues, S3 buckets, shared configs)**

These represent **AWS resources** — important for EKS, RDS, S3, etc.

#### **APIs (optional but useful)**

If backend exposes APIs or schemas → register them as `API` entity.

**Templates (for spinning new services/envs)**

This is what you will integrate with Backstage + Terraform.

Your template could:

- create a new repo
- clone Terraform modules
- generate EKS namespace
- create CI/CD pipeline
- generate component.yaml
- embed secrets namespaces
- generate Observability dashboards

Below is example project hierarchy:
  `DOMAIN: travel └── SYSTEM: anthony-travel-system ├── COMPONENT: anthony-frontend (website) ├── COMPONENT: anthony-backend (service) ├── COMPONENT: anthony-infra (terraform) ├── COMPONENT: anthony-observability (dashboards, logs, metrics) ├── COMPONENT: anthony-db (schema/migrations) ├── RESOURCE: anthony-db-rds (SQL Server) ├── RESOURCE: anthony-travel-bucket (S3) ├── RESOURCE: anthony-eks-podidentity ├── API: anthony-api (OpenAPI spec) └── TEMPLATE: anthony-service-template (IaC + repo bootstrap)`
  Or in tree representation
  `Domain └─ System ├─ Components (parts of a system, not dependsOn!) │ ├─ Backend (runtime) │ ├─ Frontend (runtime) │ ├─ Infra (Terraform) │ ├─ Observability │ └─ DB schema (runtime) ├─ APIs └─ Resources (RDS, S3, IAM roles, queues)`

### **Backstage Design Tips**

#### One repo = one Component

This is the simplest and cleanest model.

#### Group everything into Systems

So developers see one logical product rather than 20 repos.

#### Infrastructure-as-Code deserves its own Component

This is the correct way to integrate Terraform with Backstage.

#### Observability-as-Code belongs to a “resource” or “component”

Especially if dashboards/alerts could be stored in GitHub as a code

#### Map AWS resources explicitly

This is used by SRE teams/Support people

#### Use Templates for automating environment creation

This is where Terraform modules plug in.

### Component vs Resource?

Ask yourself - who maintains the code?
If it has its own repo, deployment, ci/cd pipeline, versions, deployments - it is Component.

| Thing | Backstage kind | has its own Repo | Who maintains code? | Does it have its own CI/CD? |
| --- | --- | --- | --- | --- |
| Terraform repo | Component | yes | platfrom team, devops | yes |
| Helm chart repo | Component |  | devops | yes |
| CDK app | Component |  | CDK team | yes |
| RDS instance | Resource | no |  | no |
| S3 bucket | Resource | no |  | no |
| EKS cluster | Resource | no |  | no |

NB: In some companies Terraform code (module) could also be a product -e.g tested, maintained, versioned and released and will provide terraform-module-apis to other Components/Services.

`kind: Component spec: type: infrastructure lifecycle: production providesApis: - terraform-module-api # this`

How backstage tracks this hierarchy is through metadata files in each GitHub repository.
E.g. `catalog-info.yaml`in root of your repo gives Backstage metadata it needs to display and correctly link your project/service in Software Catalog and TechDocs.

Open image-20251210-111453.png

![image-20251210-111453.png](assets/Include%20metadata%20into%20your%20repo%20so%20Backstage%20Software%20Catalog%20can%20use%20it%20-%20image-20251210-111453.png)

Source: [![](https://backstage.io/img/favicon.ico)Descriptor Format of Catalog Entities | Backstage Software Catalog and Developer Platform](https://backstage.io/docs/features/software-catalog/descriptor-format/)
