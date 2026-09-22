# Free/Local Tooling for AWS, Azure, Kubernetes & GitHub Actions

Goal: do as much of `STUDY-PLAN.md` and the monthly system design case studies as possible
without spending money. Strategy is **local-first, real-cloud-in-short-verified-bursts**:
build and iterate locally, only touch a real AWS/Azure account to validate the handful of
things that genuinely require it, then tear down immediately.

Note: free-tier terms and emulator licensing change often (LocalStack's free tier changed
materially as recently as March 2026). Treat the specifics below as "checked in Sept 2026" —
re-verify current terms on the linked pages before relying on them for anything cost-sensitive.

## Philosophy / safety net (read this part regardless of tool choice)

1. **Everything real-cloud goes through Terraform.** If it's not `terraform apply`/`terraform destroy`, don't build it — the destroy step is your cost control.
2. **Set billing alarms at trivial thresholds before doing anything else** — AWS Budgets alert at $1, Azure Cost Management budget alert at $1. Do this the same day you create the accounts, not after.
3. **Real-cloud sessions are same-day sandboxes:** spin up, validate/screenshot for the case study, `terraform destroy`, done. Never leave real infra running overnight "to keep working on it tomorrow."
4. **Local emulators for iteration, real cloud only for the specific managed-service behaviors emulators can't reproduce** (IAM/Entra nuances, real EKS/AKS control plane behavior, real CDN edge behavior, actual cross-service IAM/RBAC wiring).

## AWS — local toolkit

| Tool | What it covers | Notes |
|---|---|---|
| **LocalStack (Hobby plan)** | 30+ services emulated (Lambda, S3, DynamoDB, SQS, SNS, Kinesis, ECS, SSM, Secrets Manager, IAM, etc.) | Free tier as of 2026 is the **Hobby plan**, not the old "Community" edition (Community was discontinued/no longer updated as of March 2026). Hobby requires a free LocalStack account + auth token, and is licensed for **non-commercial use only** — fine for personal study, not for anything you'd resell. Runs via Docker. |
| **`tflocal`** | Terraform against LocalStack | Thin wrapper around `terraform` that points the AWS provider at your local LocalStack endpoint — lets you write real Terraform HCL and never touch a real AWS bill for supported services. |
| **AWS SAM CLI (`sam local`)** | Local Lambda + API Gateway invocation | Good for fast Lambda iteration without LocalStack if you only need compute, not the full service graph. |
| **DynamoDB Local** | DynamoDB emulation | Official Amazon-published Docker image/jar, free, no account needed. |
| **MinIO** | S3-compatible object storage | Useful as a lighter-weight S3 stand-in, or for practicing S3-compatible APIs outside LocalStack's licensing. |
| **Moto** | Python-based AWS service mocking | Best for unit tests, not for standing up a running "environment" to click around in. |

**Gap to know:** things like real EKS control plane behavior, real CloudFront edge caching, and real cross-account IAM policy evaluation aren't fully faithful in LocalStack's free tier — treat those as "validate briefly in a real, budget-alarmed account" items.

## Azure — local toolkit

Azure doesn't have a single LocalStack-equivalent; it's a per-service set of official emulators.
Coverage is good for data/storage/compute-local, thin for anything IAM/networking-shaped.

| Tool | What it covers | Notes |
|---|---|---|
| **Azurite** | Blob, Queue, Table storage | Official Microsoft emulator, free, npm or Docker, cross-platform. |
| **Azure Cosmos DB Emulator** | Cosmos DB (SQL, MongoDB, Cassandra, Gremlin, Table APIs) | Official, free, Docker image available (Linux/Mac/Windows). |
| **Azure Service Bus Emulator** | Service Bus queues/topics | Ships as a Docker Compose stack with a SQL Server/Edge backend — heavier to run than Azurite but free. |
| **Azure Functions Core Tools** | Functions runtime | Run and debug Functions locally for free, no subscription needed. |
| **Azure SQL Edge** | SQL Server-compatible local DB | Free container, useful for anything you'd otherwise put in Azure SQL. |
| **Event Hubs emulator** | Event Hubs (Kafka-surface) | Exists but less mature; alternative is running local Kafka/Redpanda and treating it as a stand-in when just practicing the event-driven pattern rather than the Azure-specific service. |

