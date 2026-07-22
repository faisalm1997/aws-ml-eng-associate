# 08. MLOps on AWS

> **Exam Domain:** ML Implementation and Operations (approx. 20% of exam)

## 🎯 Key Exam Topics
- Deployment strategies: blue/green, canary, linear, shadow test — differences and rollback behaviour
- Production variants for A/B testing
- Spot Instances + checkpointing for cost-effective training
- Inference options: real-time, serverless, async, batch transform — and when to use each
- SageMaker Model Monitor: data quality drift, model quality drift, bias drift
- SageMaker Neo for edge deployment + IoT Greengrass
- Auto scaling with `SageMakerVariantInvocationsPerInstance`
- Inference Pipeline (chaining 2–15 containers)
- SageMaker Inference Recommender

## ⚠️ Common Exam Traps
- **Shadow tests do NOT return responses to users** — they compare model behaviour in the background
- **Canary and linear deployments support automatic rollback via CloudWatch alarms; shadow tests do not**
- **Batch transform = no persistent endpoint** — most cost-effective for offline large-scale inference
- **Serverless inference has cold starts** — not appropriate for strictly low-latency requirements
- **Spot Instance training requires checkpointing to S3** — without it, an interruption means starting from epoch 0
- **SageMaker Neo requires the same instance type for compilation and deployment**
- Inference Recommender needs the model registered in **SageMaker Model Registry** first

---

# MLOps on AWS

MLOps combines Machine Learning and DevOps practices to automate and streamline the deployment, monitoring, and maintenance of ML models in production. It bridges the gap between data scientists and operations teams to ensure models are reliable, scalable, and continuously improved.

## Deployment Safeguards and Shadow Tests

Deployment guardrails control how traffic is shifted to a new model version on asynchronous or real-time inference endpoints. They are essential for safe, low-risk model rollouts.

- **Blue/Green deployments**: traffic is shifted from the old (blue) environment to the new (green) environment
- **Canary deployments**: a small percentage of traffic is routed to the new model first; if metrics are healthy, the rollout continues
- **Linear deployments**: traffic shifts in equal, evenly spaced steps (e.g. 10% every 5 minutes) until fully migrated
- **Shadow tests**: the new model (shadow variant) receives a copy of live traffic but its responses are not returned to users; performance is compared against production in the SageMaker console before any promotion decision is made

Key exam point: deployment guardrails support automatic rollback if a defined CloudWatch alarm is triggered during the traffic shift.

## SageMaker and Docker

All models in SageMaker are hosted in Docker containers, which provide consistent, isolated environments across training and inference.

- Pre-built containers are available for deep learning frameworks (TensorFlow, PyTorch), scikit-learn, and Spark ML
- Custom containers can be used for any algorithm or runtime not covered by the pre-built options
- Docker images are built from a Dockerfile, stored in a registry (e.g. Amazon ECR), and instantiated as containers at runtime
- SageMaker-specific environment variables (e.g. `SM_MODEL_DIR`, `SM_CHANNEL_TRAINING`) are injected into containers at runtime to locate input data and output paths

### Production Variants

Production variants allow multiple model versions to run simultaneously behind a single endpoint, enabling live traffic-based testing.

- Each variant is assigned a **variant weight** which determines the proportion of traffic it receives
- Supports **A/B testing**: compare model versions on real user traffic to validate performance before full rollout
- Once a variant is proven, its weight can be set to 100% and the old variant removed

## SageMaker on the Edge

**SageMaker Neo**: compile a model once and deploy it to a wide range of hardware targets with optimised performance.

- Consists of a compiler (optimises the model for the target device) and a runtime (executes the compiled model on the device)
- Supports cloud instances and edge devices (e.g. Jetson, Raspberry Pi, ARM-based hardware)
- The same instance type must be used for both compilation and deployment

**Neo + AWS IoT Greengrass**: extend model deployment to edge devices in the field.

- Neo-compiled models are packaged as Lambda inference components and deployed to IoT Greengrass devices
- Enables low-latency inference at the edge without a constant cloud connection

## Managing SageMaker Resources

### Choosing Instance Types

