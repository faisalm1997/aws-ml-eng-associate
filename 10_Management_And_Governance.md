# 10. Management and Governance

> **Exam Domain:** ML Implementation and Operations (approx. 20% of exam)

## 🎯 Key Exam Topics
- CloudWatch metrics: namespaces, dimensions, retention, custom metrics
- Key SageMaker endpoint metrics: `Invocations`, `ModelLatency`, `InvocationErrors`
- CloudWatch Logs: log groups, log streams, log insights queries, metric filters
- CloudWatch Alarms: threshold-based alerting, states (OK, ALARM, INSUFFICIENT_DATA)
- CloudWatch Unified Agent: collects memory, disk, and process metrics not available by default
- AWS CloudTrail: API call logging for compliance and security auditing
- AWS Config: resource configuration change tracking and compliance rules
- Cost Explorer and AWS Budgets for cost management

## ⚠️ Common Exam Traps
- **Memory utilisation is NOT available as a default EC2 CloudWatch metric** — requires the CloudWatch Unified Agent
- **CloudTrail** records API calls (who, what, when, from where); **CloudWatch** records metrics and logs — do not confuse them
- **CloudWatch Alarms** are for threshold-based alerting; **Model Monitor** is for ML-specific drift detection
- **Log Insights** is an interactive query tool for CloudWatch Logs — not the same as Kinesis or Athena
- SSM Parameter Store can centrally store the CloudWatch Unified Agent config for fleet-wide deployment

---

## Intro: Management and Governance

AWS management and governance services provide visibility, control, and optimisation across your AWS environment. Key concerns include:

- **Monitoring & Observability** — understanding what is happening in your infrastructure
- **Auditing & Compliance** — recording who did what, when
- **Cost Management** — tracking and controlling AWS spend
- **Configuration Management** — ensuring resources are configured correctly and consistently

---

## Amazon CloudWatch – Metrics

**Amazon CloudWatch** is the primary monitoring and observability service in AWS.

### Metrics
- A **metric** is a time-series of data points representing a measurable value (e.g., CPU usage, request count)
- Metrics are organised by **Namespace** (e.g., `AWS/EC2`, `AWS/SageMaker`) and identified by **Dimensions** (key-value pairs such as `InstanceId`)
- Default resolution: **1 minute**; high-resolution custom metrics: **1 second**
- Metrics are retained for varying periods depending on resolution (up to 15 months)

### Key AWS Metrics by Service

| Service | Example Metrics |
|---|---|
| EC2 | `CPUUtilization`, `NetworkIn`, `DiskReadOps` |
| SageMaker Training | `train:loss`, `validation:accuracy` |
| SageMaker Endpoint | `Invocations`, `ModelLatency`, `InvocationErrors` |
| S3 | `NumberOfObjects`, `BucketSizeBytes` |
| Lambda | `Invocations`, `Errors`, `Duration`, `ConcurrentExecutions` |

### Custom Metrics
- Publish your own metrics using `PutMetricData` API or the CloudWatch Agent
- Useful for application-level metrics (e.g., model prediction latency, data pipeline throughput)

---

## Amazon CloudWatch – Logs

CloudWatch Logs centralises log collection, storage, and analysis from AWS services and applications.

### Core Concepts

| Concept | Description |
|---|---|
| **Log Group** | Container for log streams; typically one per application or service |
| **Log Stream** | Sequence of log events from a single source (e.g., one EC2 instance) |
| **Log Event** | A single timestamped log entry |
| **Retention Policy** | How long logs are kept (1 day to indefinite); default is indefinite |
| **Metric Filter** | Extract metrics from log data using pattern matching |
| **Subscription Filter** | Stream log data in real time to Kinesis, Lambda, or OpenSearch |

### Key Sources
- **EC2 / on-premises** — via CloudWatch Agent
- **Lambda** — automatically streams logs to CloudWatch
- **ECS / EKS** — via log driver configuration
- **SageMaker** — training job and endpoint logs streamed automatically
- **VPC Flow Logs**, **CloudTrail**, **Route 53** — natively integrated

### Log Insights
- Interactive query language for analysing log data
- Use cases: error analysis, latency percentiles, top-N queries
- Example query:
  ```
  fields @timestamp, @message
  | filter @message like /ERROR/
  | sort @timestamp desc
  | limit 20
  ```

---

## Amazon CloudWatch – Logs Unified Agent

The **CloudWatch Unified Agent** is installed on EC2 or on-premises servers to collect both logs and system-level metrics.

