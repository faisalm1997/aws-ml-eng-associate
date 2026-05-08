# Sagemaker Built in Algorithms

## Introducing Sagemaker

Sagemaker is built to handle the entire ML worklflow. From model deployment to evaluating results in production, fetching/cleaning and preparing data to training and evaluating a model.

See section 02 on how sagemaker deploy and trains models using S3 and ECR.

## Sagemaker Input Modes

There are several input modes in sagemaker for the ingress of data: 

1. S3 File mode: this is the default and copies training data from S3 to local directory in a docker container
2. S3 Fast file mode: This is akin to 'pipe mode' and training of the model can begin without waiting for the data to be downloaded. Works best with sequential access to data and not random access.
3. Pipe mode: streams data directly from S3, replaced by fast file. 
4. Amazon S3 express one zone: high performance storage class in one AZ, works with file, fast file and pipe modes.
5. Amazon FSx Lustre: scales to 100s of GBs of throughput and millions of IOPS with low latency, works in a single AZ and requires a VPC.
6. Amazon EFS: Requires data to be in EFS already and also requires a VPC.

## Linear Learner in Sagemaker

Linear learner is for understanding linear regression, through fitting a line to training data and also predictions based on a line. The linear learner can handle both regression and classification predictions. For classification, a linear threshold function is used, can do binary or mult-class.

Linear learner goes through preprocessing of data which is normnalised (all features have the same weight). The input data is shuffled and then training occurs using stochastic gradient descent, multiple models can be optimised in parallel. Then validation occurs where the most optimal model is selected.

Training input: Prefers RecordIO-wrapped protobuf float 32 data only, can use csv and will assume the first column to the label. Mode of delivery through file or pipe mode both supported.

Linear learner uses instance types which are single or multi CPU/GPU. Multi-GPU often doesn't help speed up the process or make the process more powerful.

### Imporant hyperparameters:

1. Balance_multiclass_weights: gives each class equal importance in loss functions
2. Learning_rate, mini_batch_size
3. L1: regularisation
4. Wd: Weight Decay (L2 regularisation)
5. target_precision: Holds precision at this value while maximising recall
6. target_recall: Holds recall at this value while maximising precision.

## XGBoost in Sagemaker

XGBoost is a decision-tree-based ensemble Machine Learning algorithm that uses a gradient boosting framework. It is designed for speed and performance, and it has become one of the most popular algorithms for structured data. XGBoost can be used for both regression and classification tasks.

Training input: XGBoost is not made for sagemaker since its open source. It takes csv or libsvm input data and has recently been extended to recordIO-Protobuf and parquet.

XGBoost starts with models being serialised/deserialised using pickle. Can be be used as a framework within notebooks, or as a bullt in sagemaker algorithm.

### Important hyperparameters: 

XGBoost has alot of parameters but some of these are important:

1. Subsample: prevents overfitting
2. Eta: step size shrinkage which prevents overfitting
3. Gamma: Minimum loss reduction to create a partition
4. Alpha: L1 regularisation term, larger alpha = more conservative
5. Lambda: L2 regularisation term, larger lambda = more conservative
6. eval_metric: optimise on AUC, errors, RMSE - if you care about false positives more than accuracy, you can use AUC
7. scale_post_weight: Adjusts balance of positive and negative weights, helpful for unbalanced classes
8. max_depth: Max depth of the tree, too high and you can get overfitting.

XGBoost can use memory boind instances, not compute bound. M5 is a good choice, single instance GPU training is available.

## LightGBM in Sagemaker

LightGBM is a gradient boosting decision tree which predicts variables within an ensemble of estimates from simpler models. Can be used for classification, regression and ranking. Requires text/csv for training and for inference. Training and optional validation channels can be provided.

### Important hyperparameters:

1. Learning_rate
2. Num_leaves: max number of leaves per tree
3. Feature_fraction: subset of features per tree
4. Bagging_fraction: similar to feature_fraxction but randmoly sampled
5. Bagging_freq: how often bagging is done
6. Max_depth
7. Min_data_in_leaf: min amount of data in one leaf, can address overfitting

