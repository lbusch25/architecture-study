# Rosetta Stone: AWS ↔ Azure, Java ↔ .NET

Living reference, built out as you actually study each service/framework rather than front-loaded
all at once — the value is partly in filling in the "notes/gotchas" column as you learn the real
differences, not just knowing the name mapping. Doubles as a study aid for AWS Pro / AZ-305 and
as likely blog/portfolio content later, directly reinforcing the cloud-agnostic positioning.

Also directly useful for the startup's SOC2 push: AWS Pro's coverage of IAM, encryption,
logging/monitoring, and incident response maps closely onto SOC2 control domains — this isn't
purely resume study, it's immediately applicable.

## AWS ↔ Azure service mapping

### Compute
| AWS | Azure | Notes / gotchas |
|---|---|---|
| EC2 | Virtual Machines | |
| Lambda | Azure Functions | |
| ECS | Azure Container Apps / ACI | |
| EKS | AKS | |
| Elastic Beanstalk | App Service | |

### Storage
| AWS | Azure | Notes / gotchas |
|---|---|---|
| S3 | Blob Storage | |
| EBS | Managed Disks | |
| EFS | Azure Files | |

### Database
| AWS | Azure | Notes / gotchas |
|---|---|---|
| RDS (Postgres/MySQL/etc.) | Azure Database for PostgreSQL/MySQL, Azure SQL Database | |
| DynamoDB | Cosmos DB | Very different consistency/partitioning models underneath the surface similarity — worth digging into during AZ-305 |
| ElastiCache (Redis) | Azure Cache for Redis | |

### Networking
| AWS | Azure | Notes / gotchas |
|---|---|---|
| VPC | VNet | |
| Route 53 | Azure DNS | |
| CloudFront | Azure Front Door / CDN | |
| ALB / NLB | Application Gateway / Azure Load Balancer | |
| API Gateway | Azure API Management | |
| Direct Connect | ExpressRoute | |

### Messaging / eventing
| AWS | Azure | Notes / gotchas |
|---|---|---|
| SQS | Service Bus (queues) | |
| SNS | Service Bus (topics) / Event Grid | |
| EventBridge | Event Grid | |
| Kinesis | Event Hubs | |
| Step Functions | Durable Functions / Logic Apps | |

### Identity / security
| AWS | Azure | Notes / gotchas |
|---|---|---|
| IAM | Entra ID + RBAC | Different mental model: AWS IAM is more policy-document-centric, Azure leans on role assignments over resource scopes — worth being precise about this distinction in interviews |
| Secrets Manager | Key Vault | |
| KMS | Key Vault (keys) / Managed HSM | |
| Cognito | Entra External ID (formerly Azure AD B2C) | |
| WAF | Azure WAF | |

### IaC / deployment
| AWS | Azure | Notes / gotchas |
|---|---|---|
| CloudFormation | ARM templates / Bicep | Terraform is the neutral layer across both — this is the actual cloud-agnostic proof point |
| SAM / CDK | Bicep / Pulumi | |
| CodePipeline / CodeBuild | Azure Pipelines | |

### Observability
| AWS | Azure | Notes / gotchas |
|---|---|---|
| CloudWatch | Azure Monitor | |
| CloudTrail | Activity Log | |
| X-Ray | Application Insights | |

### Container registry
| AWS | Azure | Notes / gotchas |
|---|---|---|
| ECR | Azure Container Registry (ACR) | |

## Java ↔ .NET mapping

| Java / Spring | .NET | Notes / gotchas |
|---|---|---|
| Spring Boot | ASP.NET Core | |
| Spring MVC / WebFlux | ASP.NET Core MVC / Minimal APIs | |
| Spring Data JPA + Hibernate | Entity Framework Core | |
| Spring Security | ASP.NET Core Identity + Authorization | |
| Maven / Gradle | NuGet + MSBuild (.csproj) | |
| JUnit + Mockito | xUnit/NUnit + Moq | |
| Spring Boot Actuator | ASP.NET Core Health Checks + built-in diagnostics | |
| Spring Cloud Config / Netflix stack | `IConfiguration`/Options pattern; Steeltoe for Spring-Cloud-like patterns | |
| `ApplicationContext` / `@Autowired` (DI) | `Microsoft.Extensions.DependencyInjection` | |
| SLF4J / Logback | Serilog / `Microsoft.Extensions.Logging` | |