### What It Collects
- **Logs** — any log file on the filesystem (application logs, system logs, custom logs)
- **System Metrics** — metrics not available by default in CloudWatch:
  - `cpu` (per-core utilisation)
  - `mem` (memory usage — not natively available from EC2 hypervisor)
  - `disk` (disk usage, I/O)
  - `netstat` (TCP connections, packets)
  - `processes`
  - `swap`

### Configuration
- Configured via a single JSON config file (`amazon-cloudwatch-agent.json`)
- Can be stored centrally in **SSM Parameter Store** for fleet-wide deployment
- Replaces the older CloudWatch Logs Agent and CloudWatch Monitoring Scripts

---

## Amazon CloudWatch – Alarms

CloudWatch Alarms trigger actions based on metric thresholds.

### Alarm States

| State | Description |
|---|---|
| `OK` | Metric is within the defined threshold |
| `ALARM` | Metric has breached the threshold |
| `INSUFFICIENT_DATA` | Not enough data to evaluate the alarm |

### Alarm Actions
- **SNS notification** — send an email, SMS, or trigger a downstream service
- **EC2 action** — stop, terminate, reboot, or recover an instance
- **Auto Scaling action** — scale in or out
- **Systems Manager action** — run an automation document

### Composite Alarms
- Combine multiple alarms using AND/OR logic
- Reduces alarm noise by requiring multiple conditions to be true simultaneously

### Key Settings
- **Period** — how long to evaluate the metric (e.g., 5 minutes)
- **Evaluation Periods** — how many consecutive periods must breach the threshold
- **Datapoints to Alarm** — how many of the evaluation periods must be in breach (M out of N)

### ML Relevance
- Alert on SageMaker endpoint `ModelLatency` exceeding SLA thresholds
- Alert on training job failures via `TrainingJobStatus` or CloudWatch Logs metric filters

---

## AWS X-Ray

**AWS X-Ray** provides distributed tracing and end-to-end visibility into application requests.

### Core Concepts

| Concept | Description |
|---|---|
| **Trace** | End-to-end record of a single request as it flows through your system |
| **Segment** | A unit of work done by a single service (e.g., one Lambda invocation) |
| **Subsegment** | A finer-grained breakdown within a segment (e.g., a DynamoDB call) |
| **Annotation** | Key-value pairs indexed for filtering (e.g., `user_id`, `model_version`) |
| **Metadata** | Additional non-indexed data attached to a segment |
| **Service Map** | Visual representation of service dependencies and health |

### How It Works
1. Instrument your application using the **X-Ray SDK** (available for Java, Python, Node.js, .NET, Go, Ruby)
2. SDK sends **trace data** to the **X-Ray daemon** (runs as a sidecar/agent)
3. Daemon batches and forwards data to the X-Ray service
4. X-Ray builds a **service map** and allows trace analysis

### Key Features
- Identify latency bottlenecks and error sources across microservices
- Filter traces by annotations, HTTP status, latency, user, and more
- **Groups** — filter expressions to create subsets of traces
- **Sampling** — configurable rules to control what percentage of requests are traced (reduces cost)

### Integrations
- Lambda, API Gateway, EC2, ECS, Elastic Beanstalk, SageMaker, SNS, SQS

### ML Relevance
- Trace inference requests through API Gateway → Lambda → SageMaker endpoint
- Identify where latency is introduced in an ML serving pipeline

---

## Overview of Amazon QuickSight

> **Note:** Amazon QuickSight is being rebranded as **Amazon Q in QuickSight** / **Amazon Quick Suite**.

**Amazon QuickSight** is a serverless, cloud-native business intelligence (BI) service for creating interactive dashboards and visualisations.

### Key Features
- **SPICE** (Super-fast Parallel In-memory Calculation Engine) — in-memory data engine for fast queries
- Auto-scales to thousands of concurrent users with no infrastructure to manage
- **ML Insights** — built-in anomaly detection, forecasting, and narrative auto-generation powered by ML
- Pay-per-session pricing (authors vs. readers)

### Data Sources
- AWS: S3, Athena, Redshift, RDS, Aurora, OpenSearch, Timestream
- External: Salesforce, Jira, on-premises databases via connectors
- Files: CSV, JSON, Excel, Parquet

### Use Cases in ML
- Visualise model evaluation metrics and experiment results
- Build dashboards on top of Athena queries over SageMaker Feature Store or S3 data lake
- Monitor data quality and feature distributions over time

---

## Types of Visualisations and When to Use Them

Choosing the right visualisation type is critical for communicating insights effectively.

### Common Chart Types

