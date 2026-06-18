# EMR

Elastic MapReduce is AWS-managed Hadoop on EC2. It includes Spark, HBase, Presto, Flink, and Hive. EMR Notebooks can be used for data transformation and processing, and EMR integrates with several other AWS services.

## EMR Cluster

1. Master node: manages the cluster on a single EC2 instance.
2. Core node: stores HDFS data and runs tasks; scaling it down can risk data loss.
3. Task node: runs tasks only, does not store data, and can be removed without data loss. Spot instances are a good fit here.

## EMR Usage

- Transient clusters are useful for temporary workloads.
- Long-running clusters can use reserved instances to reduce cost.
- EMR jobs can be submitted directly to the master node, and ordered steps can be submitted through the console.

## EMR / AWS Integration

1. S3: read and write data directly to S3 for input and output storage.
2. VPC: define the virtual network where instances launch.
3. IAM: manage permissions and access control for EMR resources.
4. CloudWatch: monitor and log cluster performance and health.

## EMR Storage

1. HDFS: default distributed storage for EMR clusters.
2. EMRFS: access S3 as if it were HDFS.
3. EBS: add extra storage to EMR instances.
4. Local storage: temporary storage on each instance for processing.

## EMR Benefits

1. Scalability: scale up or down based on workload.
2. Cost-effectiveness: pay for what you use and save with Spot Instances.
3. Flexibility: supports many big data processing frameworks.
4. Fault tolerance: can provision new nodes if a core node fails.

## EMR Serverless

EMR Serverless runs big data applications without managing underlying infrastructure. It automatically provisions and scales resources so you can focus on the application rather than cluster management. This is useful for variable or unpredictable demand.

You can choose an EMR release and runtime such as Spark, Hive, or Presto, and manage the application lifecycle through APIs.

### Pre-initialized Capacity

Spark jobs add about 10% overhead to memory requests for drivers and executors, so initial capacity should be about 10% higher than the job request.

### EMR Serverless Security

- EMRFS supports S3 encryption or client-side encryption at rest.
- TLS is used in transit between EMR nodes and S3.
- Local disk encryption can be used.
- Spark communication between drivers and executors is encrypted.
- S3 policies can enforce HTTPS with `aws:SecureTransport`.

### How Spark Works

- The driver program contains the Spark context.
- A cluster manager such as Spark or YARN is started.
- Executors run tasks and maintain a cache.
- Spark core is extended by Spark Streaming, Spark SQL, MLlib, and GraphX.

Spark MLlib is used for machine learning jobs such as classification, regression, decision trees, recommendation engines, clustering such as k-means, and LDA.

# Feature Engineering

Feature engineering is the process of using your understanding of the data and model to create better features for training.

1. Which features should I use?
2. Do I need to transform these features?
3. How do I handle missing data?
4. Should I create new features from existing ones?

Applied ML is largely feature engineering.

## Curse of Dimensionality

Too many features can create sparse data because every feature adds a new dimension. Feature engineering is about selecting the most relevant features for the problem, often using domain knowledge. Unsupervised dimensionality reduction methods such as PCA or k-means can help compress many features into fewer ones.

## Handling Missing Data

### Mean Replacement

- A column represents a single feature, so mean replacement should be done within that feature.
- Mean replacement does not change the overall sample size.
- Median can be a better choice when outliers are present.
- This works only on numerical columns, not categorical ones.

### Dropping

- Dropping rows with missing values can remove information and introduce bias if the missingness is not random.
- Dropping columns with missing values can also remove important features.

### Machine Learning Imputation

Using ML to predict missing values can be more accurate than mean replacement or dropping, but it is more complex and time-consuming. It requires choosing an algorithm and tuning it for the dataset.

- KNN: finds the nearest similar rows and averages values; works well with numerical data.
- Deep learning: can be used to impute values, including categorical data, but is more complex.
- Regression: models linear or non-linear relationships between the missing feature and the other features.
- MICE: multiple imputation by chained equations, an advanced regression-based approach.

Getting more data is usually better than imputing data.

## Unbalanced Data

Unbalanced data can create biased models that perform poorly on the minority class. It is especially problematic for neural networks.

