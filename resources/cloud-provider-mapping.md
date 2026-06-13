# CV0-004 Concept → Cloud Provider Mapping

> CompTIA Cloud+ is **vendor-neutral**, but every objective maps cleanly to AWS, Azure, and GCP. Use this as a quick mental cross-reference.

| CompTIA Concept | AWS | Azure | GCP |
|---|---|---|---|
| **Compute — IaaS** | EC2 | Azure Virtual Machines | Compute Engine (GCE) |
| **Compute — PaaS (app)** | Elastic Beanstalk, App Runner | App Service, Azure Container Apps | App Engine, Cloud Run |
| **Compute — FaaS** | Lambda | Azure Functions | Cloud Functions (2nd gen) |
| **Serverless containers** | Fargate | Azure Container Apps | Cloud Run |
| **Managed Kubernetes** | EKS | AKS | GKE / GKE Autopilot |
| **Object storage** | S3 | Blob Storage | Cloud Storage |
| **Block storage** | EBS | Azure Disk | Persistent Disk |
| **Shared file storage** | EFS, FSx | Azure Files (SMB/NFS) | Filestore |
| **Archive storage** | S3 Glacier | Archive Blob | Coldline / Archive |
| **Relational DB (managed)** | RDS, Aurora | Azure SQL, Database for MySQL/PostgreSQL | Cloud SQL, AlloyDB |
| **NoSQL (key-value)** | DynamoDB | Cosmos DB (Table API) | Firestore, Bigtable |
| **Caching** | ElastiCache (Redis/Memcached) | Azure Cache for Redis | Memorystore |
| **VPC / VNet** | VPC | Virtual Network | VPC |
| **Load Balancer L4** | NLB | Azure Load Balancer | TCP/UDP Load Balancer |
| **Load Balancer L7** | ALB | Application Gateway | HTTP(S) Load Balancer |
| **DNS** | Route 53 | Azure DNS | Cloud DNS |
| **CDN** | CloudFront | Azure Front Door / CDN | Cloud CDN |
| **Private link** | PrivateLink | Private Link | Private Service Connect |
| **Dedicated line** | Direct Connect | ExpressRoute | Cloud Interconnect |
| **IAM** | IAM + AWS SSO | Entra ID (Azure AD) | Cloud IAM + Workforce Identity |
| **Secrets / KMS** | KMS, Secrets Manager | Key Vault | Cloud KMS, Secret Manager |
| **Monitoring / Logs** | CloudWatch, CloudTrail | Azure Monitor, Log Analytics | Cloud Monitoring, Cloud Logging |
| **IaC** | CloudFormation, CDK | ARM / Bicep | Cloud Deployment Manager |
| **Multi-cloud IaC** | Terraform (HashiCorp) | Terraform (HashiCorp) | Terraform (HashiCorp) |
| **Disaster Recovery service** | AWS Elastic DR (formerly CloudEndure) | Azure Site Recovery | Google Cloud DR Service (formerly Actifio) |
| **AI/ML PaaS** | SageMaker, Bedrock | Azure ML, Azure OpenAI | Vertex AI, Gemini API |

---

> **Exam tip:** CompTIA may use provider-neutral names. Practice translating "object storage" → S3, "managed Kubernetes" → EKS/AKS/GKE, "FaaS" → Lambda/Functions/Cloud Functions in your head.
