# 09. Security, Identity, and Compliance

## Intro: Security, Identity, and Compliance

AWS security is a shared responsibility model:
- **AWS** is responsible for security *of* the cloud (hardware, infrastructure, managed services)
- **Customer** is responsible for security *in* the cloud (data, IAM, network config, encryption)

Key pillars:
- **Identity & Access Management (IAM)** — who can do what
- **Data Protection** — encryption at rest and in transit
- **Infrastructure Security** — VPCs, security groups, NACLs
- **Detection & Compliance** — logging, monitoring, auditing

---

## Principle of Least Privilege

> Grant only the permissions required to perform a task — nothing more.

**Why it matters:**
- Reduces attack surface
- Limits blast radius if credentials are compromised
- Required for compliance (SOC 2, HIPAA, PCI-DSS)

**How to apply it in AWS:**
- Start with zero permissions, add only what's needed
- Use IAM policies scoped to specific resources (`arn:aws:s3:::my-bucket/*` instead of `*`)
- Prefer **IAM Roles** over long-lived IAM User credentials
- Use **Permission Boundaries** to cap maximum permissions a role/user can have
- Regularly audit with **IAM Access Analyzer** and remove unused permissions

---

## Data Masking and Anonymisation

Techniques to protect sensitive data (PII, PHI, financial records):

| Technique | Description | Reversible? |
|---|---|---|
| **Masking** | Replace sensitive characters (e.g., `****1234`) | No |
| **Tokenisation** | Replace data with a random token; mapping stored separately | Yes (via vault) |
| **Pseudonymisation** | Replace identifiers with pseudonyms | Yes (with key) |
| **Anonymisation** | Remove all identifying information permanently | No |
| **Generalisation** | Replace exact value with a range (age 34 → 30–40) | No |
| **Data Perturbation** | Add statistical noise to numeric data | No |

**AWS tools:**
- **Amazon Macie** — auto-discover and classify sensitive data in S3
- **AWS Glue** — apply transformations to mask data in ETL pipelines
- **Lake Formation** — column-level and row-level security on data lake

---

## SageMaker Security: Encryption at Rest and in Transit

### Encryption at Rest
- SageMaker encrypts all data at rest using **AWS KMS**
- Applies to: training data (S3), model artifacts (S3), EBS volumes on instances, SageMaker Feature Store
- You can specify a **customer-managed KMS key (CMK)** or use the AWS-managed default key
- S3 bucket policies should enforce `aws:SecureTransport` and `s3:x-amz-server-side-encryption`

### Encryption in Transit
- All data in transit uses **TLS 1.2+** by default
- SageMaker endpoints communicate over HTTPS
- Inter-container traffic during distributed training can be encrypted (adds overhead — enable only when required)
- Enable via `enable_inter_container_traffic_encryption=True` in the Estimator

### Key Points
- Training jobs run inside a managed compute environment; data pulled from S3 is encrypted in transit
- SageMaker notebooks store data on encrypted EBS volumes
- Use **VPC mode** to ensure all traffic stays within your private network

---

## SageMaker Security: VPCs, IAM, Logging and Monitoring

### VPC Configuration
- By default, SageMaker training jobs and endpoints run in a **SageMaker-managed VPC**
- Launch in your **own VPC** to control network access: specify `subnets` and `security_groups`
- Use **VPC endpoints (PrivateLink)** for S3, SageMaker API, and ECR to avoid traffic leaving AWS network
- Disable internet access on training instances when using custom VPC (set `enable_network_isolation=True`)

### IAM for SageMaker
- SageMaker **Execution Role** — assumed by SageMaker to access S3, ECR, KMS, CloudWatch, etc.
- Follow least privilege: scope role policies to specific buckets/resources
- Use **resource-based policies** on S3 buckets to allow only the SageMaker role
- Notebook instance role should not have `iam:PassRole` unless explicitly required

### Logging and Monitoring
- **Amazon CloudWatch** — metrics and logs for training jobs, endpoints, and notebooks
  - Monitor: `InvocationsPerInstance`, `ModelLatency`, `CPUUtilization`, `MemoryUtilization`
- **AWS CloudTrail** — logs all SageMaker API calls (who called what, when, from where)
- **SageMaker Model Monitor** — detect data drift and model quality degradation in production
- Enable **VPC Flow Logs** to capture all network traffic in/out of your SageMaker VPC

---

## IAM Introduction: Users, Groups, Policies

**IAM (Identity and Access Management)** is a global AWS service for managing access.

