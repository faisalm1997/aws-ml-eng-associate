# Model Training, Tuning and Evaluation

## Deep Learning - Activation Functions

An activation function defines the output of a node or neuron given its input signals. There are different types:

1. Linear activaction function - it doesnt really do anything, neither can it perform back propagation
2. Binary step function - this is an on and off kind of function, can't handle multiple classifcation and vertical slopes dont work well with calculus
3. Non linear activation functions - these create complex mappings between inputs and outputs. Allow backpropagation because they have a useful derivative and allow for multiple layers (linear functions degenerate to a single layer)

Types of non linear activation functions:

a. Signoid, logistic, TanH - nice and smooth curve which scales everything from 0-1 (signoid, logistic) or -1 to 1 (tanH, hyperbolic tangent). It is computationally expensive and TanH is generally preferred over sigmoid. 
b. Rectified Linear Unit (ReLU) - a very popular choice, easy and fast compute, but when inputs are 0 or negative, we have a linear function which causes a problem 'dying ReLU problem'
c. Leaky ReLU - solves the dying ReLU problem by introducing a negative slope below 0
d. Parametric ReLU (PReLU) - ReLU but the slope in the negative part is learned via backpropagation, often a complicated non linear activation function
e. Exponential Linear Unit (ELU), Swish and Maxout are other ReLU variants
f. Softmax - uses on the final output layer of a multi class classification problem, basically converts outputs to probabilities of each classification. This function cannot produce more than one label for something.

Choosing an activation function: For multiple classifications, use softmax on the output layer, RNNs work well with Tanh and for everything else, start with ReLU, if you need better than start with leaky ReLU and last resort, PReLU/Maxout, swish can be used for really deep networks.

## Convulational Neural Networks CNN

CNNs are used when the data you have doesnt neatly align into columns. They are most commonly used for finding features within images, machine translation, sentence classification or sentiment analysis. They can find features that aren't in a specific spot such as words in a sentence (feature-location invariant).

CNNs are made up of convolutional layers, pooling layers and fully connected layers. The convolutional layer applies a filter to the input data to create a feature map. The pooling layer reduces the spatial dimensions of the feature map, while the fully connected layer takes the output from the convolutional and pooling layers and makes a final prediction.

### How do they work?

1. Local receptive fields are groups of neurons which only respond to a part of what your eyes see (subsampling)
2. They overlap each other to cover the entire visual field (these are called convolutions)
3. They feed into higher layers that identify increasingly complex images such as some receptive fields identifying horizontal lines and lines at different angles etc. 
4. For colour images, extra layers are added for red, green and blue

A CNN can tell a user about a certain image by scanning the images edges using the local receptive fields, the edges then get picked up by a higher level convolution which identifies the shape. The shape gets matched against the pattern of what the image is showing and the information keeps getting processed until the image is identifed and formed. 

### CNNs with keras / tensorflow 

- The source data must be of appropriate dimensions ie width x length x colour channels. 
- Conv2D layer type does the actual convolution on a 2D image
- MaxPooling2D layer type does the pooling, it reduces the spatial dimensions of the feature map
- Flatten layer type flattens the output of the convolutional and pooling layers into a hidden layer of neurons 
- Dense layer type is the fully connected layer that takes the output from the convolutional and pooling layers and makes a final prediction
- The first layer of the CNN should be a Conv2D layer, followed by a MaxPooling2D layer, and then a Flatten layer. After that, you can add as many Dense layers as you want, but the last layer should be a Dense layer with the number of neurons equal to the number of classes in your classification problem and an activation function of softmax.

Typical usage: Conv2D > MaxPooling2D > Dropout > flatten > dense > dropout > softmax

Note: CNN's are very resource intensive for CPU/GPU/RAM, have lots of hyperparameters such as kernel size, layers, number of units, amount of pooling. Getting the training data, storing and accessing it is often the hardest part of making CNNs work.