- Deep learning training: use GPU instances (e.g. `ml.p3`, `ml.g4dn`) for significantly faster computation
- Inference: compute-optimised instances (e.g. `ml.c5`) are typically sufficient and more cost-effective than GPU instances
- Match the instance family to the workload; GPU instances are expensive and should not be used where compute instances suffice

### Managed Spot Instances

- EC2 Spot Instances can be used for SageMaker training jobs, saving up to 90% compared to on-demand pricing
- Spot Instances can be interrupted; use **checkpointing to S3** so that training can resume from the last checkpoint rather than restarting from scratch
- Configure `MaxWaitTimeInSeconds` to set an upper bound on total wait + training time

### Auto Scaling

- Define a **scaling policy** with target metrics (e.g. `SageMakerVariantInvocationsPerInstance`), minimum/maximum instance counts, and cooldown periods
- Works with Amazon CloudWatch; SageMaker dynamically adjusts the number of instances serving a production variant based on traffic

### Availability Zones

- SageMaker automatically distributes instances across multiple AZs where possible
- A production endpoint should have at least two instances to remain available if one AZ has an outage
- Configure the VPC with at least two subnets, each in a different AZ, to support multi-AZ deployments

## Deploying Models for Inference

SageMaker supports several inference deployment options; choosing the right one is a common exam scenario:

| Option | Best for |
|---|---|
| **SageMaker JumpStart** | Deploying pre-trained foundation or built-in models with minimal configuration |
| **ModelBuilder (Python SDK)** | Programmatically building and deploying models with the SageMaker SDK |
| **AWS CloudFormation** | Provisioning SageMaker model and endpoint resources as infrastructure-as-code |
| **Real-time inference** | Low-latency, interactive workloads requiring synchronous responses |
| **Serverless inference** | Sporadic or unpredictable traffic; no infrastructure management; pay per invocation |
| **Async inference** | Large payloads or long processing times; requests are queued and results stored in S3 |
| **Batch transform** | Running inference on large datasets offline with no persistent endpoint |
| **Auto scaling** | Dynamically adjusting instance count for real-time endpoints based on traffic |
| **SageMaker Neo** | Optimising models for AWS Inferentia chips or edge hardware |

## SageMaker Serverless Inference and Inference Recommender

### Serverless Inference

- Specify the container image, memory size (1024–6144 MB), and maximum concurrency; AWS provisions and scales capacity automatically
- Billed per invocation and per GB-second of memory used; no charge when idle
- Best suited to workloads with unpredictable or intermittent traffic patterns
- Cold starts are possible; not suitable for strictly low-latency requirements

### SageMaker Inference Recommender

Automates benchmarking to find the optimal instance type and configuration for a model.

- Register the model in the **SageMaker Model Registry** before running benchmarks
- Two modes:
    - **Instance recommendations**: runs load tests across a set of recommended instance types; takes approximately 45 minutes
    - **Endpoint recommendations**: custom load test where you specify instance types, traffic patterns, and latency/throughput requirements; takes approximately 2 hours
- Outputs metrics (latency, throughput, cost) to help select the most cost-effective endpoint configuration

## SageMaker Inference Pipelines

An inference pipeline chains 2–15 containers into a single endpoint to handle multi-step inference logic.

- Containers execute sequentially: input passes through each container in order before a final response is returned
- Can combine pre-processing, model prediction, and post-processing steps in a single call
- Supports any mix of built-in SageMaker algorithms and custom Docker containers
- Works with both real-time inference endpoints and batch transform jobs

## SageMaker Model Monitor

SageMaker Model Monitor continuously tracks the quality of deployed ML models in production and alerts when issues are detected.

- Automatically detects **data quality drift** (statistical changes in input features), **model quality drift** (degrading prediction accuracy), **bias drift**, and **feature attribution drift**
- Monitoring jobs run on a schedule (e.g. hourly, daily) defined by a monitoring schedule resource
- Captured data and monitoring results are stored in S3; metrics are emitted to CloudWatch for alerting
- A **baseline** is established from the training dataset; Monitor compares live traffic statistics against this baseline

### Model Monitor Data Capture

- Logs both request inputs and inference outputs for endpoints in real time
- Captured data is delivered to S3 as JSON and can be used for retraining, auditing, and debugging
- Supports both real-time and batch inference monitoring modes
- Data in transit and at rest can be encrypted using KMS keys