1. Oversampling: duplicate minority-class samples.
2. Undersampling: remove majority-class samples instead of creating more positives.
3. SMOTE: synthetically generates new minority-class samples using nearest neighbours; generally better than simple oversampling.
4. Threshold adjustment: change the probability threshold used to flag a prediction as positive.

## Handling Outliers

Variance measures spread as the average of squared differences from the mean. Standard deviation is the square root of variance.

Removing outliers from training data is often appropriate. AWS Random Cut Forest can be used for outlier detection and is available in services such as QuickSight, SageMaker, and Kinesis Analytics.

### Binning

Binning converts continuous data into categorical data. It can reduce the impact of outliers and make models easier to interpret. Common approaches include equal-width bins, equal-frequency bins, and custom bins based on domain knowledge.

### Transforming

Transforming applies a function to a feature to make it more suitable for training. For example, data with an exponential trend may benefit from a logarithmic transform.

### Encoding

Encoding converts categorical data into numerical data. Common techniques include one-hot encoding, label encoding, and target encoding.

### Scaling / Normalisation

Scaling transforms features to a common scale so algorithms that are sensitive to magnitude behave better. Common approaches include min-max scaling, standardisation, and robust scaling. Scikit-learn provides preprocessing tools for this.

### Shuffling

Shuffling randomly rearranges the data order. This helps when algorithms assume independent and identically distributed samples because it breaks unwanted correlations.

# SageMaker AI

## Overview

SageMaker covers the full ML workflow, including preparing data, training, evaluating, and deploying models. Models are deployed through endpoints. S3 stores training data and model artifacts, ECR stores training and inference container images, and SageMaker notebooks provide an interface to access S3 data, build models, and deploy trained models.

## AI Domains

Domains organize users, apps, and resources.

1. All domains share an EFS volume.
2. User profiles have their own personal apps.
3. Shared spaces can be used to share an EFS directory.

By default, a domain has two VPCs: one for internet access managed by SageMaker AI, and one for encrypted traffic to the EFS volume managed by the user. Users can route all traffic through their own VPC.

## Data Prep on SageMaker

SageMaker Data Wrangler is a visual interface for preparing data for training. It includes built-in transformations and allows custom transformations in Python or R. It can be used to clean, transform, and explore data before training.

Data usually comes from S3. The ideal format depends on the algorithm, often RecordIO or Protobuf. It can also ingest data from Athena, EMR, Redshift, and other sources. Apache Spark can integrate with SageMaker, along with scikit-learn and NumPy, for transformations.

1. SageMaker Processing: copy data from S3, spin up a processing container, and output processed data back to S3.
2. Training on SageMaker: create a training job using an S3 URL for training data, provide the output S3 bucket, and point to the ECR path for training code. Options include Spark MLlib, TensorFlow, PyTorch, custom Docker images, or marketplace algorithms.
3. Deploying trained models: save the model to S3, use a persistent endpoint for on-demand predictions, or use SageMaker Batch Transform for batch predictions. SageMaker also supports complex processing, Elastic Inference, auto scaling, and shadow testing.

## SageMaker Ground Truth

Ground Truth is a data labeling service for creating labeled training datasets for image, video, text, and tabular data. It supports custom labeling workflows, pre-built workflows, and synthetic data generation.

Ground Truth can use a model to route only uncertain images to human labelers, which can reduce labeling cost by up to 70%. Rekognition and Comprehend can also be used to generate labels.

Ground Truth Plus is a managed service where AWS experts handle the labeling workflow. You submit an intake form, track progress in the project portal, and receive the labeled data through S3.

### Amazon Mechanical Turk

Mechanical Turk is a marketplace for human intelligence tasks. It can support custom or pre-built labeling workflows and is essentially a distributed virtual workforce for simple human tasks.

## SageMaker Data Wrangler

Data Wrangler is a visual interface for preparing data for training. It offers 300+ transformations and integrates with pandas, PySpark, and PySpark SQL. The Quick Model feature can train a model on the data and measure its results.

Workflow: import data -> preview data -> visualise data -> transform data -> quick model -> export data flow.

## SageMaker Model Monitor