### Specialised CNN architectures

- LeNet-5: one of the earliest CNN architectures, designed for handwritten digit recognition
- AlexNet: won the ImageNet competition in 2012, introduced the use of image classification and is deeper then LeNet
- GoogleLeNet: Even deeper, but with better performance. Introduces inception modules
- ResNet: Introduces skip connections to allow for very deep networks without the vanishing gradient problem - maintains performance

## Recurrent Neural Networks RNNs 

RNNs are used when the data you have is sequential in nature such as time series data, speech recognition, natural language processing etc. They are designed to handle sequences of data by maintaining a hidden state that captures information about previous inputs in the sequence.

A recurrent neuron is often termed a memory cell and connected together to form a layer of recurrent neurons.

### RNN Topologies

1. Sequence to sequence - e.g. predict stock prices based on a series of historical data
2. Sequence to vector - e.g. words in a sentence to sentiment 
3. Vector to sequence - e.g. create captions from an image 
4. Encoder to decoder - e.g. sequence > vector > sequence used for machine translation

### Training RNNs

RNNs are trained using backpropagation through time (BPTT), which is a variant of backpropagation that takes into account the sequential nature of the data. The training process involves unrolling the RNN for a certain number of time steps and applying backpropagation to update the weights of the network. The end looks like a deep neural network. 

1. One of the drawbacks of training RNNs is that the earlier time steps get diluted over time, this can be a problem e.g. when learning new sentence structures.
2. LSTM Cell - Long short term memory cell which maintains the short term and long term states.
3. GRU Cell - gated recurrent unit which is a simplified version of LSTM cell that performs about as well.

Training is really difficult as well for RNNs as they are very sensitive to topologies, and choice of hyperparameters. They are very resource intensive and a wrong choice can lead to an RNN which doesnt converge at all. 

## Tuning Neural Networks

Users are able to tune neural networks in several ways: 

1. Learning rate: neural networks are trained by the gradient descent and users can change the learning rate to see how far apart these samples are. They can start at random points and sample different solutions which can then minimise cost over many epochs.
    a. Effect of learning rate: too high a learning rate means you can overshoot the optimal solution, too small a learning rate will take too long to find the optimal solution. Learning rate is a hyperparameter.
2. Batch size: how many training samples are used within each batch of each epoch
    a. smaller batch sizes can work their way out of the local minima more easily, larger batch sizes can converge on the wrong solution at random.

## Regularisation techniques for neural networks

Regularisation is the technique which is used to prevent overfitting. Overfitted models have learned patterns in the training data that dont generalise to the real world. They are often seen as high accuracy on training data but low accuracy on test/evaluation data sets.

When there are too many layers or too many neurons in the neural network, we often use dropout and early stopping mechanisms to regularise the neural network and prevent overfitting. 

1. Dropout - randomly drops out neurons during training, this forces the network to learn multiple independent representations of the data and prevents overfitting.
2. Early stopping - monitors the performance of the model on a validation set during training and stops

L1 and L2 regularisation: this technique prevents overfitting in ML in general. A regularisation term is added as weights are learned. L1 term is the sum of weights, L2 term is the sum of the square of weights. The same idea can be applied to loss functions. The difference is that L1 performs feature selection and is computationally inefficient with a sparse output whereas L2 has all the features weighted and is computationally efficient with a dense ouput.

Users may choose L1 over L2, such out of 100 features, maybe only 10 end up with non zero coefficients which can result in sparsity and computationally inefficient. If a user thinks all features are important then L2 is better.

## The vanishing gradient problem

The vanishing gradient problem is a common issue in training deep neural networks, where the gradients of the loss function with respect to the weights become very small as they are propagated back through the layers. The learning curve slope approaches 0. This can lead to slow convergence and difficulty in training the network. The opposite problem is 'exploding gradients'.

To address the vanishing gradient problem, several techniques have been developed:

1. Multi level hierarchy - break up levels into their own sub networks which are trained individually
2. Long short term memory
3. Residual networks - ResNet, an ensemble of short networks
4. Better choice of activation function - ReLU is a good choice
5. Gradient checking - a debugging technique which checks the derivatives computed during training and is useful for validating code of neural network training

## The confusion matrix

A confusion matrix is a table that is used to evaluate the performance of a classification model. It is a square matrix that shows the number of true positives, true negatives, false positives, and false negatives for a given set of predictions.

A predidction is denoted by the total of the row, whereas the true prediction or frequency is the total of the column.

## Precision, Recall, F1, AUC

### Recall 

Recall is the proportion of true positives that are correctly identified by the model. It is calculated as: True positives / True positives + False Negatives. In other words, this is called sensitivity, a good choice of metric when you care a lot about false negatives e.g. fraud detection

### Precision 

Precision is the proportion of predicted positives that are actually true positives. It is calculated as: True Positives / True Positives + False Positives. In other words, this is called correct positive value, a good choice of metric when you care a lot about false positives e.g. medical screening, drug testing.

### Other Metrics

- F1 Score: the harmonic mean of precision and recall, a good choice when you want to balance precision and recall. Calculated as: 2TP/ 2TP + FP + FN 
- RMSE: Root mean squared error, an accuracy measurement which only cares about right and wrong answers
- Specifity: TN / TN + FP also called the true negative rate

### ROC Curve

The ROC curve is a graphical representation of the performance of a binary classification model. It plots the true positive rate (recall) against the false positive rate (1 - specificity) at various threshold settings. The area under the ROC curve (AUC) is a measure of the overall performance of the model, with a value of 1 indicating perfect performance and a value of 0.5 indicating random performance. 

The ideal curve would be a point in the upper-left corner. The more its bent towards the upper left, the better it is.

### AUC 

The area under the ROC curve (AUC) is a measure of the overall performance of a binary classification model. It is calculated as the area under the ROC curve, which ranges from 0 to 1. A value of 1 indicates perfect performance, while a value of 0.5 indicates random performance. AUC is a useful metric for evaluating the performance of a binary classification model, especially when the classes are imbalanced.

ROC AUC of 0.5 is a useless classifier, a value of 1.0 is perfect, AUC is used to compare classifiers.

### P-R Curve

The precision-recall (P-R) curve is a graphical representation of the performance of a binary classification model. It plots precision against recall at various threshold settings. The area under the P-R curve (AUC-PR) is a measure of the overall performance of the model, with a value of 1 indicating perfect performance and a value of 0 indicating random performance. The P-R curve is particularly useful when evaluating models on imbalanced datasets, where the positive class is rare. It is similar to the ROC curve.

## RMSE, R-squared, MSE

- RMSE: Root mean squared error, an accuracy measurement which only cares about right and wrong answers, calculated as the square root of the average of the squared differences between the predicted and actual values.
- R-squared: a statistical measure of how well the regression model fits the data, calculated as the proportion of the variance in the dependent variable that is predictable from the independent variables.
- MSE: Mean squared error, a measure of the average squared difference between the predicted and actual values, calculated as the average of the squared differences between the predicted and actual values. It is similar to RMSE but without taking the square root.

## Ensemble Learning: Bagging & Boosting

Ensemble learning is a machine learning technique that combines the predictions of multiple models to improve the overall performance of the model. There are two main types of ensemble learning: bagging and boosting.

- Bagging (Bootstrap Aggregating): This technique involves training multiple models on different subsets of the training data and then averaging their predictions. The idea is to reduce the variance of the model by combining the predictions of multiple models. Examples of bagging algorithms include Random Forest and Bagged Decision Trees.
- Boosting: This technique involves training multiple models sequentially, where each model is trained to correct the errors of the previous model. The idea is to reduce the bias of the model by combining the predictions of multiple models. Examples of boosting algorithms include AdaBoost, Gradient Boosting, and XGBoost.

