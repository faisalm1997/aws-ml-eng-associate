# MLOps

MLOps is a set of practices that combines Machine Learning (ML) and DevOps to automate and streamline the process of deploying, monitoring, and maintaining ML models in production. It focuses on collaboration between data scientists, ML engineers, and operations teams to ensure that ML models are reliable, scalable, and continuously improved.

## Implement safeguards + shadow tests for deploynent

Deployment guardrails are used for async or real time inference endpoints. These guardrails controls shifting traffic to new models such as 'blue and green' deployments. 

Canary deployments: shift a small portion of traffic and monitor
Linear deployments: shift traffic in linear spaced steps

Shadow tests: these compare performance of shadow variant to production, users can monitor in sagemaker console and decide when to promote the code.

## ML implementations and operations

Sagemaker + docker: All models in sagemaker are hosted in docker containers. Pre built deep learning, scikit learn and spark ML containers can be used for training and inference code. This allows users to use any script or algorithm within sagemaker regardless of runtime or language.

Containers are isolated and contain all dependencies and resources needed to run.

Docker containers are created from images, images are built from a dockerfile, images are saved in a repository. 

Environment variables can be set based on sagemaker functionality within the dockerfile.

Production variants: you can test multiple models on live traffic using production variants, variant weights tell sagemaker how to distribute traffic among the models. Once the user is confident in the models performance, user can ramp is up 100%. 

Production variants lets the user perform A/B tests which validates the performance of the model in real world settings.

## Sagemaker on the edge

Sagemaker NEO: train the model once, run them anywhere. Models can be run on edge devices such as your car, users can optimise code for specific devices. Consist of a compiler and a runtime.

Neo + IoT Greengrass: neo compiled models can be deployed using a HTTPS endpoint, the same instance type should be used for both compilation and deployment. Users can deploy to IoT greengrass, which brings the model to the edge and uses lambda inference applications.

## Managing sagemaker resources

Choosing instance types: In general, algorithms which rely on deep learning will benefit from GPU instances, inference usually use compute instances and GPU instances can be pricey. 

Managed spot instances: users can use EC2 spot instances for training and save up to 90% on demand instances. Spot instances can be interrupted, so users can use checkpoints to s3 so training can resume.

Auto scaling: users can setup a scaling policy to define target metrics, min/max capacity with cooldown periods. Works with cloudwatch and dynamically adjusts number of instances based on production variant.

Availability zones: Sagemaker automatically attempts to distribute instances across AZs, users need multiple instances for each production endpoint to work. We should aim to configure VPCs with at least two subnets, each in a different AZ.

## Deploying models for inference

1. Sagemaker jumpstart: deploying pre trained models to pre configured endpoints
2. Modelbuilder from the sagemaker python SDK
3. AWS Cloudformation: AWS sagemaker model resources creates a model to host an endpoint.
4. Real time inference: for interactive workloads with low latency requirements
5. Sagemaker serverless inference: no management of infrastructure, ideal if workload has idle periods and uneven traffic over time
6. Async inference: queues requests and processes them in an async manner
7. Autoscaling: dynamically adjust compute resources for endpoints based on traffic.
8. Sagemaker Neo: optimises models for AWS inferentia chips

## Sagemaker Serverless inference and inference recommender

- Serverless inference: users specify container, memory and concurrency requirements and underlying capacity is auto provisioned and scaled. Charges based on usage.
- Sagemaker inference recommender: recommends the best instance type and configuration for your models. Automates load testing and model tuning and deploys to the most optimal inference endpoint.
    - Users register the model to the model registry and benchmark different endpoint configurations, collect and visualise metrics to decide on instance types.
    - Instance recomnendations: runs load tests on recommended instance types which takes about 45 mins
    - Endpoint recommendations: custom load test where you specify instances, traffic patterns, latency requirements, and takes around two hours

## Sagemaker inference pipelines

Inference pipelines are a sequence of 2-15 containers. Any combination of pre trained built in algorithms or your own algorithms in docker containers. Combine pre processing, predictions and post processing. Can handle both real time inference and batch transforms.

## Sagemaker model monitor

Sagemaker model monitor continuously monitors the quality of ML models in production. It automatically detects concept drift and data quality issues. Users can set up monitoring schedules, configure alerts, and take corrective actions when necessary.

Data is stored in s3 and secured, monitoring jobs are scheduled via a monitoring schedule and metrics are emitted to cloudwatch. 

### Model monitor data capture

Logs inputs to your endpoint and inference endpoints. Data delivered to S3 as JSON, this can be used for further training, debugging and monitoring. Automatically compares data metrics to your baseline and supports both real time and batch model monitor modes. Inference data may be encrypted.