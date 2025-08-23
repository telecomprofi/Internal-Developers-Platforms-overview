
# Humanitec integrating new private EKS cluster in DirectMode with humanitec agent 
Assumptions:
EKS auto mode cluster with private endpoint
EKS access entries uses instead of aws-auth mapping to IRSA 
Humanitec agent will running inside EKS 'call home' to Humanitec for new Deployments aka GitOps flow
Humanitec Orchestrator is running 'Direct/Agent' mode

```mermaid
flowchart LR

    Step1[Create EKS auto mode private cluster in new AWS account]
    Step2[Create new IAM Role in AWS account with permission to access EKS cluster and trust policy so Humanitec can assume it]
    Step3[Create Cloud Account in Humanitec and point it to IAM Role. Map this account to new EKS k8s-cluster through resource definition type k8s-cluster with criteria env_type. Workloads with specific env_type will deploy to this cluster]
    Step4[Install Humanitec agent into the new EKS cluster to allow it to call GitHub repo, detect new code, container images, and trigger deployments with dependent resources]
    Step5[Create new Humanitec resource of type k8s-cluster-agent that will be implicitly referred from workloads]
    Step6[Create new k8s ClusterRoles and ClusterRole bindings mapped to EKS cluster API Access Entries for Humanitec Agent]
    Step7[Create new k8s ClusterRoles and ClusterRole bindings for Opentofu Container Runner]

    %% Linear flow
    Step1 --> Step2 --> Step3 --> Step4 --> Step5 --> Step6 --> Step7 
```

```mermaid
flowchart LR

    subgraph AWS_Account["AWS Account /EKS Cluster Account/"]
        A1[Generate External ID /random UUID/]
        A2[Create Trust Policy /trust-policy.json/]
        A3[Create IAM Role humanitec-role-to-access-eks-automode-prod-cluster]
        A4[Create IAM Policy: EKS Read + API Access]
        A5[Attach Policy to Role]
        A6[EKS Cluster: eks-automode-prod]
    end

    subgraph Humanitec["Humanitec Orchestrator Portal"]
        H1[Export HUMANITEC_TOKEN]
        H2[Define Cloud Account YAML /aws-role-cloudaccount.yaml/]
        H3[humctl apply → Register AWS Account with Role + External ID]
        H4[Cloud Account: platform-production]
    end

    subgraph Next_Steps["Next Steps /after Step #1/"]
        N1[Step #2: Add Resource type k8s-cluster with criteria /env=prod, app_id=.../]
        N2[Step #3: Install Humanitec Agent in new EKS cluster /private API endpoint/]
        N3[Step #4: Configure Container Runner access → STS Temporary Credentials]
    end

    %% Workflow arrows
    A1 --> A2 --> A3 --> A4 --> A5
    A6 --> A4
    A3 --> H2
    H1 --> H2
    H2 --> H3 --> H4
    H4 --> N1 --> N2 --> N3
```