## What is Docker?

Docker is a platform that packages applications and their dependencies into containers, ensuring consistent behaviour across environments. 

- Containers are lightweight and isolated, sharing the host OS kernel rather than requiring a full VM
- A Dockerfile defines the instructions to build an image; images are stored in a registry (e.g. Docker Hub or Amazon ECR)
- Containers are created from images and can be started, stopped, and scaled independently
- Key use case in ML: packaging model code, runtime dependencies, and configuration into a single portable unit that runs identically on a laptop, CI/CD pipeline, or production cluster

## Amazon ECS

Amazon Elastic Container Service (ECS) is a fully managed container orchestration service that runs Docker containers on AWS.

- Two launch types: EC2 (you manage the underlying instances) and Fargate (serverless, AWS manages the infrastructure)
- Core concepts:
    - Cluster: logical grouping of tasks or services
    - Task definition: blueprint describing which containers to run, CPU/memory, networking, and IAM roles
    - Task: a running instance of a task definition
    - Service: maintains a desired number of running tasks and can be paired with a load balancer
- Integrates with IAM for fine-grained permissions, CloudWatch for logging and metrics, and ECR for image storage
- Relevant for MLOps: used to run model serving containers, batch preprocessing jobs, or custom training pipelines

## Amazon ECR

Amazon Elastic Container Registry (ECR) is a fully managed Docker container registry for storing, managing, and deploying container images.

- Private registries are secured with IAM policies; no infrastructure to manage
- Images are stored in repositories and versioned with tags
- Integrates natively with ECS, EKS, and AWS CodePipeline for CI/CD workflows
- Supports image vulnerability scanning and lifecycle policies to automatically expire old images
- Used in MLOps to store versioned training and inference container images

## Amazon EKS

Amazon Elastic Kubernetes Service (EKS) is a managed Kubernetes service that removes the need to install and operate the Kubernetes control plane.

- Runs standard upstream Kubernetes, so existing tooling and manifests work without modification
- Supports EC2 node groups and AWS Fargate for serverless pods
- Integrates with IAM, VPC networking, CloudWatch, and ECR
- Preferred over ECS when teams already use Kubernetes or need portability across clouds
- In ML workloads: used to orchestrate distributed training jobs (e.g. with Kubeflow), model serving at scale, and feature engineering pipelines

## AWS Batch

AWS Batch is a fully managed service for running batch computing workloads at any scale. It dynamically provisions the optimal quantity and type of compute resources based on job requirements.

- Key concepts:
    - Job: unit of work (a shell script, Docker container, or executable)
    - Job definition: specifies how a job runs (container image, CPU, memory, IAM role)
    - Job queue: jobs are submitted to a queue and scheduled onto compute environments
    - Compute environment: managed or unmanaged set of EC2 or Fargate resources
- Automatically scales compute up and down; no cluster management required
- Supports EC2 Spot Instances for cost savings on fault-tolerant jobs
- ML use cases: large-scale data preprocessing, model evaluation across many parameter sets, or feature engineering pipelines that run on a schedule

## AWS CloudFormation

AWS CloudFormation is an infrastructure-as-code service that lets you model and provision AWS resources using YAML or JSON templates.

- Templates describe the desired state of resources; CloudFormation handles create, update, and delete operations
- Stacks: a collection of AWS resources managed as a single unit from a template
- Change sets: preview how proposed template changes will affect running resources before applying them
- Supports nested stacks for reusable modular templates
- Drift detection identifies when live resource configuration has diverged from the template
- MLOps use case: consistently provision SageMaker endpoints, S3 buckets, IAM roles, and VPCs across dev/staging/production environments

## AWS CDK

The AWS Cloud Development Kit (CDK) is an open-source framework that lets you define cloud infrastructure using familiar programming languages (Python, TypeScript, Java, etc.) which then synthesise into CloudFormation templates.

- Constructs are the basic building blocks: L1 (raw CloudFormation), L2 (opinionated defaults), L3 (higher-level patterns)
- `cdk synth` generates the CloudFormation template; `cdk deploy` deploys it
- Benefits over raw CloudFormation: loops, conditionals, type checking, and IDE autocompletion
- Stacks can be parameterised and reused across accounts and regions
- MLOps use case: programmatically define SageMaker pipelines, endpoints, and supporting infrastructure as versioned code alongside model code