Model Monitor monitors deployed model performance and can detect data drift, model drift, and bias. It can also create alerts and notifications when thresholds are crossed. No code is required. Monitoring jobs run on a schedule and emit metrics to CloudWatch.

Monitoring types include data quality drift, model quality drift, bias drift, and feature attribution drift.

Model Monitor integrates with SageMaker Clarify, which detects potential bias such as imbalances across groups, ages, and income brackets. Clarify also helps explain model behaviour and identify which features contribute most to predictions.

Bias metrics in Clarify include:

1. Class imbalance: one demographic group has fewer training samples than another.
2. Difference in proportions of labels (DPL): imbalance in positive outcomes between facet values.
3. Kullback-Leibler divergence and Jensen-Shannon divergence: how much outcome distributions diverge.
4. Lp-norm: p-norm difference between outcome distributions.
5. Total variation distance (TVD): L1-norm difference between outcome distributions.
6. Kolmogorov-Smirnov (KS): maximum divergence between outcome distributions.
7. Conditional demographic disparity (CDD): disparity of outcomes between facets overall and by subgroup.

### Partial Dependence Plots

Partial dependence plots show the relationship between a feature and a model's predicted outcome. They help show how feature values influence predictions and can also show the data distribution for each bucket of values.

### Shapley Values

Shapley values measure the contribution of each feature to a model prediction. They estimate the impact of dropping individual features and become more complex as the number of features grows. Asymmetric Shapley values can be used for time series to estimate the contribution of input features at each time step to forecasted predictions.

## SageMaker Feature Store

A feature is a property used to train a machine learning model. Feature Store provides fast, secure access to feature data for training and helps keep features organized and reusable across models. Features are grouped into feature groups, each with a record identifier, feature name, and event time.

Data ingestion can happen in streaming or batch mode. Streaming access uses PutRecord and GetRecord APIs, while batch access uses an offline S3 store.

Feature Store supports encryption at rest and in transit, works with KMS customer managed keys, and supports fine-grained access control with IAM. It can also be secured with PrivateLink.

## SageMaker Canvas

Canvas is no-code ML for business analysts. Users can upload CSV data, choose a target column, build predictions, and join datasets. It supports classification and regression, automatically cleans missing values, duplicates, and outliers, and shares models and datasets with SageMaker Studio. Canvas also supports generative AI through Bedrock or JumpStart foundation models.

## AWS Glue

AWS Glue is a serverless data integration and ETL service. It is commonly used to discover, catalog, clean, transform, and move data between sources such as S3, databases, and analytics tools.

Key Glue concepts:

1. Glue Data Catalog: central metadata store for tables, schemas, partitions, and connections.
2. Crawlers: scan data sources and automatically infer table definitions and schema updates.
3. Jobs: serverless ETL tasks that run Spark-based transformations.
4. Triggers and workflows: orchestrate Glue jobs and dependent steps.
5. Glue Studio: visual interface for building ETL pipelines.

Glue is useful for ML pipelines because it can prepare raw data, create curated datasets, and register them in the catalog so tools like Athena and Lake Formation can query them.

## AWS Glue DataBrew

AWS Glue DataBrew is a visual, no-code data preparation tool for analysts and data engineers. It is designed to clean and normalize data without writing code.

Common uses include:

1. Profiling data to find missing values, duplicates, and anomalies.
2. Applying pre-built transformations through reusable recipes.
3. Cleaning and standardising data before loading it into S3, Athena, or downstream ML workflows.
4. Running batch jobs to apply the same recipe to new datasets.

DataBrew is useful when you want interactive data cleaning before sending data into training pipelines or analytics workflows.

## Amazon Athena

Amazon Athena is a serverless query service that lets you run SQL directly on data stored in S3.

Important points:

1. Athena uses standard SQL for ad hoc querying.
2. It integrates with the Glue Data Catalog for metadata and table definitions.
3. It works best with partitioned data and columnar formats such as Parquet or ORC.
4. It is useful for quick analysis, feature exploration, and preparing datasets without provisioning servers.
5. It can query common formats such as CSV, JSON, Avro, Parquet, and ORC.

For ML workflows, Athena is often used to inspect raw data, validate feature sets, and build SQL-based preprocessing steps before moving data into training pipelines.