### Core Components

| Component | Description |
|---|---|
| **Root Account** | Created with AWS account; has full access — should not be used day-to-day |
| **IAM User** | Individual identity with long-term credentials (username + password / access keys) |
| **IAM Group** | Collection of users; policies attached to a group apply to all members |
| **IAM Policy** | JSON document defining permissions (Allow/Deny on Actions/Resources) |
| **IAM Role** | Identity with temporary credentials; assumed by AWS services, users, or external identities |

### Best Practices
- Enable MFA on root account and all privileged users
- Never share credentials
- Use groups to assign permissions, not individual users
- Rotate access keys regularly
- One physical user = one IAM user

---

## IAM Policies

Policies are JSON documents that define what actions are allowed or denied.

### Policy Structure
```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": ["s3:GetObject", "s3:PutObject"],
      "Resource": "arn:aws:s3:::my-bucket/*",
      "Condition": {
        "StringEquals": {"aws:RequestedRegion": "us-east-1"}
      }
    }
  ]
}
```

### Policy Types

| Type | Attached To | Notes |
|---|---|---|
| **Managed Policy (AWS)** | Users, Groups, Roles | Predefined by AWS; read-only |
| **Managed Policy (Customer)** | Users, Groups, Roles | Custom; reusable |
| **Inline Policy** | Single user/group/role | Tightly coupled; deleted with identity |
| **Resource-based Policy** | AWS resource (e.g., S3) | Grants cross-account access |
| **Permission Boundary** | User or Role | Sets maximum permissions |
| **SCP (Service Control Policy)** | AWS Organisation OU/Account | Org-wide guardrails |

### Policy Evaluation Logic
1. Explicit **Deny** always wins
2. Then check for explicit **Allow**
3. Default is **implicit Deny**

---

## IAM MFA

**Multi-Factor Authentication (MFA)** adds a second layer of security beyond passwords.

### MFA Device Types

| Type | Example |
|---|---|
| **Virtual MFA** | Google Authenticator, Authy (TOTP) |
| **Hardware TOTP token** | Gemalto device |
| **FIDO Security Key** | YubiKey (U2F/WebAuthn) |

### Key Points
- Always enable MFA on the **root account**
- Enforce MFA via IAM policy condition: `"Condition": {"Bool": {"aws:MultiFactorAuthPresent": "true"}}`
- MFA can be required for sensitive API calls (e.g., deleting S3 objects with MFA Delete)

---

## IAM Roles

IAM Roles provide **temporary credentials** via **AWS STS (Security Token Service)**.

### Common Use Cases

| Use Case | Role Type |
|---|---|
| EC2 instance accessing S3 | EC2 Instance Profile |
| Lambda reading DynamoDB | Lambda Execution Role |
| SageMaker training job | SageMaker Execution Role |
| Cross-account access | Role with trust policy for external account |
| Federated identity (SSO, SAML) | Identity Provider Role |

### Trust Policy vs. Permission Policy
- **Trust Policy** — defines *who* can assume the role (the principal)
- **Permission Policy** — defines *what* the role can do once assumed

### Why Roles Over Users?
- No long-lived credentials to manage or rotate
- Credentials automatically expire (15 min – 12 hours)
- Auditable via CloudTrail (`AssumeRole` events)

---

## Encryption 101

### Symmetric vs. Asymmetric Encryption

| | Symmetric | Asymmetric |
|---|---|---|
| Keys | Single shared key | Public key + Private key |
| Speed | Fast | Slow |
| Use case | Bulk data encryption (AES-256) | Key exchange, digital signatures (RSA, ECC) |
| AWS example | KMS symmetric CMK | KMS asymmetric CMK, ACM certificates |

### Encryption in AWS

- **Encryption at Rest** — data encrypted when stored (S3 SSE, EBS encryption, RDS encryption)
- **Encryption in Transit** — data encrypted while moving (TLS/HTTPS)
- **Client-side Encryption** — data encrypted before sending to AWS; AWS never sees plaintext
- **Server-side Encryption** — AWS handles encryption/decryption on your behalf

### Key Concepts
- **Envelope Encryption** — data is encrypted with a **Data Encryption Key (DEK)**; the DEK itself is encrypted with a **Key Encryption Key (KEK/CMK)** stored in KMS
- Only the encrypted DEK is stored alongside the data; plaintext DEK never persists

---

## AWS KMS (Key Management Service)

AWS KMS is a managed service for creating, managing, and using cryptographic keys.

### Key Types

