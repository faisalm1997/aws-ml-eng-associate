# EMR 

Elastic map reduce which is managed hadoop framework on EC2 instances. Includes spark, HBase, Presto, Flink and Hive. EMR notebooks can be used for data transformation and processing. EMR also has several integration points with other AWS services.

## EMR Cluster

1. Master node: manages the cluster on a single EC2 instance
2. Core node: Hosts HDFS data and runs tasks which can be scaled up and down but with some risk. 
3. Task node: runs tasks which does not host data and has no risk of data loss when removing. Spot instances are a good use for task nodes.

## EMR Usage

Transient vs long running clusters: A user can spin up task nodes using spot instances for temp capacity, reserved instances can be used on long running clusters which can save $. EMR can be connected directly to master to run jobs and ordered steps can be submitted via the console. 

## EMR/AWS Integration

1. S3: EMR can read and write data directly to S3, which is often used for input and output data storage.
2. VPC: to configure the virtual network in which you can launch instances.
3. IAM: to manage permissions and access control for EMR resources.
4. CloudWatch: for monitoring and logging EMR cluster performance and health.

## EMR Storage

1. HDFS: the default storage system for EMR clusters, which is distributed across the cluster nodes.
2. EMRFS: Accessing S3 as if it were HDFS
3. EBS: Elastic Block Store can be used for additional storage on EMR instances.
4. Local Storage: Each EMR instance has local storage that can be used for temporary data processing.

## EMR Promises

1. Scalability: EMR can easily scale up or down based on the workload allowing for efficient resource utilisation.
2. Cost-effectiveness: EMR allows users to pay only for the resources they use, and can leverage spot instances for cost savings. Charged by the hour.
3. Flexibility: EMR supports a wide range of big data processing frameworks and can be customised to meet specific needs.
4. EMR can provision new nodes if the core node fails. 

## EMR Serverless

EMR Serverless allows users to run big data applications without having to manage the underlying infrastructure. It automatically provisions and scales resources as needed, allowing users to focus on their applications rather than cluster management. This can be particularly beneficial for workloads with variable or unpredictable demand, as it can help optimize costs and improve efficiency.

Users can choose an EMR release and runtime (Spark, Hive, Presto). A user can create, start, stop and terminate the EMR serverless application lifecycle by calling APIs.

### Pre Initialised Capacity 

Spark jobs add 10% overhead to memory requests for drivers and executors so users have to be sure that the initial capacity of the EMR cluster is 10% more than the job requested.

### EMR Serverless Security

EMRFS has S3 encryption or CSE at rest. TLS in transit between EMR nodes and S3. Local disk encryption and spark is used for communication between drivers and executors. HTTPS/TLS on S3 policies with aws:SecureTransport.

### How does spark work?

Driver program has a spark context. A cluster manager (spark, YARN) is initiated. Executor is initiated which has a cache and runs tasks. The components with spark core; spark streaming, spark SQL, MLLib and GraphX.

Spark MLLib is used for ML jobs; classification, regression, decision trees, recommendation engine, clustering (k-means), LDA. 

# Feature Engineering

Feature engineering is applying your knowledge of the data, and the model you're using to create better features to train your model with.

1. Which features should I use?
2. Do I need to transform these features in some way? 
3. How do I handle missing data?
4. Should I create new features from the existing ones?

Applied ML is basically feature engineering.

## Curse of dimensionality

Too many features can be a problem, which leads to sparse data. Every feature is a new dimension.

Feature engineering is selecting the features most relevant to the problem at hand. This is often where domain knowledge comes into play. Unsupervised dimensionality reduction techniques can be used to distill many features into fewer features eg. PCA, K-means

## Inputting missing data

### Mean Replacement
A column represents a single feature, and it only makes sense to take the mean from other samples of the same feature. Mean replacement doesnt affect the mean or sample size of the overall data set.

Using the median maybe a better choice than the mean when outliers are present, but it only works on column level and cant be used on categorical features making it not very accurate.

### Dropping
Dropping rows with missing data can lead to loss of information and bias if the missing data is not random. Dropping columns with missing data can also lead to loss of important features.

### Machine Learning 

Using machine learning algorithms to predict missing values can be more accurate than mean replacement or dropping, but it can also be more complex and time-consuming. It requires selecting an appropriate algorithm and tuning it for the specific dataset.

