## Golden Path Example for Backstage

Golden Path in platform engineering is a collection of either tickets or tasks that every team has to perform when new project is started. Because this work is often repetitive we can include it into Scaffolder template in Backstage and speed-up these tasks execution or completely automate them.

### Example golden path for Next.js project:

1. Create a new repo with Next.js source code template and Dockerfile.
2. Add CI/CD pipeline (GitHub Actions) with build, test, publish, deploy to QA, run integration tests, promote to staging, run integration tests there, wait for approval, then tag & promote/release to production.
3. Add SAST, vulnerability, and secret scans (e.g., SonarQube).
4. Create AWS infrastructure if the project reuses an existing AWS account—such as ECS cluster, RDS DB, S3 bucket—from existing Terraform IaaC stacks. Important question: do platform services team resources exist to maintain these stacks?
5. Add DORA metrics collection steps i.e. release KPIs, reliability KPIs, MTTR for production incidents.
6. Add DataDog monitoring default dashboard as code for public endpoints, SLI, SLO, error budgets.
7. Populate metadata catalog-info.yaml file used for Backstage software catalog.
8. Add FinOps data based on a custom AWS CostExplorer SDK script and team ID, project ID, cost center tags, or AWS account numbers used to deploy non-prod and prod environments.
9. In the future, automate granting AWS account access based on Zendesk tickets and approvals that generate PRs adding users to existing groups in IaaC Terraform code or Identity Center repo. This requires predefined standard project roles and tracking Project - AWS Account - Environment - User Groups/Roles - UserID relationships in an RBAC database outside Backstage.