LightGBM often uses single of multi instance CPU training, often uses general purpose over compute optimised. M5 is the better option and enough memory is always needed. 

## Seq2Seq in Sagemaker

Seq2seq is where the input is a sequence of tokens and output is a sequence of tokens. Used for machine translation, text summarisation, speech to text and implemented with RNNs and CNNs with attention.

Training input: RecordIO-Protobuf where tokens must be integers, start with tokenized text files and convert to protobuf using sample code. Must provide training data, validation data and vocab files for Seq2Seq to work.

Instance types which are used for Seq2Seq are GPU instance types e.g. P3, can only use a single machine for training. 

### Important hyperparameters:

1. Batch_size
2. Optimizer_type (adam, sgd, rmsprop)
3. Learning_rate
4. Num_layers_encoder, Num_layers_decoder

## DeepAR in Sagemaker

DeepAR is used for forecasting one dimensional time series data. Uses RNNs and allows you to train the same model over several related time series. It allows you to find frequencies and seasonality. Users can use the entire dataset as a test set, and remove the last time points for training. Don't use large values for prediction length (>400)

Training input: JSON, gzip or parquet. Each record must contsin the starting timestamp and the time series values. Categorical features and dynamic_features are welcome to be used. 

### Important hyperparameters:

1. Context_length: number of time points the model sees before making a prediction
2. Epochs
3. Mini_batch_size
4. Learning_rate
5. Num_cells

DeepAR can use CPU or GPU instance types, single or multi machine. Best is to start with CPU and then change to GPU is necessary. Larger instances maybe needed for tuning, CPU only for inferences.

## BlazingText in Sagemaker

BlazingText is used for text classification and predicting labels for a sentence, useful in web searches and information retrieval. Word2Vec creates a vector representation of words, semantically similar words are represented by vectors close to each other - this is called word embedding. Useful for NLP but blazingtext is not an NLP algorithm.

Training input: for supervised mode, there is one sentence per line and the first word in the sentence is the string __label__ followed by the label. Also augmented manifest text format is used for blazingtext. Word2Vec uses one text file with one training sentence per line. 

### Important hyperparameters:

1. Word2Vec:
    - Mode (batch_skipgram, skipgram, cbow)
    - Learning_rate
    - Window_size
    - Vector_dim
    - Negative_samples
2. Text classification:
    - Epochs
    - Learning_rate
    - Word_ngrams
    - Vector_dim

Instance types: For cbow and skipgram, it is recommended to use a single ml.p3.2xlarge, for batch_skipgram use multiple CPU instances. For text classification, C5 instance types are recommended if less than 2GB of training data.

## Object2Vec in Sagemaker

Object2Vec is used for objects and creating low dimensional dense embeddings of high dimensional objects. Generalised to handle things other than words. Compute the nearest neighbours of objects, visualise clusters and provide recommendations.

Object2Vec processes data into JSON lines and shuffles it. The model trains with two input channels, two encoders and a comparator. The comparator is followed by a feed forward neural network.

Training input: data must be tokenized into integers, and training data must consist of pairs of tokens and/or sequences of tokens.

### Important hyperparameters:

1. Deep Learning: dropout, early stopping, epochs, learning rate, batch size, layers, activation function, optimizer, weight decay
2. Enc1_network, Enc2_network 

Instance types: Can only train on a single machine CPU or GPU, multi GPU is okay. Most common instance types are: ml.m5.2xlarge, ml.p2.xlarge, if needed can go up to 4xlarge or 12xlarge. For inference, ml.p3.2xlarge is used with inference_preferred_mode.

## Object Detection in Sagemaker