KNN: Find K nearest similar rows and average values. Assumes numerical data but not categorical. 

Deep Learning: Build a ML model to impute data for your ML model, work well with categorical data but its complicated.

Regression: Finding linear or non linear relationships between the missing feature and other features. The most advanced technique associated with regression is MICE (Multiple imputation by chained equations).

Note: Getting hold of more data is better than imputing data. 

## Unbalanced data

Unbalanced data can lead to biased models that perform poorly on the minority class. It is mainly a problem with neural networks Techniques to address unbalanced data include:

1. Oversampling: duplicate samples from the minority class. Can be done at random.
2. Undersampling: Instead of creating more positive cases, remove negative ones. Do not throw data away.
3. SMOTE: Synthetic minority over sampling technique which artificially generates new samples of the minority class using nearest neighbours. Both generates new samples and undersamples majority class. This technique is generally better than oversampling.
4. Adjusting thresholds: when making predictions about a classification, there is some sort of threshold of probability at which point the case is flagged as positive. If there are too many false positives then you can simply increase the threshold.

## Handling Outliers

Variance measures how 'spread out' the data is. Variance is simply the average of the squared differences from the mean.

Standard deviation or sd is the square root of the variance.

Its appropriate to remove outliers from the training data. AWS has the random cut forest algorithm which can deal with outlier detection. Can be found in quicksight, sagemaker, kinesis analytics and more.

### Binning

Binning is a technique for converting continuous data into categorical data. It can be used to reduce the impact of outliers and to create more interpretable models. Binning can be done using equal-width bins, equal-frequency bins, or custom bins based on domain knowledge. It is especially useful when it comes to uncertainty in measurements.

### Transforming

Transforming is the application of some function to a feature to make it better suited for training e.g. feature data with an exponential trend may benefit from a logarithmic transform.

### Encoding

Encoding is the process of converting categorical data into numerical data. This is necessary because most machine learning algorithms require numerical input. Common encoding techniques include one-hot encoding, label encoding, and target encoding.

### Scaling/Normalisation

Scaling is the process of transforming features to a common scale. This is important because many machine learning algorithms are sensitive to the scale of the input data. Common scaling techniques include min-max scaling, standardization, and robust scaling. SCIKIT Learn has a preprocessor module which can be used for scaling and normalisation. Most ML models require some sort of feature data to at least be scaled to comparable values.

### Shuffling

Shuffling is the process of randomly rearranging the order of the data. This is important because many machine learning algorithms assume that the data is independent and identically distributed (i.i.d.). Shuffling can help to break any correlations between the features and the target variable, which can improve the performance of the model.

# Sagemaker AI

## Overview

Sagemaker is built to handle the full ML workflow from deploying models to cleaning/preparing data to training and evaluating models. Sagemaker deploys models through an endpoint, s3 works in conjuction with sagemaker to provide and store training data but also hold model artifacts. ECR holds the inference and training code images. Sagemaker notebooks can provide users with an interface to gain access to s3 data, build modela and deploy trained models via EC2. 

## AI Domains

Domains organise the users, apps and resources

1. All domains share an EFS volume
2. User profiles have their own personal apps
3. Shared spaces can be used to share EFS directory

By default, a domain has two VPC's. One for internet access which is managed by sagemaker AI and one for encrypted traffic to the EFS volume which is managed by the user. Users are able route all traffic to their own VPC.

## Data prep on Sagemaker

Sagemaker data wrangling is a visual interface to prepare data for training. It has built in transformations and allows users to write their own transformations in python or R. It can be used to clean, transform and explore data before training a model.

Data usually comes from S3, the ideal format varies with the algorithm often RecordIO, Protobuf. Can also ingest data from Athena, EMR, Redshift etc. Apache Spark can integrate with sagemaker as well as scikit learn and numpy for transformations.

1. Sagemaker processing: copy data from s3, spin up a processing container which can be built in or user provided, output processed data to s3. 
2. Training on sagemaker: create a training job using an s3 url with training data, provide url of s3 bucket with output and an ECR path to the training code. There are a bunch of training options such as spark MLLib, tensorflow, PyTorch, custom built docker image or an algorithm purchased from the marketplace.
3. Deploying trained models: users can save trained model to s3, using a persistent endpoint to make predictions on demand or sagemaker batch transform to get predictions for an entire dataset. Sagemaker also has other options for complex processing, elastic inference and auto scaling, shadow testing to evaluate new models against existing models.