| Type | Description |
|---|---|
| **AWS Managed Key** | Created and managed by AWS for a specific service (e.g., `aws/s3`) |
| **Customer Managed Key (CMK)** | Created by you; full control over rotation, deletion, policies |
| **Data Key** | Generated by KMS via `GenerateDataKey`; used for envelope encryption |

### Key Features
- Keys never leave KMS in plaintext
- Supports **automatic key rotation** (annual, for symmetric CMKs)
- **Key Policy** — resource-based policy defining who can use and manage the key
- **KMS Grants** — temporary, scoped permissions to use a key (useful for SageMaker, etc.)
- Integrated with most AWS services: S3, EBS, RDS, Secrets Manager, SageMaker

### KMS API Calls
- `Encrypt` / `Decrypt` — encrypt up to 4 KB directly
- `GenerateDataKey` — returns plaintext + encrypted DEK for envelope encryption
- `GenerateDataKeyWithoutPlaintext` — returns only the encrypted DEK (decrypt later)

### KMS in ML Context
- Encrypt SageMaker training data, model artifacts, and feature store data
- Use CMKs to maintain control and auditability over ML data encryption

---

## Amazon Macie

**Amazon Macie** is a data security service that uses ML to automatically discover, classify, and protect sensitive data in **Amazon S3**.

### What It Does
- Scans S3 buckets for **PII** (names, SSNs, credit cards, passport numbers, etc.)
- Identifies **misconfigured buckets** (publicly accessible, unencrypted)
- Generates **findings** that integrate with Security Hub and EventBridge

### Use Cases in ML
- Detect if training datasets contain PII before model training
- Enforce data governance in data lakes
- Trigger automated remediation via EventBridge → Lambda

---

## AWS Secrets Manager

**AWS Secrets Manager** stores, rotates, and manages secrets (DB passwords, API keys, OAuth tokens).

### Key Features
- **Automatic rotation** — integrates with RDS, Redshift, DocumentDB natively; custom Lambda for others
- Secrets encrypted with KMS
- Access controlled via IAM policies and resource-based policies
- Versioning: `AWSCURRENT`, `AWSPENDING`, `AWSPREVIOUS`

### vs. SSM Parameter Store

| | Secrets Manager | SSM Parameter Store |
|---|---|---|
| Cost | Paid per secret | Free (standard), paid (advanced) |
| Auto rotation | Native support | Manual (Lambda needed) |
| Cross-account | Yes | Limited |
| Best for | Credentials & secrets | Config values & parameters |

### ML Use Case
- Store database credentials for feature stores
- Store API keys for third-party ML services
- Retrieve secrets in SageMaker notebooks/containers without hardcoding

---

## AWS WAF (Web Application Firewall)

**AWS WAF** protects web applications from common exploits at **Layer 7 (HTTP/HTTPS)**.

### Key Concepts
- Deployed in front of: **CloudFront, ALB, API Gateway, AppSync**
- **Web ACL (Access Control List)** — a set of rules applied to incoming requests
- **Rules** can match on: IP address, HTTP headers, URI string, body content, query string
- **Rate-based rules** — block IPs exceeding a request threshold (DDoS/brute-force protection)
- **Managed Rule Groups** — pre-built rule sets (AWS, AWS Marketplace vendors)

### Common Rule Types
- SQL injection protection
- Cross-site scripting (XSS) protection
- Geo-blocking
- IP reputation lists

### ML Relevance
- Protect SageMaker inference endpoints exposed via API Gateway
- Block malicious payloads before they reach your model serving layer

---

## AWS Shield

**AWS Shield** provides DDoS (Distributed Denial of Service) protection.

| Tier | Cost | Coverage |
|---|---|---|
| **Shield Standard** | Free | Automatically applied to all AWS customers; protects against common Layer 3/4 attacks |
| **Shield Advanced** | ~$3,000/month | Enhanced protection for EC2, ELB, CloudFront, Route 53; 24/7 DRT support; cost protection |

- **DRT (DDoS Response Team)** — AWS experts available 24/7 with Shield Advanced
- **Shield Advanced** provides near real-time attack visibility and integrates with WAF

---

## VPC, Subnets, Internet Gateway, NAT Gateway

### VPC (Virtual Private Cloud)
- Logically isolated network within AWS
- You define CIDR block (e.g., `10.0.0.0/16`)
- Spans all AZs in a region

### Subnets
- Subdivision of a VPC, tied to a single AZ
- **Public Subnet** — has a route to an Internet Gateway; resources can have public IPs
- **Private Subnet** — no direct internet route; resources hidden from internet

