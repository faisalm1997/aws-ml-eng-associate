# Model Training, Tuning and Evaluation

## Deep Learning Activation Functions

An activation function defines the output of a node or neuron given its input.

Types of activation functions:

1. Linear: does not add much value and cannot support backpropagation well.
2. Binary step: on/off style function that does not work well with calculus or multiclass classification.
3. Non-linear: creates complex mappings between inputs and outputs and supports backpropagation.

Common non-linear activation functions:

1. Sigmoid, logistic, and tanh: smooth curves that map values to 0-1 or -1 to 1; tanh is often preferred over sigmoid.
2. ReLU: fast and popular, but can cause the dying ReLU problem when inputs are zero or negative.
3. Leaky ReLU: adds a small negative slope to reduce the dying ReLU problem.
4. PReLU: learns the negative slope through backpropagation.
5. ELU, Swish, and Maxout: other ReLU variants.
6. Softmax: used on the final layer of multiclass classification to convert outputs into probabilities.

Choosing an activation function:

- Use softmax for multiclass output layers.
- RNNs often work well with tanh.
- ReLU is usually a good default for hidden layers.
- Leaky ReLU is a strong fallback if ReLU is not enough.
- PReLU, Maxout, and Swish are options for deeper networks.

## Convolutional Neural Networks

CNNs are used when data does not fit neatly into columns. They are commonly used for images, machine translation, sentence classification, and sentiment analysis.

CNN building blocks:

1. Convolutional layers: apply filters to produce feature maps.
2. Pooling layers: reduce the spatial size of feature maps.
3. Fully connected layers: produce the final prediction.

How CNNs work:

1. Local receptive fields respond to parts of the input.
2. Convolutional filters overlap to cover the full input.
3. Higher layers learn more complex patterns.
4. Colour images often use separate channels for red, green, and blue.

CNNs in Keras and TensorFlow:

- Input data must have the right dimensions, such as width, height, and colour channels.
- Conv2D performs the convolution.
- MaxPooling2D reduces spatial dimensions.
- Flatten converts convolution output into a vector.
- Dense layers perform the final classification.
- A common pattern is Conv2D -> MaxPooling2D -> Dropout -> Flatten -> Dense -> Dropout -> Softmax.

Notes:

- CNNs are resource intensive on CPU, GPU, and RAM.
- They have many hyperparameters such as kernel size, layer count, units, and pooling size.
- Data preparation and storage are often the hardest parts of building CNNs.

Specialised CNN architectures:

1. LeNet-5: early CNN for handwritten digit recognition.
2. AlexNet: deeper model that won ImageNet in 2012.
3. GoogLeNet: deeper model with inception modules.
4. ResNet: uses skip connections to support very deep networks.

## Recurrent Neural Networks

RNNs are used for sequential data such as time series, speech recognition, and natural language processing. They maintain a hidden state that captures information from previous inputs.

RNN topologies:

1. Sequence to sequence: e.g. stock prediction from historical data.
2. Sequence to vector: e.g. sentence to sentiment.
3. Vector to sequence: e.g. image to caption.
4. Encoder to decoder: used for machine translation.

Training RNNs:

- RNNs are trained using backpropagation through time.
- The network is unrolled across time steps and then backpropagated.
- Earlier time steps can become diluted over time.
- LSTM cells preserve short-term and long-term state.
- GRU cells are a simpler alternative to LSTM.

Notes:

- RNNs are sensitive to topology and hyperparameters.
- They are resource intensive and can fail to converge if configured badly.

## Tuning Neural Networks

Common tuning knobs:

1. Learning rate: controls the step size of gradient descent.
2. Batch size: controls how many samples are used in each training batch.

Learning rate guidance:

- Too high: may overshoot the optimal solution.
- Too low: may take too long to converge.

Batch size guidance:

- Smaller batches can escape local minima more easily.
- Larger batches may converge to poor solutions by chance.

## Regularisation Techniques

Regularisation helps prevent overfitting, where a model learns training patterns that do not generalize well.

Common methods:

1. Dropout: randomly removes neurons during training to force more robust representations.
2. Early stopping: stops training when validation performance stops improving.

L1 and L2 regularisation:

- L1 adds the sum of weights as a penalty and tends to perform feature selection.
- L2 adds the sum of squared weights as a penalty and keeps all features weighted more smoothly.
- L1 can create sparsity but is less efficient with sparse output.
- L2 is generally more computationally efficient with dense output.

## The Vanishing Gradient Problem

The vanishing gradient problem occurs when gradients become very small as they propagate backward through deep networks. This slows learning and can prevent convergence. The opposite issue is exploding gradients.

Ways to address it:

1. Split the model into smaller sub-networks.
2. Use LSTM cells.
3. Use residual networks.
4. Choose better activation functions such as ReLU.
5. Use gradient checking to validate derivatives during training.

## The Confusion Matrix

A confusion matrix is used to evaluate classification performance. It shows the number of true positives, true negatives, false positives, and false negatives.

- Rows usually represent predictions.
- Columns usually represent actual values.

## Precision, Recall, F1, and AUC

### Recall

Recall is the proportion of true positives that are correctly identified.

Formula:

True Positives / (True Positives + False Negatives)

Use recall when false negatives matter most, such as fraud detection.

### Precision

Precision is the proportion of predicted positives that are actually true positives.

Formula:

True Positives / (True Positives + False Positives)

Use precision when false positives matter most, such as medical screening.

### Other Metrics

1. F1 score: harmonic mean of precision and recall.
2. RMSE: root mean squared error.
3. Specificity: TN / (TN + FP), also called true negative rate.

### ROC Curve

The ROC curve plots true positive rate against false positive rate across different thresholds. The ideal curve approaches the upper-left corner.

### AUC

AUC is the area under the ROC curve. It ranges from 0 to 1.

- 1.0 is perfect.
- 0.5 is random.
- AUC is especially useful for comparing classifiers on imbalanced data.

### Precision-Recall Curve

The precision-recall curve plots precision against recall across thresholds. It is especially useful for imbalanced datasets where the positive class is rare.

## RMSE, R-squared, and MSE

1. RMSE: square root of the average squared difference between predicted and actual values.
2. R-squared: proportion of variance in the target explained by the model.
3. MSE: average squared difference between predicted and actual values.

## Ensemble Learning: Bagging and Boosting

Ensemble learning combines multiple models to improve performance.

1. Bagging: trains models on different subsets of the data and averages predictions. Examples include Random Forest and Bagged Decision Trees.
2. Boosting: trains models sequentially so each model corrects the previous one. Examples include AdaBoost, Gradient Boosting, and XGBoost.

Notes:

- Boosting often improves accuracy more than bagging.
- Bagging is easier to parallelise.
- The right choice depends on the goal.

## Automatic Model Tuning in SageMaker

Hyperparameter tuning searches for the best values for settings such as learning rate, batch size, and network depth.

SageMaker hyperparameter tuning jobs can train multiple combinations and learn as they go instead of trying every possible combination.

Best practices:

1. Do not optimise too many parameters at once.
2. Keep parameter ranges as narrow as possible.
3. Use logarithmic scales when appropriate.
4. Avoid running too many training jobs in parallel.
5. Make sure distributed jobs report the correct objective metric.

### Hyperparameter Tuning in AMT

1. Early stopping: stop a tuning job early if it is not improving.
2. Warm start: reuse previous tuning jobs as a starting point.
3. Resource limits: there are quotas for parallel jobs, hyperparameters, and training jobs.
4. Grid search: tries every possible combination of categorical parameters.
5. Random search: samples combinations randomly and can run in parallel.
6. Bayesian optimisation: learns from each run to move toward better values.
7. Hyperband: allocates resources dynamically and uses early stopping.

## SageMaker Autopilot

SageMaker Autopilot automatically builds, trains, and tunes models from your data.

Workflow:

1. Load data from S3.
2. Select the target column.
3. Auto-generate candidate models.
4. Review the model notebook for visibility and control.
5. Deploy and monitor the model.

Notes:

- Human guidance can be added.
- Input data must be tabular CSV or Parquet.