**Gap to know:** there's no free local stand-in for AKS control plane specifics, Entra ID/RBAC wiring, networking (VNets/private endpoints/App Gateway), or Azure Policy. These are exactly the AZ-305-relevant topics you'll need to validate in a real, budget-alarmed Azure free account — but keep those sessions short and destroy-after-use.

## Kubernetes (shared across AWS/Azure practice)

| Tool | Use |
|---|---|
| **kind** / **k3d** / **minikube** | Free local Kubernetes clusters — do all manifest/Helm/operator iteration here. |
| Real **EKS**/**AKS** | Only spin up briefly to validate the specific managed-control-plane behavior or to take the case-study screenshot/demo for your portfolio, then tear down (EKS control plane bills hourly the moment it exists; AKS control plane is free but the node VMs aren't). |

## GitHub Actions — local + free

| Option | Notes |
|---|---|
| **`act` (nektos/act)** | Runs your `.github/workflows/*.yml` locally in Docker, actively maintained, free. Fastest iteration loop — no push-and-wait cycle. |
| **Public GitHub repo** | GitHub Actions minutes are **unlimited and free on public repositories** (private repos get a limited free monthly allowance). Since this is a personal study/portfolio repo, making it public sidesteps the cost question entirely for real CI runs, not just local ones. |

Practical flow: iterate on workflow YAML with `act` locally, push to the public portfolio repo
to get a real, free CI run for anything `act` can't fully simulate (e.g., OIDC federation to a
real cloud, matrix runners).

## Free-tier reference (verify current numbers before relying on them)

- **AWS**: new accounts currently get credits (structure has changed year over year — check `aws.amazon.com/free` for the current figure) plus 30+ "Always Free" services with permanent monthly allowances (Lambda, DynamoDB, and CloudFront have the most clearly published numbers). Set the $1 budget alarm regardless of which credit structure is active.
- **Azure**: new accounts get an introductory credit for the first 30 days, plus 55+ "Always Free" services with permanent monthly allowances (Functions executions, Cosmos DB RU/s + storage, and App Service are the most relevant to this plan). Check `azure.microsoft.com/en-us/pricing/free-services` for the current list.

## Quick decision guide

| You want to practice... | Do it here first | Only go real-cloud for... |
|---|---|---|
| Event-driven messaging (SQS/SNS, Service Bus) | LocalStack / Azure Service Bus emulator | Cross-service IAM/RBAC wiring |
| Object storage / data lake patterns | MinIO / Azurite | CDN edge behavior, real access-control policy |
| Serverless compute | SAM local / Functions Core Tools | Cold-start/scale behavior at real load |
| Kubernetes workloads | kind / k3d | EKS/AKS-specific control-plane features, final demo screenshot |
| CI/CD pipelines | `act` | OIDC-to-cloud auth, real matrix runners |
| Networking (VNets, VPCs, private endpoints) | Terraform plan/validate only | Almost everything else here is real-Azure/real-AWS-only — budget-alarm and time-box these sessions tightly |

## Sources

- [LocalStack Pricing](https://www.localstack.cloud/pricing)
- [Important Updates to Pricing & Packaging for LocalStack for AWS](https://blog.localstack.cloud/2026-upcoming-pricing-changes/)
- [Use the Azurite emulator for local Azure Storage development](https://learn.microsoft.com/en-us/azure/storage/common/storage-use-azurite)
- [Azure Cosmos DB Emulator (Docker/local)](https://docs.azure.cn/en-us/cosmos-db/emulator)
- [nektos/act on GitHub](https://github.com/nektos/act)
- [AWS Free Tier](https://aws.amazon.com/free/)
- [Explore Free Azure Services](https://azure.microsoft.com/en-us/pricing/free-services)