Object detection is used for identifying objects in an image with bounding boxes. Detects and classifies objects with a single deep neural network. Classes are accompanied by confidence scores. Can train from scratch or use pre trained models based on Imagenet. There are two variants: MNXet and tensorflow, takes an image as an input and outputs all instances of objects in the image with categories and confidence scores. 

MXNet uses a CNN with single shot multibox detector algorithm and uses flip, rescale and jitter to avoid overfitting. Tensorflow uses ResNet, efficientNet, mobilenet models from the tensorflow model garden.

Training input: MXNet requires RecordIO or image in jpg, png - a JSON file with annotation data for each image needs to be supplied

### Important hyperparameters:

1. mini_batch_size
2. Learning_rate
3. Optimizer - sgd, adam, rmsprop, adadelta

Instance types used for object detection are GPUs only, multi GPU and multi machine are okay, CPU or GPU is only used for inference.

## Image Classification in Sagemaker

Assigning one or more labels to an image, image classification doesnt tell you what the objects are but just what the objects are in the image. Its used as seperate algorithms for MXNet and tensorflow. 

- MXNet: has full training mode which runs image classification with random weights, transfer learning mode which has pre trained weights.
- Tensorflow: Uses various tensorflow hub models for image classification.

### Important hyerparameters:

1. Batch_size
2. Learning_rate
3. Optimizer
4. Weight_decay, beta1, beta2, eps, gamma

Instance types: GPU usesd for training with CPU or GPU used for inference.

## Semantic Segmentation in Sagemaker

Pixel level object classification which is different from image classification that assigns labels to whole images. Different from object detection as that assigns labels to bounding boxes. Semantic segmentation produces a segmentation mask. 

Training input: JPG and PNG images with annotations, these are valid for training and validation. Label maps to describe annotations. There is also augmented manifest image format which is supported for pipe mode. 

Built on MXNet Gluon and Gluon CV. There is a choice of 3 algorithms which can be used such as fully convolutional network, pyramid scene parsing and deeplabv3. Incremental training or training from scratch is supported too. 

### Important hyperparameters:

1. Epochs, learning_rate, batch_size, optimizer
2. Algorithm
3. Backbone

Instance types: Only GPU is suppported for training on a single machine. Inference is supported on CPU or GPU. 

## Random Cut Forest in Sagemaker

A random cut forest is used for anomaly detection, undergoes unsupervised learning and detects unexpected spikes in time series data. It assigns an anomaly score to each data point and is based on an algorithm developed by Amazon. The algorithm creates a forest of trees where each tree is a partition of the training data. Looks at expected change in complexity of the tree as a result of adding a point into it. The data is sampled randomly, and then trained. 

Training input: requires Record-IO Protobuf or CSV and can use file, pipe mode on either.

### Important hyperparmeters:

1. Num_trees: increasing this reduces the noise in the data
2. Num_samples_per_tree: Should be chose such that 1/num_samples_per_tree is approx the same as ratio of anomalous to normal data

Instance types: does not take advantage of GPUs, use M4/C4/C5 for training and ml.c5.xl for inference

## Neural Topic Model in Sagemaker

Used to organise documents into topics or classify/summarise documents based on topics. Undergoes unsupervised ML and uses an algorithm called neural variational inference for training. Users can define how many topics are needed and these topics are a latent representation based on top ranking words. 

Training input: expects four data channels, train is required with validation/test/auxiliary as optional. recordIO-protobuf or csv is used as data input and word must be tokenized into integers. Data input can be in file or pipe mode.

### Important hyperparameters:

1. Lowering mini_batch_size and learning_rate can reduce validation loss. This comes at the expense of training time.
2. Num_topics

Instance types: GPU is recommended for training with CPU used for inference and being cheaper.

## Latent Dirichlet Allocation LDA in Sagemaker

LDA is another topic modelling algorithm which is not the same as deep learning. Undergoes unsupervised learning where the topics themselved are unlabelled. Can be used for things other than words such as clustering customer based on purchases.

Training input: recordIO-protobuf or csv data is used, each document has counts for every word in vocabularly. Pipe mode as method of delivery is the only one supported

