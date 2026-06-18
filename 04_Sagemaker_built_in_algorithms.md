# Sagemaker Built In Algorithms

## Introducing Sagemaker

Sagemaker is built to handle the full ML workflow, from preparing and cleaning data to training, evaluating, and deploying models in production.

See section 02 for how Sagemaker uses S3 and ECR for training and deployment.

## Sagemaker Input Modes

There are several input modes in Sagemaker for ingesting training data.

1. S3 file mode: the default mode; copies training data from S3 into a local directory in a Docker container.
2. S3 fast file mode: similar to pipe mode; training can begin before the data is fully downloaded and works best with sequential access.
3. Pipe mode: streams data directly from S3.
4. Amazon S3 Express One Zone: high-performance storage in a single AZ; works with file, fast file, and pipe modes.
5. Amazon FSx for Lustre: high-throughput, low-latency storage for large workloads; runs in a single AZ and requires a VPC.
6. Amazon EFS: requires data to already be in EFS and also requires a VPC.

## Linear Learner in Sagemaker

Linear Learner is used for regression and classification by fitting a linear model to training data.

It can:

1. Handle both regression and classification.
2. Use a linear threshold function for classification.
3. Work with binary and multiclass problems.

Linear Learner preprocesses data by normalising features so they have similar weight. Training data is shuffled, then optimized with stochastic gradient descent, and multiple models can be trained in parallel before the best one is selected through validation.

Training input:

- Prefers RecordIO-wrapped protobuf float32 data.
- Can also use CSV, where the first column is treated as the label.
- Supports file mode and pipe mode.

Instance types:

- Works on single or multi CPU/GPU instances.
- Multi-GPU usually does not add much benefit.

### Linear Learner Hyperparameters

1. Balance_multiclass_weights: gives each class equal importance in the loss function.
2. Learning_rate and mini_batch_size.
3. L1: regularisation.
4. Wd: weight decay, which is L2 regularisation.
5. target_precision: holds precision at a target value while maximising recall.
6. target_recall: holds recall at a target value while maximising precision.

## XGBoost in Sagemaker

XGBoost is a decision-tree-based ensemble algorithm that uses gradient boosting. It is popular for structured data and can be used for regression and classification.

Training input:

- Supports CSV and libSVM.
- Has also been extended to support RecordIO-Protobuf and Parquet.

XGBoost can be used as a framework in notebooks or as a built-in SageMaker algorithm.

### XGBoost Hyperparameters

1. Subsample: helps prevent overfitting.
2. Eta: learning rate or step-size shrinkage.
3. Gamma: minimum loss reduction needed to create a partition.
4. Alpha: L1 regularisation term.
5. Lambda: L2 regularisation term.
6. eval_metric: common choices include AUC, error, and RMSE.
7. scale_pos_weight: adjusts the balance of positive and negative weights for imbalanced data.
8. max_depth: maximum tree depth; too high can lead to overfitting.

Instance types:

- Memory-bound instances are a better fit than compute-bound ones.
- M5 is a good choice.
- Single-instance GPU training is available.

## LightGBM in Sagemaker

LightGBM is a gradient-boosted decision tree algorithm used for classification, regression, and ranking.

Training input:

- Uses text or CSV for training and inference.
- Supports training and optional validation channels.

### LightGBM Hyperparameters

1. Learning_rate.
2. Num_leaves: maximum number of leaves per tree.
3. Feature_fraction: subset of features used per tree.
4. Bagging_fraction: similar to feature_fraction, but sampled randomly.
5. Bagging_freq: how often bagging is performed.
6. Max_depth.
7. Min_data_in_leaf: minimum data needed in one leaf; helps reduce overfitting.

Instance types:

- Often uses CPU instances.
- General purpose or compute-optimised instances can both work.
- M5 is a good option when enough memory is available.

## Seq2Seq in Sagemaker

Seq2Seq maps a sequence of input tokens to a sequence of output tokens. It is used for machine translation, text summarisation, and speech-to-text.

Training input:

- Uses RecordIO-Protobuf.
- Tokens must be integers.
- Requires training data, validation data, and vocabulary files.

Instance types:

- Typically uses GPU instances such as P3.
- Training is usually done on a single machine.

### Seq2Seq Hyperparameters

1. Batch_size.
2. Optimizer_type: for example adam, sgd, or rmsprop.
3. Learning_rate.
4. Num_layers_encoder and Num_layers_decoder.

## DeepAR in Sagemaker

DeepAR is used for forecasting one-dimensional time series data. It uses RNNs and can train a single model across many related time series.

It is useful for:

1. Detecting seasonality.
2. Identifying frequency patterns.
3. Training with many related time series at once.

Training input:

- Supports JSON, gzip, and Parquet.
- Each record must contain the starting timestamp and the time series values.
- Categorical features and dynamic features are supported.

### DeepAR Hyperparameters

1. Context_length: number of time points seen before making a prediction.
2. Epochs.
3. Mini_batch_size.
4. Learning_rate.
5. Num_cells.