Boosting generally yields better model accuracy where as bagging is used to avoid overfitting. Bagging is easier to parallelise in terms of running models in tandem. It all depends on the users goal for the model.

## Automatic model tuning in sagemaker

Often a user has to experiment with the best values they would need for hyperparameters such as learning rate, batch size and depth. The problem becomes more complicated when you have many different hyperparameters and need to try every combination possible to train a model and evaluate it every time. 

Sagemaker has a hyperparameter tuning job which can be used to train combinations of values, the set of values which produce the best results can then be deployed as a model. The job learns as it goes so it doesnt have to try every combination to make sure the model is working optimally.

Best practices:

- Dont optimise too many parameters at once
- Limit the ranges to as small as possible
- Use logarithmic scales when appropriate
- Don't run too many training jobs concurrently, this will limit how well the process can learn as it goes
- Making sure the training jobs which run on multiple instances report the correct objective metric in the end

### Hyperparameter tuning in AMT

1. Early stopping: Stop training in a tuning job early if it is not improving the objective, this reduces compute time and avoids overfitting
2. Warm start: Uses one or more previous tuning jobs as a starting point, informs the hyperparameter which combinations to search next and can be a way to start where you left off from a stopped hyperparameter job.
3. Resource limits: there are limit for a number of parallel tuning jobs, number of hyperparameters and number of training jobs per tuning job etc. Increasing these requires requesting a quota increase from AWS support.
4. Grid search: limited to categorical parameters, and tries every possible combination
5. Random search: choose a random combination of hyperparameter values for each job, no dependence on prior runs so they can all run in parallel
6. Bayesian optimsiation: Treats tuning as a regression problem, learns from each run to converge on optimal values
7. Hyberband: appropriate for algorithms that publish results iteratavely, dynamically allocates resources, early stopping and in parallel. Much faster than random search of bayesian. 

## Sagemaker Autopilot

Sagemaker Autopilot is a fully managed service that automatically builds, trains, and tunes machine learning models based on the user's data. It allows users to create machine learning models without having to write any code or have any prior knowledge of machine learning.

Autopilot workflow: Load data from s3 for training, select the target column for prediction, and models are created automatically. The model notebook is available for visibility and control. Users are able to deploy and monitor the model, refine via notebook if needed.

Sagemaker autopilot can add in human guidance, and data must be tabular csv or parquet.

### Autopilot explainability

1. Integrates well with sagemaker clarify, transparency on how models arrive at predictions.

2. Feature attribution: Uses SHAP baselines/shapley values. Research from cooperative game theory and assigns each feature an importance value for a given prediction. 

## Sagemaker Studio

Sagemaker Studio is an integrated development environment (IDE) for machine learning that provides a web-based interface for building, training, and deploying machine learning models.

1. Sagemaker notebooks: Create and share jupyter notebooks with sagemaker studio. Switch between hardware configurations with no infra to manage.
2. Sagemaker experiments:  Organise, capture, compare and search your ML jobs.

## Sagemaker Debugger

Saves internal model state at periodical intervals. Defines rules for detecting unwanted conditions while training. A debug job is run for each rule you configure. Everytime a rule is hit, it fires a cloudwatch event.

Sagemaker debugger dashboards can auto generate training reports. There are built in rules, profile model framework operations, debug model parameters and can monitor system bottlenecks.

There are debugger APIs available in GitHub from which we can construct hooks, rules for CreateTrainingJob and DescribeTrainingJob APIs. SMDebug client library lets you register hooks for accessing training data.

Sagemaker debugger insights dashboard, which can profile system resource usage and training.

## Sagemaker Model Registry

Sagemaker model registry is a central repository for storing and managing machine learning models.

It allows users to:

1. Track model versions, manage model metadata, and deploy models to production.
2. Associate metadata with models
3. Manage approval status of models
4. Deploy models to prod and automate deployment with CI/CD
5. Share models with other users and also integrate sagemaker model cards

