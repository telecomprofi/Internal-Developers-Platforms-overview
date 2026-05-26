
Backstage onboarding checklist - template


To make onboarding of new Dev team to Internal Developer Portal (Backstage) more efficient it makes sense to gather required information about application/services so adding it to software catalog quickly give Development teams access to most Plugins and features, and encourages team to keep information in metadata file `catalog-info.yaml`, `mkdocs.yaml` up to date.

## General

| **Field** | **Details/URL** |
| --- | --- |
| Domain (aka vertical) |  |
| Project name |  |
| Service name |  |
| Service type |  |
| Team Name that develops this project/service and/or link to Confluence team memebers list |  |
| Team Lead or Lead Developer |  |
| Team’s slack channel |  |

| **Field** | **Details/URL** |
| --- | --- |
| GitHub Repo (aka slug like `<github-org>/<repo-name>`) |  |
| Jira Project key(s) |  |
| Jira JQL to filter tickets for Dashboard (e.g. labels = prjctnm) |  |
| Confluence Space (or docs URL) |  |
| Environments list (e.g. dev, qa, uat, prod) |  |
| Endpoint/Web UI urls, please list for all envs |  |

| **Field** | **Details** |
| --- | --- |
| DependsOn (names, repos) / List other internal services/APIs that service depends on |  |
| Dependants (names, repos) / Other Internal Services/APIs that depend on this Service (names, repos) |  |

## Security

| **Field** | **Details/URL** |
| --- | --- |
| SonarQube Project name |  |
| Security dashboards URLs (GHAS, Dependabot, trivy, Snyk) |  |
| DataDog Cloud Security URL |  |
| How to request access |  |

## Observability

| **Field** | **Details/URL** |
| --- | --- |
| DataDog service name(s), prod env |  |
| DataDog dashboard(s), prod env |  |
| DataDog Graph with key prod KPI (to display on Overview page) |  |
| DataDog Logs |  |
| DataDog Metrics |  |
| DataDog Traces |  |
| DataDog Monitors (Four Golden Signals) |  |
| Synthetic test(s) |  |
| SLO/SLI monitor |  |
| DORA metrics dashboard |  |
| AuditLog |  |

## FinOps

| **Field** | **Details** |
| --- | --- |
| AWS Tags (CostCenter, ProjectName, Owner, Env, etc) |  |
| AWS account #s (non-prod, prod) |  |

## Support/Reliability

| **Field** | **Details** |
| --- | --- |
| DataDog Incident Management Team (on-call rotation, url), Escalation path |  |
| Infrastructure/DevOps team |  |
| Runbooks (Confluence URL or directory in repo with .md files) |  |
| Slack channel with production Alerts |  |
| Infrastructure repo (Terraform, CloudFormation, Pulumi etc) |  |
| Root Cause Analysis (RCA) list for past P1 prod Incidents |  |