Instance types:

- Can run on CPU or GPU instances.
- Can be single-machine or multi-machine.
- A common approach is to start on CPU and move to GPU if needed.
- Larger instances may be needed for tuning, while CPU is often enough for inference.

## BlazingText in Sagemaker

BlazingText is used for text classification and Word2Vec embeddings.

It is useful for:

1. Text classification.
2. Predicting labels for a sentence.
3. Word embeddings where semantically similar words have nearby vectors.

Training input:

- Supervised mode uses one sentence per line.
- Each label is prefixed with `__label__`.
- Augmented manifest text format is also supported.
- Word2Vec uses one training sentence per line.

### BlazingText Hyperparameters

1. Word2Vec:
   - Mode: batch_skipgram, skipgram, or cbow.
   - Learning_rate.
   - Window_size.
   - Vector_dim.
   - Negative_samples.
2. Text classification:
   - Epochs.
   - Learning_rate.
   - Word_ngrams.
   - Vector_dim.

Instance types:

- For cbow and skipgram, a single `ml.p3.2xlarge` is recommended.
- For batch_skipgram, multiple CPU instances can be used.
- For text classification, C5 instances are recommended when training data is under 2 GB.

## Object2Vec in Sagemaker

Object2Vec creates low-dimensional embeddings for high-dimensional objects and is useful for recommendations and clustering.

It works by:

1. Processing data into JSON lines.
2. Shuffling the inputs.
3. Training with two input channels, two encoders, and a comparator.
4. Passing the comparator output through a feed-forward neural network.

Training input:

- Data must be tokenized into integers.
- Training data must contain pairs of tokens or sequences of tokens.

### Object2Vec Hyperparameters

1. Deep learning settings such as dropout, early stopping, epochs, learning rate, batch size, layers, activation function, optimizer, and weight decay.
2. Enc1_network and Enc2_network.

Instance types:

- Trains on a single machine CPU or GPU.
- Multi-GPU is supported.
- Common instance types include `ml.m5.2xlarge` and `ml.p2.xlarge`.
- For inference, `ml.p3.2xlarge` is used with `inference_preferred_mode`.

## Object Detection in Sagemaker

Object detection identifies objects in images with bounding boxes and confidence scores.

It can:

1. Detect and classify objects in a single deep neural network.
2. Train from scratch or use pretrained models based on ImageNet.
3. Use either MXNet or TensorFlow implementations.

MXNet uses a CNN with a single-shot multibox detector and augmentation such as flip, rescale, and jitter. TensorFlow uses models such as ResNet, EfficientNet, and MobileNet from the TensorFlow model garden.

Training input:

- MXNet uses RecordIO or JPG/PNG images.
- A JSON file with annotation data is required.

### Object Detection Hyperparameters

1. mini_batch_size.
2. Learning_rate.
3. Optimizer: sgd, adam, rmsprop, or adadelta.

Instance types:

- Training uses GPU-only instances.
- Multi-GPU and multi-machine are supported.
- CPU or GPU can be used for inference.

## Image Classification in Sagemaker

Image classification assigns one or more labels to an image, but it does not identify the objects inside the image.

Implementation notes:

- MXNet supports full training mode with random weights.
- MXNet also supports transfer learning with pretrained weights.
- TensorFlow uses models from TensorFlow Hub.

### Image Classification Hyperparameters

1. Batch_size.
2. Learning_rate.
3. Optimizer.
4. Weight_decay, beta1, beta2, eps, and gamma.

Instance types:

- GPU is used for training.
- CPU or GPU can be used for inference.

## Semantic Segmentation in Sagemaker

Semantic segmentation performs pixel-level classification and produces a segmentation mask.

It differs from:

1. Image classification, which labels whole images.
2. Object detection, which labels bounding boxes.

Training input:

- JPG and PNG images with annotations.
- Label maps are used to describe annotations.
- Augmented manifest image format is supported for pipe mode.

The algorithm is built on MXNet Gluon and GluonCV, and supported models include fully convolutional network, pyramid scene parsing, and DeepLabv3.

### Semantic Segmentation Hyperparameters

1. Epochs, learning_rate, batch_size, and optimizer.
2. Algorithm.
3. Backbone.

Instance types:

- Training requires GPU on a single machine.
- Inference can use CPU or GPU.

## Random Cut Forest in Sagemaker

Random Cut Forest is used for anomaly detection and is effective for unexpected spikes in time series data.

It works by:

1. Sampling the data randomly.
2. Building a forest of trees where each tree partitions the training data.
3. Measuring the expected change in tree complexity when a point is added.
4. Assigning an anomaly score to each data point.

Training input:

- Uses RecordIO-Protobuf or CSV.
- Supports file mode and pipe mode.

### Random Cut Forest Hyperparameters

1. Num_trees: more trees reduce noise.
2. Num_samples_per_tree: should be chosen so that the inverse is roughly the ratio of anomalous to normal data.