## AWS CodeDeploy

AWS CodeDeploy is a fully managed deployment service that automates application deployments to EC2 instances, on-premises servers, Lambda functions, and ECS services.

- Supports in-place and blue/green deployment strategies
- Deployment configurations control the rate of traffic shifting: canary, linear, or all-at-once
- Hooks allow custom scripts to run at lifecycle events (e.g. before/after traffic is shifted) for validation
- Integrates with CodePipeline to form part of a CI/CD workflow
- MLOps use case: automate the rollout of updated model serving containers or Lambda-based inference functions with rollback on failure

## AWS CodeBuild

AWS CodeBuild is a fully managed continuous integration service that compiles source code, runs tests, and produces deployable artefacts.

- Builds run in ephemeral, fully managed containers; no build servers to manage
- Build instructions are defined in a `buildspec.yml` file at the repo root
- Supports custom Docker images for the build environment
- Scales automatically to handle concurrent builds
- MLOps use case: run unit tests on feature engineering code, build and push Docker images to ECR, or package model artefacts as part of a pipeline

## AWS CodePipeline

AWS CodePipeline is a fully managed continuous delivery service that automates the stages of a release pipeline.

- A pipeline consists of stages (e.g. Source → Build → Test → Deploy), each containing one or more actions
- Integrates with CodeCommit, GitHub, ECR as source providers; CodeBuild for build/test; CodeDeploy, ECS, CloudFormation, or SageMaker for deployment
- Supports manual approval actions before critical stages
- Automatically triggers on source changes (e.g. a git push)
- MLOps use case: end-to-end automation from code commit → container build → model training/evaluation → endpoint deployment

## Git Review: Architecture and Commands

Git is a distributed version control system. Each developer has a full copy of the repository history locally.

- Key areas: working directory → staging area (index) → local repository → remote repository
- Core commands:
    - `git init` / `git clone`: initialise or copy a repo
    - `git add`: stage changes
    - `git commit`: save staged changes to local history with a message
    - `git push` / `git pull`: sync with remote
    - `git branch` / `git checkout` / `git merge`: manage branches
    - `git rebase`: reapply commits on top of another branch for a linear history
    - `git stash`: temporarily shelve uncommitted changes
- Tags mark specific commits (e.g. `v1.0`) and are commonly used to identify model release versions

## Gitflow and GitHub Flow

Two common branching strategies for managing collaboration and releases.

**Gitflow**:
- Long-lived branches: `main` (production-ready) and `develop` (integration)
- Supporting branches: `feature/*`, `release/*`, `hotfix/*`
- Features branch off `develop` and merge back; releases branch off `develop`, are tested, then merged to both `main` and `develop`
- Suited to teams with scheduled release cycles

**GitHub Flow**:
- Single long-lived branch: `main`
- Short-lived feature branches are created, pull-requested, reviewed, and merged directly to `main`
- Simpler than Gitflow; suited to teams practising continuous delivery

## Amazon EventBridge

Amazon EventBridge is a serverless event bus that connects application components using events.

- Events are JSON objects produced by AWS services, SaaS providers, or custom applications
- Core concepts:
    - Event bus: receives events (default bus for AWS services; custom buses for your apps)
    - Rules: match incoming events using patterns and route them to one or more targets
    - Targets: services that process the event (Lambda, Step Functions, SQS, SNS, ECS tasks, SageMaker pipelines, etc.)
- Supports scheduled rules using cron or rate expressions (replaces CloudWatch Events)
- Schema registry: discovers and stores event schemas for use in code
- MLOps use case: trigger a SageMaker pipeline on new data arriving in S3, or route model monitoring alerts to an SNS topic for notification

## AWS Step Functions

AWS Step Functions is a serverless orchestration service that coordinates distributed application components as a series of steps in a visual workflow called a state machine.

- Workflows are defined in Amazon States Language (ASL), a JSON-based specification
- Supports two workflow types:
    - Standard: exactly-once execution, audit history retained for 90 days, suited to long-running workflows
    - Express: at-least-once, high-throughput, short-duration workloads