| Visualisation | Best Used For | Example |
|---|---|---|
| **Bar Chart** | Comparing categories | Model accuracy by algorithm |
| **Line Chart** | Trends over time | Training loss per epoch |
| **Scatter Plot** | Correlation between two variables | Feature A vs. Feature B |
| **Histogram** | Distribution of a single variable | Distribution of prediction scores |
| **Heat Map** | Correlation matrix or density | Feature correlation matrix |
| **Pie / Donut Chart** | Part-to-whole (use sparingly, max 5–6 slices) | Class distribution |
| **Box Plot** | Distribution, median, outliers | Model latency distribution across endpoints |
| **Geospatial Map** | Geographic data | Sales by region |
| **KPI / Gauge** | Single metric vs. target | Current model accuracy vs. baseline |
| **Pivot Table** | Multi-dimensional aggregation | Metrics broken down by model and dataset |
| **Waterfall Chart** | Cumulative effect of sequential values | SHAP feature contributions |
| **Funnel Chart** | Sequential stage drop-off | ML pipeline step completion rates |

### Design Principles
- Match the chart type to the data relationship (comparison, distribution, composition, relationship)
- Avoid 3D charts — they distort perception
- Use colour purposefully; ensure accessibility (colour-blind friendly palettes)
- Keep dashboards focused: fewer charts with clear titles are more effective than dense layouts

---

## Amazon CloudTrail

**AWS CloudTrail** records all API calls made in your AWS account — who did what, where, and when.

### What It Captures
- Every API call: via the console, CLI, SDKs, or AWS services acting on your behalf
- Includes: caller identity (IAM user/role), source IP, timestamp, request parameters, response

### Event Types

| Type | Description |
|---|---|
| **Management Events** | Control plane operations (create, delete, modify resources) — enabled by default |
| **Data Events** | Object-level operations (S3 GetObject, PutObject; Lambda invocations) — not enabled by default |
| **Insights Events** | Detect unusual API activity (e.g., spike in `TerminateInstances` calls) |

### Key Features
- **Trail** — configuration to deliver events to an S3 bucket and/or CloudWatch Logs
- Events stored in S3 can be queried with **Athena**
- CloudTrail logs are integrity-validated using SHA-256 hashing
- **Multi-region trails** — capture activity across all regions in one trail
- **Organisation trail** — centralise CloudTrail logs across all accounts in an AWS Organisation

### ML Relevance
- Audit who invoked SageMaker training jobs, endpoint deployments, and model approvals
- Detect unintended access to training data in S3
- Required for compliance frameworks (SOC 2, HIPAA, PCI-DSS)

---

## AWS Config

**AWS Config** continuously monitors and records the configuration of AWS resources and evaluates them against desired rules.

### Core Concepts

| Concept | Description |
|---|---|
| **Configuration Item** | Point-in-time snapshot of a resource's configuration |
| **Configuration History** | Timeline of all changes to a resource |
| **Configuration Snapshot** | Full snapshot of all recorded resources at a point in time |
| **Config Rule** | AWS-managed or custom rule defining desired resource configuration |
| **Conformance Pack** | Collection of Config rules packaged together (e.g., CIS Benchmark) |
| **Remediation Action** | Automated fix triggered when a resource is non-compliant |

### Example Rules
- `s3-bucket-public-read-prohibited` — S3 buckets must not allow public read access
- `encrypted-volumes` — EBS volumes must be encrypted
- `iam-password-policy` — IAM password policy must meet minimum requirements
- `sagemaker-endpoint-config-kms-key-configured` — SageMaker endpoints must use a KMS key

### Key Points
- AWS Config does **not prevent** changes — it records and evaluates them
- Integrates with **CloudTrail** to correlate what changed and who changed it
- Non-compliant findings can trigger **SSM Automation** or **Lambda** for auto-remediation
- Works across regions and accounts via **Aggregators**

---

## CloudWatch vs. CloudTrail vs. Config

| | CloudWatch | CloudTrail | Config |
|---|---|---|---|
| **Purpose** | Monitor performance & logs | Audit API activity | Track resource configuration |
| **Focus** | Metrics, logs, alarms | Who did what, when | What does this resource look like? |
| **Data Type** | Time-series metrics, log events | API call records | Configuration state snapshots |
| **Use Case** | Operational monitoring, alerting | Security auditing, compliance | Change management, compliance |
| **Retention** | Configurable (metrics up to 15 months) | 90 days (default); S3 indefinite | Indefinite (in S3) |
| **Key Question** | "Is my system healthy?" | "Who changed this resource?" | "Is this resource configured correctly?" |

