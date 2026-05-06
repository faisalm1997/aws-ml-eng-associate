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

## Neural Topic Model in Sagemaker

## Latent Dirichlet Allocation LDA in Sagemaker

## K-Nearest-Neighbours KNN in Sagemaker

## K-means Clustering in Sagemaker

## Principal Component Analysis PCA in Sagemaker

## Factorization Machines in Sagemaker

## IP Insights in Sagemaker