## Sagemaker ground truth 

Ground truth is a data labelling service which can be used to create labelled datasets for training. It can be used for image, video, text and tabular data. Ground truth can be used to create custom labelling workflows and can also leverage pre-built workflows for common use cases. Ground truth can also be used to create synthetic data for training.

Ground truth can create its own model as images, as the model learns, only the images which the model is unsure about is sent to human labelers. This can reduce the cost of labeling jobs by 70%. Rekognition and comprehend can also be used to generate labels for training.

Ground truth plus: this is a turnkey solution which is managed by a team of AWS experts, a user fills in an intake form and can track progress via the plus project portal. After the data has been labeled, it is sent via s3.

### Amazon Mechnical Turk

Mechanical Turk is a marketplace for human intelligence tasks. It can be used to create custom labelling workflows and can also leverage pre-built workflows for common use cases. Mechanical Turk can be used to create synthetic data for training. A crowdsourcing marketplace to perform simple human tasks - a distributed virtual workforce.

## Sagemaker Data Wrangler

Data wrangler is a visual interface to prepare data for training. It has built in transformations and allows users to write their own transformations in python or R. It can be used to clean, transform and explore data before training a model. There are 300+ transformations to choose from or integrate with pandas, pyspark and pyspark SQL. 'Quick model' can be used to train your model with your data and measure its results.

Steps: Import data -- preview data -- visualise data -- transform data -- quick model -- export data flow

## Sagemaker model monitor

Model monitor is a service which can be used to monitor the performance of deployed models. It can be used to detect data drift, model drift and bias in the model. Model monitor can also be used to create alerts and notifications when certain thresholds are met. There is no code needed for this feature, can detect new features and anomalies/outliers. Monitoring jobs are scheduled via a monitoring schedule and metrics are emitted to cloudwatch.

There are several monitoring types which include: drift in data quality, drift in model quality, bias drift and feature attribution drift.

Model monitor can integrated with sagemaker clarify - sagemaker clarify detects potential bias ie imbalances across groups, ages and income brackets. Clarify can also help explain model behaviour and understand which features contribute most to your predictions.

There are several bias metrics in sagemaker clarify:
1. Class Imbalance: one facet or demographic group has fewer training values than the other
2. Differences in proportions of labels DPL: imbalance in positive outcomes between facet values
3. Kullback-leiber divergence KL, Jehnsen-shannon divergence JS: how much outcome distributions of facets diverge
4. Lp-norm: p-norm difference between distributions of outcomes from facets
5. Total variation distance TVD: L1-norm difference between distributions of outcomes from facets
6. Kolmogorov-Smirnov KS: Max divergence between outcomes in distributions from facets
7. Conditional demographic disparity CDD: disparity of outcomes between facets as a whole, and by subgroups

### Partial Dependence Plots

Partial dependence plots show the relationship between a feature and the predicted outcome of a model. Plots can show you how feature values influence predictions. You can also get back data distributions for each bucket of values.

### Shapley Values

Shapley values are the algorithm used to determine the contribution of each feature toward a models prediction. It basically measurss the impact of dropping individual features but can get complicated when there are alot of features. Assymetric shapley values can be used for time series and the algorithm is used to determine the contribution of input features at each time step towards forecasted predictions.

## Sagemaker feature store

A feature in this context is a property which is used to train a ML model. ML models require fast, secure access to feature data for training and it can also be a challenge to keep the features organised, and share features across different models. Where the features come from is up to the user, a feature store has several feature groups which each have a record identifier, feature name and event time.

Data ingestion can be in both streaming or batch forms into the feature store, streaming access can be via Putrecord and Getrecord APIs with Batch access via offline S3 store. 

The feature store has security with data being encrypted at rest and at transit, works with KMS customer master keys and fine grain access control with IAM. Can also be secured with privatelink.

## Sagemaker Canvas

No code ML for business analysts, can upload csv data and select a column to predict, build and make predictions. Users can also join datasets, allows classification or regression and auto cleans data for missing values, dupes, and outliers. Shares models and datasets with sagemaker studio. Canvas also has generative AI support via bedrock or jumpstart foundational models.

----- finish off aws glue, glue databrew, athena sections.