### Important hyperparameters:

1. Num_topics
2. Alpha0: smaller values generate sparse topic mixtures whilst larger values > 1.0 produce uniform mixtures

Instance types: single instance CPU used for training

## K-Nearest-Neighbours KNN in Sagemaker

A simple classification or regression algorithm which finds the closest K points to a sample point and returns the most frequent label or average value. The training channel contains data whilst the test channel emits accuracy or MSE. Data is first sampled, sagemaker uses a dimensionality reduction stage and builds an index to look up neighbours. The model is serialised and queried for the nearest K.

Training input: recordIO-protobuf or csv data is used with the first column used as a label. File or pipe mode can be used on either.

### Important hyperparameters:

1. K!
2. Sample_size

Instance types: Ml.m5.2xlarge, Ml.p2.xlarge used for training CPU/GPU. For inference use CPU for lower latency and GPU for higher throughput on larger batches of data. 

## K-means Clustering in Sagemaker

K-means is unsupervised clustering which divides data into K groups where members of a group are as similar as possible to each other. Every observation is mapped to n-dimensional space where n = no. of features. Works to optimise the centre of K clusters. 

Training Input: recordIO-Protobuf or CSV data is used in either file or pipe mode.

### Important Hyperparameters: 

1. K! - choosing K is tricky and so plot within-cluster sum of squares as function of K
2. Mini_batch_size
3. Extra_center_factor
4. Init_method

Instance types: CPU or GPU but CPU is recommended with only one GPU instance user per GPU. ml.g4dn.large is used for GPU.

## Principal Component Analysis PCA in Sagemaker

PCA is used for dimensionality reduction where high dimensionality data is projects into a lower dinension to minimise the loss of information. The reduced dimenions are called components. The first component has the largest possible variability. PCA is a method of unsupervised ML. The covariance metric is created then singular valuar decomposition is used. There are two modes: regular mode for sparse data and a moderate number of observations, randomised for large number of observations and features.

Training Input: recordIO-profotbuf or csv, can use file or pipe modes on either file type.

### Important Hyperparameters:

1. Algorithm_mode
2. Subtract_mean: unbias data

Instance types: CPU or GPU can be used and it depends on the specifics of the input data about which one to use.

## Factorization Machines in Sagemaker

Factorisation machines are used for dealing with sparse data. This algorithm can be used for click prediction, item recommendations. It comes about as an individual doesnt interact with most pages/products, there is sparse data. A form of supervised ML, classification or regression and limited to pair-wise interactions.

Factorisation machines finds factors which can be used to predict a classification, ie whether a purchase has been made or not. Usually used in the context of recommender systems. 

Training input: recordIO-Protobuf with Float32. If there is sparse data then this means using csv as input data isnt practical.

### Important hyperparameters:

1. Initialisatiom methods for bias, factors and linear terms
2. Uniform, normal or constant
3. Can tune properties of each method

Instance types: CPU or GPU where CPU is recommended and GPU works with dense data

## IP Insights in Sagemaker

Unsupervised learning of IP address usage patterns, identifies suspicious behaviour from IP addresses such as identifing logins from anomalous IPs and identifing accounts which create resources from anomalous IPs. Uses a neural network to learn latent vector representations of entities and IP addresses. Entities are hashed and embedded with many entities needing a large hash size.

IP insights can also generate negative samples during training by randomly pairing entities and IPs. 

Training input: usernames, accountids can be fed in directly with no need for pre processing of this data. CSV input only with entities and IPs being a part of that csv file. 

### Important hyperparameters:

1. Num_entity_vectors: hash_size, set to twice the number of unique entity identifiers.
2. Vector_dim: size of embedding vectors, scales model size, too large a vector dimension results in overfitting.
3. Epochs, learning_rate, batch_size etc.

Instance types: CPU or GPU can be used but GPU is recommended or multiple GPUs. The size of the instance.