The overall workflow for sagemaker model registry: create model group > create sagemaker pipeline > register model versions for each run in the model group > add model group to model registry collections.

### Analysing training jobs with tensorboard

TensorBoard is a visualisation tool that allows users to analyse and visualise the training process of machine learning models. 

It provides a web-based interface for visualising metrics such as loss and accuracy, as well as the structure of the model itself. Tensorboard is a visualisation toolkit for tensorflow or pytorch. Can be integrated with sagemaker console or via ORL.

## Sagemaker training techniques

### Sagemaker Training Compiler

Sagemaker training compiler is a tool that optimises the training process of machine learning models by compiling the model code into a more efficient format. It can significantly reduce the training time and improve the performance of the model.

1. It can be integrated with AWS deep learning container and DLCs are pre made docker images for MXNet, tensorflow, pytorch
2. Converts models into hardware optimised instructions
3. Incompatible with sagemaker distributed training libraries

### Warm Pools

- Retain and re use provisioned infrastructure. Useful if repeatedly training a model to speed things up. 
- Use by setting 'keepaliveperiodinseconds' in your training jobs resource config.
- Requires a service limit increase request.
- Use a persistent cache to store data across training jobs, to reduce costs.

## Sagemaker checkpointing, cluster health checks and system restarts

### Checkpointing

Checkpointing is the process of saving the state of a machine learning model during training. This allows users to resume training from the last checkpoint in case of interruptions or failures. 

Auto synchronises with S3. To use, define checkpoint_s3_uri and checkpoint_local_path in sagemaker estimator.

### Cluster health check and automatic restarts

- Run automatically when using ml.g or ml.p instance types.
- Replaces any faulty instances and runs GPU health checks
- Ensures NVIDIA collective communication library is working. 
- Sagemaker internal service errors will results in an auto restart of the training job which replaces bad instances, restarts healthy ones or restarts the job. 

## Sagemaker distributed training + distributed data parallelism

### Distributed training

- Users are able to run multiple training jobs in parallel called job parallelism.
- Individual training can also be parallelised. Both from data and model perspective.
- Use larger instance types before enabling distributed training as they use multiple parallel instances.

### Distributed training libraries

Built on the AWS custom collective library for EC2. Solves a similar problem to MapReduce/Spark but for distributing computation of gradients in the gradient descent.

1. AllReduce collective: 
    - Distributes computation of gradient updates to and from GPUs.
    - Implemented in the sagemaker distributed data parallelism library.
    - Users have to specify a backend of smdpp

2. AllGather collective:
    - Manages communication between nodes to imrpove performance
    - Offloads communications overhead to the CPU, freeing up GPUs.

Not compatible with sagemaker training compiler.

Other distributed training libraries: pytorch distributed data parallel, torchrun, mpirun, deepspeed and horovod.

## Sagemaker model parallelism library

Model parallelism is a technique used to train large machine learning models that cannot fit into the memory of a single GPU.

The Sagemaker model parallelism library allows users to split a model across multiple GPUs and train it in parallel.

Sagemaker MPP can go further with pytorch and can add in optimise state sharding. Sharded data parallelism combined parallel data and models, this means the optimiser states are sharded into sharding groups.

Sharded data parallelism also:

- Shards the trainable parameters
- Shards associated gradients
- Implemented in sagemaker model parallel library

## Elastic Fabric Adapter (EFA)

Elastic Fabric Adapter (EFA) is a network interface for Amazon EC2 instances that provides low latency and high throughput communication between instances. It is designed to improve the performance of distributed machine learning training by reducing the communication overhead between instances.

### MiCS

MiCS is a communication library that is optimized for use with EFA. It provides a high-performance communication layer for distributed machine learning training, allowing for efficient communication between instances using EFA.

It enables the training of models with more than 1T parameters. Bigger instances helps minimise communication overhead but it also expensive.