### Internet Gateway (IGW)
- Allows resources in **public subnets** to communicate with the internet
- Horizontally scaled, highly available, no bandwidth constraints
- One IGW per VPC

### NAT Gateway
- Allows resources in **private subnets** to initiate outbound internet traffic (e.g., download packages)
- Deployed in a **public subnet**; routes traffic through IGW
- Managed by AWS (highly available within AZ); use one per AZ for HA
- **NAT Instance** — self-managed EC2 alternative (legacy, not recommended)

### ML Relevance
- SageMaker training instances in private subnets use NAT Gateway to pull from ECR/S3
- Keep training data and model artifacts in private subnets

---

## NACLs, Security Groups, VPC Flow Logs

### Security Groups (SG)
- **Stateful** — return traffic automatically allowed
- Applied at the **ENI/instance level**
- Only **Allow** rules (no explicit deny)
- Evaluated as a whole (all rules)

### Network ACLs (NACLs)
- **Stateless** — must explicitly allow return traffic in both directions
- Applied at the **subnet level**
- Support both **Allow** and **Deny** rules
- Rules evaluated in **number order** (lowest first); first match wins
- Default NACL allows all inbound and outbound traffic

### SG vs. NACL

| | Security Group | NACL |
|---|---|---|
| Level | Instance/ENI | Subnet |
| Stateful | Yes | No |
| Allow & Deny | Allow only | Both |
| Rule evaluation | All rules | In order |

### VPC Flow Logs
- Capture metadata about IP traffic to/from network interfaces in your VPC
- Published to **CloudWatch Logs** or **S3**
- Does NOT capture payload content — only metadata (src IP, dst IP, port, protocol, action, bytes)
- Useful for: security analysis, troubleshooting, compliance auditing

---

## VPC Peering, Endpoints, VPN, Direct Connect

### VPC Peering
- Private connectivity between two VPCs (same or different accounts/regions)
- Traffic stays on AWS network; no transitive peering (A↔B, B↔C ≠ A↔C)
- Non-overlapping CIDR blocks required

### VPC Endpoints
- Connect to AWS services **without traversing the internet**
- **Interface Endpoint (PrivateLink)** — creates an ENI in your subnet; supports most AWS services
- **Gateway Endpoint** — routes traffic via route table; supports **S3 and DynamoDB only**; free

### AWS VPN
- **Site-to-Site VPN** — encrypted IPSec tunnel between on-premises and AWS VPC
  - Uses **Virtual Private Gateway (VGW)** on AWS side, **Customer Gateway** on-prem side
- **Client VPN** — TLS-based VPN for individual user access to AWS resources
- Travels over the public internet (encrypted)

### AWS Direct Connect (DX)
- Dedicated **private physical connection** from on-premises to AWS
- More consistent latency and higher throughput than VPN
- Does NOT encrypt traffic by default (combine with VPN over DX for encryption)
- Use cases: large data transfers, latency-sensitive workloads, hybrid architectures

---

## AWS PrivateLink

**AWS PrivateLink** enables private connectivity to AWS services or your own services without exposing traffic to the public internet.

### How It Works
- Creates a **VPC Interface Endpoint** (ENI with private IP) in your VPC
- Traffic flows through AWS's private network — never leaves AWS
- You can expose your own service via a **Network Load Balancer** + PrivateLink to other VPCs/accounts

### vs. VPC Peering

| | PrivateLink | VPC Peering |
|---|---|---|
| Direction | One-way service consumption | Bidirectional full VPC access |
| CIDR overlap | Allowed | Not allowed |
| Transitive | N/A | Not supported |
| Use case | SaaS / service sharing | Full network connectivity |

### ML Relevance
- Use PrivateLink endpoints for SageMaker API, SageMaker Runtime, S3, ECR within a private VPC
- Ensures ML workloads never route traffic over the internet, meeting security and compliance requirements

---

## Key Security Services Summary

| Service | Purpose |
|---|---|
| **IAM** | Identity and access management |
| **KMS** | Encryption key management |
| **Secrets Manager** | Secrets storage and rotation |
| **Macie** | Sensitive data discovery in S3 |
| **WAF** | Layer 7 web application firewall |
| **Shield** | DDoS protection |
| **CloudTrail** | API audit logging |
| **VPC + PrivateLink** | Network isolation and private connectivity |
| **Security Hub** | Centralised security findings aggregation |