### Autopilot Explainability

1. Integrates with SageMaker Clarify for transparency.
2. Uses SHAP baselines and Shapley values for feature attribution.

## SageMaker Studio

SageMaker Studio is a web-based IDE for building, training, and deploying ML models.

1. SageMaker notebooks: create and share notebooks without managing infrastructure.
2. SageMaker experiments: organise, capture, compare, and search ML jobs.

## SageMaker Debugger

SageMaker Debugger saves internal model state at regular intervals and defines rules to detect unwanted training behaviour.

It can:

1. Run a debug job for each configured rule.
2. Fire CloudWatch events when a rule is triggered.
3. Generate training reports and dashboards.
4. Profile framework operations, model parameters, and system bottlenecks.

There are debugger APIs and the SMDebug client library for hooks and training inspection.

## SageMaker Model Registry

The SageMaker Model Registry is a central repository for storing and managing models.

It helps users:

1. Track model versions and metadata.
2. Manage approval status.
3. Deploy models to production.
4. Automate deployment with CI/CD.
5. Share models and integrate with model cards.

Typical workflow:

1. Create a model group.
2. Create a SageMaker pipeline.
3. Register model versions for each run.
4. Add the model group to the registry collections.

### Analysing Training Jobs with TensorBoard

TensorBoard is a visualisation tool for analysing model training.

It can show:

1. Loss and accuracy over time.
2. Model structure.
3. TensorFlow or PyTorch training behaviour.

It can be integrated through the SageMaker console or via URL.

## SageMaker Training Techniques

### SageMaker Training Compiler

SageMaker Training Compiler optimises training by compiling model code into a more efficient format.

It:

1. Works with AWS Deep Learning Containers.
2. Converts models into hardware-optimised instructions.
3. Is incompatible with SageMaker distributed training libraries.

### Warm Pools

Warm pools retain and reuse provisioned infrastructure for repeated training runs.

Benefits:

1. Speeds up repeated training.
2. Can use persistent cache across jobs.
3. Requires setting `keepaliveperiodinseconds` in the training job resource config.
4. Requires a service limit increase request.

## SageMaker Checkpointing, Health Checks, and Restarts

### Checkpointing

Checkpointing saves model state during training so the job can resume after interruptions.

Notes:

- Checkpoints synchronise with S3.
- Configure `checkpoint_s3_uri` and `checkpoint_local_path` in the estimator.

### Cluster Health Checks and Automatic Restarts

- Health checks run automatically on `ml.g` and `ml.p` instances.
- Faulty instances are replaced.
- GPU health checks and NVIDIA collective communication library checks are performed.
- SageMaker internal service errors can trigger automatic job restarts.

## SageMaker Distributed Training and Distributed Data Parallelism

### Distributed Training

Distributed training lets you run training across multiple instances or parallelise individual training jobs.

Notes:

1. You can parallelise by data or by model.
2. Larger instance types should usually be considered before distributed training.

### Distributed Training Libraries

The SageMaker distributed training libraries are built on AWS custom collective communication for EC2.

1. AllReduce: distributes gradient computation across GPUs and is implemented in the distributed data parallel library.
2. AllGather: improves performance by managing communication between nodes and offloading communication work to the CPU.

Notes:

- These libraries are not compatible with the SageMaker Training Compiler.
- Other options include PyTorch DDP, torchrun, mpirun, DeepSpeed, and Horovod.

## SageMaker Model Parallelism Library

Model parallelism splits a model across multiple GPUs when it does not fit into a single GPU's memory.

The SageMaker model parallel library can:

1. Split the model across GPUs.
2. Support sharded data parallelism.
3. Shard trainable parameters, gradients, and optimizer state.

## Elastic Fabric Adapter

Elastic Fabric Adapter (EFA) is a network interface for EC2 that provides low-latency, high-throughput communication between instances.

### MiCS

MiCS is a communication library optimised for EFA.

It:

1. Provides a high-performance communication layer for distributed training.
2. Supports models with more than 1T parameters.
3. Benefits from larger instances, though they are more expensive.
