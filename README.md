# Internal-Developers-Platforms-overview
## Backstage with Humanitec orchestrator Platform Engineers and DevOps work flow
Assumptions:  

```
Public Cloud  : AWS  

k8s cluster   : EKS Auto Mode  

CI/CD         : GitHub Actions  

IaaC          : Terraform/Opentofu

Org Structure : DevOps in Dev Teams, Platform Enginners separate team

Issue         : Who supports Platform Infra (Platform EKS clusters, AWS accounts, Volumes, SSO/Accesses, GitHub permissions, SonarQube etc)
```

```mermaid
graph TD
    subgraph "Phase 1: Manual Setup and Discovery"
        A[Start: New Application Request] --> B{Define App Requirements};
        B --> C[Platform and DevOps: Manually Create AWS Resources   - VPC, Subnets, EKS, RDS, S3, etc.- ];
        C --> D[Platform and DevOps: Manually Deploy App to EKS   - using kubectl/Helm- ];
        D --> E[Verify Manual Deployment in Lower Envs];
    end

    subgraph "Phase 2: IaC and Workload Automation"
        E --> F[DevOps: Create Terraform Modules for AWS Resources];
        F --> G[DevOps: Parameterize Terraform with Humanitec Placeholders   e.g., context.env.id];
        G --> H[DevOps: Push Terraform Modules to a Dedicated Git Repo];

        E --> I[Platform: Create score.yaml for the Application];
        I --> J[Platform: Define Workload Dependencies and Config];
        J --> K[Platform: Push score.yaml to the Application Repo];
    end

    subgraph "Phase 3: Humanitec Integration"
        H --> L[Platform: Create Humanitec Resource Definitions - .yaml- ];
        L --> M[Platform: Map Resource Definitions to Terraform Modules];
        M --> N[Platform: Push Resource Definitions to a Dedicated Git Repo];

        K --> O[DevOps: Set up Humanitec App and Environments];
        O --> P[DevOps: Connect Humanitec to AWS via OIDC/IAM Roles];
        P --> Q[DevOps: Register Resource Definitions in Humanitec];
    end

    subgraph "Phase 4: CI/CD and Automation"
        Q --> R[DevOps: Create GitHub Actions CI/CD Pipeline];
        R --> S["CI Pipeline:   1. SAST - e.g., SonarQube-    2. Secret Scanning - e.g., GitGuardian-    3. Build and Push Immutable Image to ECR   4. Container Scan - e.g., Trivy-    5. Generate SBOM"];
        S --> T["CD Pipeline:   1. Trigger on PR Merge to Main - Trunk-Based-    2. Humanitec CLI creates/updates resources   3. Deploy to Lower Envs"];
        T --> U[Automated Integration and E2E Tests];
        U --> V{Manual Approval for Prod};
        V --> W[Deploy to Production - Multi-Region- ];
    end

    subgraph "Security and Compliance - Continuous- "
        Z1[PR Reviews and Approvals]
        Z2[IaC Scans - e.g., Checkov- ]
        Z3[IAM Least Privilege]
        Z4[EKS Hardening and Minimal Maintenance]
        Z1 & Z2 & Z3 & Z4 -.-> A & C & F & I & L & R;
    end

    style A fill:#dbeafe,stroke:#3b82f6,stroke-width:2px
    style E fill:#dbeafe,stroke:#3b82f6,stroke-width:2px
    style W fill:#dbeafe,stroke:#3b82f6,stroke-width:2px
    style V fill:#fef9c3,stroke:#eab308,stroke-width:2px


```

Other view (anchored to Team)
<img width="9997" height="2130" alt="image" src="https://github.com/user-attachments/assets/4dfa8164-5c45-4f8d-816f-72283266611f" />

```mermaid
graph TD
    A[Initial Setup Phase] --> B[Manual Creation]
    B --> B1[Platform Engineers: Create AWS Resources Manually]
    B --> B2[DevOps: Create K8s Manifests/Helm Charts Manually]
    
    A --> C[Automation Phase]
    C --> C1[Platform Engineers: Terraform AWS Resources]
    C1 --> C1a[Shared EKS for Non-Prod]
    C1 --> C1b[Dedicated EKS per Prod Account]
    C --> C2[DevOps: Convert to score.yaml]
    
    C --> D[Humanitec Integration]
    D --> D1[Platform Engineers: Create Resource Definitions]
    D1 --> D1a[Template with Humanitec Variables]
    D --> D2[DevOps: Setup score.yaml with Placeholders]
    
    D --> E[CI/CD Pipeline Creation]
    E --> E1[Platform Engineers]
    E1 --> E1a[Resource Module Repos]
    E1 --> E1b[Resource Definition Repos]
    E1 --> E1c[IAM/OIDC Setup]
    E --> E2[DevOps]
    E2 --> E2a[App Repo CI/CD]
    E2 --> E2b[Humanitec Integration]
    
    E --> F[Security Gates]
    F --> F1[SAST/SCA]
    F --> F2[IaC Scans]
    F --> F3[Container Scans]
    F --> F4[Immutable ECR]
    F --> F5[PR Reviews]
    
    G[Ongoing Operations] --> G1[Platform Engineers: Minimal EKS Maintenance]
    G --> G2[DevOps: Workload Deployment]
    
    style A fill:#f9f,stroke:#333
    style B fill:#bbf,stroke:#333
    style C fill:#bbf,stroke:#333
    style D fill:#bbf,stroke:#333
    style E fill:#bbf,stroke:#333
    style F fill:#fbb,stroke:#f66
    style G fill:#bfb,stroke:#393
```