- Integrates with over 200 AWS services natively via SDK integrations
- Built-in error handling, retries, and parallel execution
- MLOps use case: orchestrate multi-step ML pipelines (data ingestion → preprocessing → training → evaluation → deployment) with branching logic based on model metrics

### State Machines and States

A state machine is a graph of states; execution starts at the designated start state and ends when a terminal state is reached.

- **Task**: executes a unit of work (Lambda, ECS task, SageMaker job, etc.)
- **Choice**: branches execution based on conditions
- **Parallel**: executes multiple branches simultaneously
- **Map**: iterates over an array and runs states for each item
- **Wait**: pauses execution for a fixed time or until a timestamp
- **Pass**: passes input to output, optionally injecting fixed data (useful for testing)
- **Succeed / Fail**: terminal states that end the execution successfully or with an error

## Amazon Managed Workflows for Apache Airflow (MWAA)

Amazon MWAA is a managed service for Apache Airflow that makes it easy to set up, operate, and scale workflow orchestration in the cloud.

- Airflow uses DAGs (Directed Acyclic Graphs) written in Python to define workflows and their dependencies
- AWS manages the Airflow infrastructure (scheduler, webserver, workers) and handles patching and scaling
- DAG files are stored in an S3 bucket; MWAA automatically syncs and deploys them
- Supports custom plugins and Python dependencies via S3
- Integrates with AWS services (Glue, EMR, SageMaker, Redshift) through Airflow providers/hooks
- Use case: complex, dependency-heavy data pipelines where teams are already familiar with Airflow or need rich scheduling and monitoring capabilities

## AWS Lake Formation

AWS Lake Formation is a service that makes it easy to set up, secure, and manage a data lake on Amazon S3.

- Simplifies the process of ingesting, cataloguing, cleaning, transforming, and securing data at scale
- Built on top of AWS Glue (data catalogue, crawlers, ETL jobs) and S3
- Key features:
    - **Blueprints**: pre-built workflow templates to ingest data from databases or log sources into the data lake
    - **Data Catalogue**: centralised metadata repository (Glue Data Catalog) for all data assets
    - **Fine-grained access control**: column-level, row-level, and cell-level security on top of IAM
    - **Cross-account sharing**: share catalogue resources and data with other AWS accounts
- Permissions model: Lake Formation permissions layer sits on top of IAM and S3 bucket policies
- ML use case: govern and share curated datasets for model training, ensuring data scientists only access the columns and rows they are authorised to see

## Lake Formation Data Filters

Data filters in Lake Formation provide fine-grained access control over data stored in the data lake.

- **Column-level security**: grant access to specific columns only, hiding sensitive attributes (e.g. PII fields) from certain principals
- **Row-level security**: filter rows based on values in a column, so a principal only sees rows relevant to them
- **Cell-level security**: combine row and column filters to restrict access to individual cells
- Filters are applied at query time via the Lake Formation permission engine when data is accessed through Athena, Redshift Spectrum, EMR, or Glue
- Filters are attached to tables in the Glue Data Catalog and assigned to IAM principals (users, roles)
- ML use case: ensure that model training jobs executed by different teams only read the subset of data they are permitted to use, without duplicating or masking datasets manually
---

## 📋 Module 08 — Quick Summary

| Topic | Must-Know Fact |
|---|---|
| Canary deployment | Small % to new model first; auto-rollback via CloudWatch alarm |
| Blue/green deployment | Full traffic shift; safe promotion |
| Shadow test | New model gets copy of traffic; responses NOT returned to users |
| Batch transform | Offline inference on large datasets; no persistent endpoint |
| Serverless inference | Sporadic traffic; cold starts; pay per invocation |
| Async inference | Large payloads or long processing; results stored in S3 |
| Spot Instances | Up to 90% savings; requires checkpointing to S3 |
| Model Monitor | Detects data quality drift, model quality drift, bias drift |
| Auto scaling | Uses `SageMakerVariantInvocationsPerInstance` metric |
| SageMaker Neo | Compile once; deploy to cloud or edge; same instance type for both |
| Inference Pipeline | Chain 2–15 containers in one endpoint |
| Inference Recommender | Benchmark instance types; needs model in Model Registry first |
| Production variants | Multiple model versions on one endpoint with traffic weights |