Instance types:

- Does not benefit from GPUs.
- Use M4, C4, or C5 for training.
- Use `ml.c5.xl` for inference.

## Neural Topic Model in Sagemaker

Neural Topic Model is used to organize documents into topics or classify and summarise them based on topic structure.

It uses unsupervised learning and neural variational inference.

Training input:

- Expects four data channels.
- Train is required; validation, test, and auxiliary are optional.
- Uses RecordIO-Protobuf or CSV.
- Words must be tokenized into integers.
- Supports file mode and pipe mode.

### Neural Topic Model Hyperparameters

1. Lowering mini_batch_size and learning_rate can reduce validation loss, but increases training time.
2. Num_topics.

Instance types:

- GPU is recommended for training.
- CPU is cheaper for inference.

## Latent Dirichlet Allocation in Sagemaker

LDA is another topic modelling algorithm and is not deep learning.

It can be used for:

1. Topic discovery.
2. Document clustering.
3. Grouping customers by purchase patterns or similar behaviour.

Training input:

- Uses RecordIO-Protobuf or CSV.
- Each document contains counts for every word in the vocabulary.
- Only pipe mode is supported.

### LDA Hyperparameters

1. Num_topics.
2. Alpha0: smaller values create sparse topic mixtures; values above 1.0 produce more uniform mixtures.

Instance types:

- Single-instance CPU training is used.

## K-Nearest Neighbours in Sagemaker

KNN is a simple classification or regression algorithm that finds the closest K points to a sample and returns the most frequent label or average value.

The workflow is:

1. Train on the data.
2. Build an index to look up neighbours.
3. Query the model for the nearest K points.

Training input:

- Uses RecordIO-Protobuf or CSV.
- The first column is used as the label.
- Supports file mode and pipe mode.

### KNN Hyperparameters

1. K.
2. Sample_size.

Instance types:

- `ml.m5.2xlarge` and `ml.p2.xlarge` are commonly used for training.
- CPU is preferred for lower-latency inference.
- GPU can help with higher throughput on larger batches.

## K-Means Clustering in Sagemaker

K-means is an unsupervised clustering algorithm that divides data into K groups so that members of each group are as similar as possible.

Training input:

- Uses RecordIO-Protobuf or CSV.
- Supports file mode and pipe mode.

### K-Means Hyperparameters

1. K: choosing K is difficult, so it is common to inspect the within-cluster sum of squares across values of K.
2. Mini_batch_size.
3. Extra_center_factor.
4. Init_method.

Instance types:

- CPU or GPU can be used.
- CPU is usually recommended.
- `ml.g4dn.large` is a common GPU option.

## Principal Component Analysis in Sagemaker

PCA is used for dimensionality reduction by projecting high-dimensional data into fewer dimensions while preserving as much information as possible.

It works by:

1. Building a covariance matrix.
2. Applying singular value decomposition.
3. Producing components ordered by explained variance.

There are two modes:

1. Regular mode: good for sparse data and a moderate number of observations.
2. Randomized mode: good for large numbers of observations and features.

Training input:

- Uses RecordIO-Protobuf or CSV.
- Supports file mode and pipe mode.

### PCA Hyperparameters

1. Algorithm_mode.
2. Subtract_mean: centers the data.

Instance types:

- CPU or GPU can be used depending on the input data and workload.

## Factorization Machines in Sagemaker

Factorization Machines are used for sparse data and are common in recommendation systems and click prediction.

They are useful when:

1. Most users interact with only a small subset of items.
2. Pairwise feature interactions matter.
3. You want a supervised model for classification or regression.

Training input:

- Uses RecordIO-Protobuf with Float32.
- CSV is not practical for sparse data in this case.

### Factorization Machines Hyperparameters

1. Initialisation methods for bias, factors, and linear terms.
2. Uniform, normal, or constant initialisation.
3. Additional method-specific tuning options.

Instance types:

- CPU is recommended.
- GPU can also work, especially with denser data.

## IP Insights in Sagemaker

IP Insights is an unsupervised learning service for discovering IP address usage patterns and suspicious behaviour.

It can help identify:

1. Logins from anomalous IP addresses.
2. Accounts creating resources from anomalous IP addresses.

It uses a neural network to learn latent vector representations of entities and IP addresses. Entities are hashed and embedded, so large hash sizes are needed when there are many entities.

Training also includes negative sampling by randomly pairing entities and IPs.

Training input:

- CSV only.
- Can include usernames and account IDs directly.
- No extra preprocessing is required beyond the CSV format.

### IP Insights Hyperparameters

1. Num_entity_vectors: hash size; often set to about twice the number of unique entity identifiers.
2. Vector_dim: size of the embedding vectors; too large can cause overfitting.
3. Epochs, learning_rate, batch_size, and related training settings.

Instance types:

- CPU or GPU can be used.
- GPU is recommended, including multiple GPUs for larger workloads.