---

## AWS Budgets

**AWS Budgets** allows you to set custom cost and usage thresholds and receive alerts when they are breached.

### Budget Types

| Type | Description |
|---|---|
| **Cost Budget** | Alert when actual or forecasted spend exceeds a threshold |
| **Usage Budget** | Alert on service usage (e.g., EC2 hours, S3 storage) |
| **Reservation Budget** | Track RI/Savings Plans utilisation and coverage |
| **Savings Plans Budget** | Monitor Savings Plans coverage and utilisation |

### Key Features
- **Forecasted alerts** — warn you before you actually exceed a budget
- Notifications via **SNS** or **email**
- Up to 5 SNS topics and 10 email addresses per budget alert
- **Budget Actions** — automatically apply IAM policies or SCP to restrict spending when a threshold is breached
- First two budgets per month are free; $0.02/day per additional budget

### ML Relevance
- Set budgets per SageMaker service to control training and inference costs
- Use tagging to create budgets scoped to a specific ML project or team

---

## AWS Cost Explorer

**AWS Cost Explorer** provides interactive visualisations and analysis of your AWS costs and usage over time.

### Key Features
- View cost breakdowns by service, account, region, tag, or usage type
- **Forecasting** — projects future costs based on historical patterns (up to 12 months)
- **Savings Plans recommendations** — suggests optimal Savings Plans based on your usage
- **Reserved Instance recommendations** — identifies opportunities to reduce costs with RIs
- Hourly and resource-level granularity available (with additional cost)
- Export reports to S3 for further analysis

### vs. AWS Budgets

| | Cost Explorer | AWS Budgets |
|---|---|---|
| Purpose | Analyse and understand past/current spend | Set forward-looking alerts and limits |
| Direction | Retrospective + forecast | Prospective alerts |
| Automation | No automated actions | Budget Actions can restrict usage |

---

## AWS Trusted Advisor

**AWS Trusted Advisor** inspects your AWS environment and provides real-time recommendations across five categories.

### Five Check Categories

| Category | Examples |
|---|---|
| **Cost Optimisation** | Idle EC2 instances, underutilised RIs, unassociated Elastic IPs |
| **Performance** | High-utilisation EC2 instances, CloudFront configuration issues |
| **Security** | Open security groups, MFA not enabled on root, public S3 buckets |
| **Fault Tolerance** | RDS without Multi-AZ, EBS without snapshots, low Route 53 TTLs |
| **Service Limits** | Resources approaching AWS service quotas |

### Support Plan Tiers
- **Basic / Developer** — access to 7 core checks only (key security + service limits)
- **Business / Enterprise** — full access to all ~500+ checks + programmatic access via AWS Support API

### Key Points
- Trusted Advisor checks are **not real-time continuous** — they refresh periodically (some within 5 minutes with Business/Enterprise support)
- Integrate with **CloudWatch Events / EventBridge** to alert on Trusted Advisor findings
- Use the **AWS Support API** to automate remediation based on Trusted Advisor results

---

## Key Management and Governance Services Summary

| Service | Purpose |
|---|---|
| **CloudWatch** | Metrics, logs, alarms, and dashboards for operational monitoring |
| **X-Ray** | Distributed tracing for request-level visibility |
| **QuickSight** | Serverless BI and visualisation |
| **CloudTrail** | API audit trail — who did what |
| **Config** | Resource configuration history and compliance rules |
| **Budgets** | Cost and usage alerts with optional automated actions |
| **Cost Explorer** | Cost analysis, forecasting, and savings recommendations |
| **Trusted Advisor** | Best-practice recommendations across cost, security, and performance |

---

## 📋 Module 10 — Quick Summary

| Topic | Must-Know Fact |
|---|---|
| CloudWatch metrics | Time-series data; 1-min default resolution; 1-sec high resolution |
| Memory utilisation | NOT a default EC2 metric; requires CloudWatch Unified Agent |
| CloudWatch Alarms | Threshold-based; states: OK, ALARM, INSUFFICIENT_DATA |
| CloudWatch Logs Insights | Interactive query language for log analysis |
| CloudWatch Unified Agent | Collects logs + system metrics (memory, disk, process) |
| CloudTrail | Records all AWS API calls; who, what, when, from where |
| AWS Config | Tracks resource configuration changes; evaluates compliance rules |
| Cost Explorer | Visualise and analyse AWS spending over time |
| AWS Budgets | Set spend or usage budgets with alerts |
| SSM Parameter Store | Centralised config storage; used for Unified Agent config |
