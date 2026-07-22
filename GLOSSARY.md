# AWS ML Engineer Associate — Glossary

Key terms and definitions, organized alphabetically. Use this to quickly look up an unfamiliar term during study.

---

## A

**Accuracy**
The proportion of total predictions that are correct. Only reliable as a metric when classes are balanced.

**Action Group (Bedrock)**
A set of tools defined for a Bedrock agent, each backed by a Lambda function. The agent calls these functions during task planning.

**Activation Function**
A mathematical function applied to the output of a neural network node that determines whether and how strongly the node fires. Common examples: ReLU, Sigmoid, Tanh, Softmax.

**Amazon Augmented AI (A2I)**
An AWS service that adds human review workflows to ML predictions. Used when a model's confidence is low or human oversight is required for compliance.

**Amazon Bedrock**
A fully managed AWS service for building and scaling GenAI applications using foundation models from Amazon and third-party providers, exposed via APIs.

**Amazon Comprehend**
An AWS NLP service that extracts entities, key phrases, sentiment, and topics from text. Supports custom classifiers and custom NER.

**Amazon Kendra**
An AWS enterprise search service powered by ML. Supports natural language queries across PDFs, HTML, Word docs, and other content.

**Amazon Lex**
An AWS service for building conversational chatbots using voice and text. Detects user intents and integrates with Lambda for fulfillment.

**Amazon Macie**
An AWS service that uses ML to automatically discover, classify, and protect sensitive data (PII, PHI) stored in S3.

**Amazon Personalize**
A fully managed AWS recommendation service. Uses prebuilt recipes for user personalization, related items, and popular items. No ML expertise required.

**Amazon Polly**
An AWS service that converts text to lifelike speech using deep learning. Supports lexicons, SSML, and multiple voice engines.

**Amazon Q for Business**
A fully managed GenAI assistant for employees built on Bedrock. Connects to 40+ enterprise data sources and respects existing access controls via IAM Identity Center.

**Amazon Rekognition**
An AWS service for image and video analysis. Detects objects, people, scenes, text, and inappropriate content (`DetectModerationLabels`).

**Amazon Textract**
An AWS service that extracts text, handwriting, forms, and tables from scanned documents and PDFs — beyond simple OCR.

**Amazon Transcribe**
An AWS automatic speech recognition service that converts audio to text. Supports custom vocabularies and custom language models.

**Amazon Translate**
An AWS service for natural language translation of large text volumes. Used to localise applications for international audiences.

**Anonymisation**
Permanently removing all identifying information from data so that re-identification is impossible. Irreversible.

**AUC-ROC (Area Under the Curve)**
A metric that measures a classifier's ability to distinguish between classes across all thresholds. A value of 1.0 is perfect; 0.5 is random.

**Auto Scaling (SageMaker)**
A SageMaker feature that dynamically adjusts the number of instances serving a production variant based on traffic, using CloudWatch metrics and defined scaling policies.

---

## B

**Backpropagation**
An algorithm for training neural networks by computing the gradient of the loss function with respect to each weight and updating weights in the direction that reduces loss.

**Backpropagation Through Time (BPTT)**
The backpropagation algorithm applied to recurrent neural networks by unrolling them across time steps.

**Bagging**
A technique where multiple models are trained on different random subsets of the training data and their predictions are aggregated. Random Forest uses bagging.

**Baseline (Model Monitor)**
A statistical profile of training data established by SageMaker Model Monitor. Live traffic statistics are compared against this baseline to detect drift.

**Batch Transform**
A SageMaker inference option that runs inference on large datasets offline. Results are stored in S3. No persistent endpoint is maintained.

**Binning (Bucketing)**
Converting a continuous numerical feature into discrete categories or "bins". Reduces sensitivity to small variations and outliers.

**Blue/Green Deployment**
A deployment strategy where all traffic is shifted from the old environment (blue) to the new environment (green).

**Boosting**
An ensemble learning technique where models are trained sequentially, with each model correcting the errors of the previous one. XGBoost and LightGBM use gradient boosting.

---

## C

**Canary Deployment**
A deployment strategy where a small percentage of traffic is routed to a new model first. If metrics are healthy, the rollout continues. SageMaker supports automatic rollback via CloudWatch alarms.

**Checkpointing**
Saving training progress to persistent storage (e.g., S3) at regular intervals so training can resume from the last checkpoint if interrupted.

**Chunking (Bedrock)**
The process of splitting documents into smaller pieces before converting them to vectors for a knowledge base. Strategies include semantic, hierarchical, fixed-size, and Lambda-based.

**Class Imbalance**
A dataset condition where one class significantly outnumbers another. Can lead to biased models that perform poorly on the minority class.

**Classification**
A supervised learning task where the model predicts a discrete category label. Examples: binary (fraud/not fraud) and multiclass (cat/dog/bird).

**CNN (Convolutional Neural Network)**
A neural network architecture that uses convolutional layers to detect local patterns in data. Commonly used for images but also for text and time series.

**Context Window**
The maximum number of tokens an LLM can process at once (input + output combined). Determines how much history or context the model can consider.

**Confusion Matrix**
A table that shows the counts of true positives, true negatives, false positives, and false negatives for a classification model.

**Continued Pre-training (Bedrock)**
Training a foundation model on additional unlabeled text to expose it to domain-specific knowledge. Does not require labeled input-output pairs.

**Cross-Validation**
A technique for estimating model performance by partitioning data into k subsets, training on k-1 and evaluating on the held-out fold, repeated k times.

**Curse of Dimensionality**
The phenomenon where adding more features to a dataset creates increasingly sparse data, making it harder for models to find patterns. Addressed with dimensionality reduction (e.g., PCA).

---

## D

**Data Drift**
A change in the statistical distribution of input features over time, causing model performance to degrade. Also called covariate shift.

**Data Quality Drift**
A type of drift detected by SageMaker Model Monitor where statistical properties of input features deviate from the baseline.

**Deployment Guardrails (SageMaker)**
Controls that manage how traffic shifts to a new model version, including canary, linear, and blue/green strategies with optional automatic rollback.

**Dimensionality Reduction**
Reducing the number of features in a dataset while preserving as much information as possible. Common methods: PCA, t-SNE, autoencoders.

**Dropout**
A regularisation technique where a random subset of neurons is ignored (dropped) during each training pass, preventing co-adaptation and reducing overfitting.

---

## E

**Early Stopping**
A regularisation technique that stops model training when validation performance stops improving, preventing overfitting.

**Embedding**
A dense vector representation of a token, word, or item that captures semantic meaning. Similar items have embeddings that are close together in vector space.

**EMR (Elastic MapReduce)**
An AWS-managed Hadoop service that runs Spark, Hive, Presto, Flink, and other big data frameworks on EC2 clusters.

**EMRFS**
A connector that allows EMR to read and write data in Amazon S3 as if it were HDFS.

**Encoder-Decoder Architecture**
A neural network design where an encoder compresses input into a latent representation and a decoder reconstructs or translates from that representation. Used in Seq2Seq, translation, and summarisation models.

**Ensemble Method**
Combining multiple models to produce better predictions than any single model. Examples: bagging (Random Forest), boosting (XGBoost), stacking.

**Explainability**
The ability to explain why a model made a specific prediction. SageMaker Clarify provides explainability using SHAP values.

---

## F

**F1 Score**
The harmonic mean of precision and recall. Best used when both false positives and false negatives matter and the class distribution is imbalanced.

**False Negative (FN)**
A prediction where the model predicted negative but the true label is positive. Minimising FNs maximises recall.

**False Positive (FP)**
A prediction where the model predicted positive but the true label is negative. Minimising FPs maximises precision.

**Feature Engineering**
The process of selecting, transforming, and creating features from raw data to improve model performance. Often the most impactful part of applied ML.

**Feature Store (SageMaker)**
A managed repository for storing, sharing, and retrieving ML features. Supports both online (low-latency) and offline (batch) access.

**Fine-tuning (Bedrock)**
Adapting a foundation model to a specific use case using labeled prompt-completion pairs. Reduces prompt size and improves domain-specific performance.

**Foundation Model**
A large pre-trained ML model (typically a transformer) that can be adapted to many downstream tasks. Examples: Claude, Titan, Llama, Stable Diffusion.

---

## G

**Gradient Boosting**
An ensemble technique that builds trees sequentially, where each tree corrects the residual errors of the previous trees. Used by XGBoost and LightGBM.

**Gradient Descent**
An optimization algorithm that iteratively updates model parameters in the direction that reduces the loss function.

**Guardrails (Bedrock)**
Bedrock controls that filter content in prompts and responses. Features include topic denial, word filters, PII masking, and contextual grounding checks.

**GRU (Gated Recurrent Unit)**
A simpler alternative to LSTM for sequential data. Uses gating mechanisms to control information flow but with fewer parameters.

---

## H

**Hallucination**
When an LLM generates text that is factually incorrect or fabricated with apparent confidence. RAG and contextual grounding checks help reduce hallucinations.

**Hyperparameter**
A configuration value set before training that controls the learning process (e.g., learning rate, batch size, max depth). Distinct from model parameters learned during training.

**Hyperparameter Tuning (SageMaker)**
SageMaker's automated hyperparameter optimization service that runs multiple training jobs in parallel to find the best combination of hyperparameter values.

---

## I

**IAM (Identity and Access Management)**
An AWS service for managing who can access what resources. Core components: users, groups, roles, and policies.

**IAM Execution Role (SageMaker)**
An IAM role assumed by SageMaker to access AWS resources (S3, ECR, KMS, CloudWatch) on your behalf. Should follow least privilege principles.

**Inference Pipeline (SageMaker)**
A SageMaker feature that chains 2–15 containers into a single endpoint for multi-step inference (e.g., preprocessing → model → postprocessing).

**Inference Recommender (SageMaker)**
A SageMaker tool that benchmarks instance types to recommend the optimal configuration for an inference endpoint based on latency and cost requirements.

---

## K

**KMS (AWS Key Management Service)**
An AWS service for creating and managing cryptographic keys used to encrypt data at rest. Supports AWS-managed keys and customer-managed keys (CMK).

**Knowledge Base (Bedrock)**
A managed Bedrock RAG implementation that indexes documents into a vector store for automatic retrieval during inference.

---

## L

**L1 Regularisation (Lasso)**
A regularisation technique that adds the sum of the absolute values of weights as a penalty to the loss function. Tends to produce sparse models (feature selection).

**L2 Regularisation (Ridge / Weight Decay)**
A regularisation technique that adds the sum of squared weights as a penalty. Keeps all features but with smaller, smoother weights.

**Learning Rate**
A hyperparameter controlling the step size during gradient descent. Too high overshoots optima; too low converges slowly.

**LightGBM**
A gradient-boosted decision tree algorithm optimised for speed and memory efficiency. Supports classification, regression, and ranking. Available as a SageMaker built-in algorithm.

**Linear Learner (SageMaker)**
A SageMaker built-in algorithm for regression and classification using linear models. Trains multiple models in parallel and selects the best via validation.

**LSTM (Long Short-Term Memory)**
A type of RNN cell with gates that preserve both short-term and long-term state. Addresses the vanishing gradient problem in deep sequential models.

---

## M

**Masked Self-Attention**
An attention mechanism used in decoder-only transformers (e.g., GPT) that prevents positions from attending to future tokens during training. Enables autoregressive generation.

**Max Tokens**
The maximum number of tokens allowed in an LLM's input or output. Limits context and response length.

**MICE (Multiple Imputation by Chained Equations)**
An advanced missing data imputation method that uses regression models to iteratively impute missing values across multiple passes.

**MLOps**
The practice of combining ML and DevOps to automate the deployment, monitoring, and maintenance of ML models in production.

**Model Drift**
Degradation in a deployed model's prediction quality over time due to changes in the underlying data distribution or target relationship.

**Model Monitor (SageMaker)**
A SageMaker feature that continuously monitors deployed endpoints for data quality drift, model quality drift, bias drift, and feature attribution drift.

**Model Registry (SageMaker)**
A SageMaker catalog for versioning, tracking, and managing approved ML models across the lifecycle.

**Multi-Head Attention**
An attention mechanism that runs multiple attention operations in parallel (heads), each learning different relationships. The outputs are concatenated and projected.

---

## N

**Named Entity Recognition (NER)**
An NLP task that identifies and classifies named entities (people, places, organizations, etc.) in text. Amazon Comprehend supports both general-purpose and custom NER.

**Neo (SageMaker)**
A SageMaker compiler and runtime that optimises models for specific hardware targets (cloud instances and edge devices).

**Network Isolation (`enable_network_isolation=True`)**
A SageMaker training configuration that blocks all outbound internet access from the training container.

---

## O

**Overfitting**
When a model learns the training data too well, including noise, and fails to generalise to new data. Signs: high training accuracy, low validation accuracy.

**Oversampling**
Increasing the size of the minority class by duplicating its samples. Less sophisticated than SMOTE.

---

## P

**PCA (Principal Component Analysis)**
An unsupervised dimensionality reduction technique that projects data onto the directions of maximum variance (principal components).

**Pipe Mode**
A SageMaker input mode that streams data directly from S3 to the training container without downloading it first.

**Positional Encoding**
A technique used in transformers to inject token position information into embeddings, since transformers have no inherent notion of sequence order.

**Precision**
The proportion of positive predictions that are actually positive. TP / (TP + FP). Optimise when false positives are costly.

**PrivateLink (AWS)**
An AWS service that provides private connectivity between VPCs and AWS services without routing traffic over the public internet.

**Production Variants (SageMaker)**
Multiple model versions running simultaneously behind a single SageMaker endpoint, each with a weight determining its traffic share. Used for A/B testing.

---

## R

**RAG (Retrieval-Augmented Generation)**
An LLM technique that retrieves relevant information from an external knowledge source before generating a response. Reduces hallucinations for knowledge the model was not trained on.

**Recall (Sensitivity)**
The proportion of actual positives that are correctly identified. TP / (TP + FN). Optimise when false negatives are costly.

**Recurrent Neural Network (RNN)**
A neural network architecture designed for sequential data. Maintains a hidden state that captures information from previous inputs.

**Regularisation**
Techniques that reduce overfitting by adding constraints or penalties to the model training process. Examples: L1, L2, dropout, early stopping.

**ReLU (Rectified Linear Unit)**
An activation function that outputs the input if positive, and zero otherwise. Fast and widely used; can suffer from dying ReLU.

**Reserved Instance**
An EC2 pricing model where you commit to a one- or three-year term in exchange for significantly reduced hourly rates. Used for long-running EMR or SageMaker workloads.

---

## S

**SageMaker Clarify**
A SageMaker tool that detects bias in training data and model predictions, and provides explainability using SHAP values.

**SageMaker Model Cards**
Documentation artifacts in SageMaker that capture model purpose, training details, evaluation results, and known limitations.

**SageMaker Pipelines**
A CI/CD service for ML that automates the end-to-end ML workflow from data preparation through deployment.

**Self-Attention**
An attention mechanism where each token in a sequence attends to all other tokens to build context-aware representations.

**Semantic Search**
Search based on meaning rather than exact keyword matches. Implemented using vector embeddings and approximate nearest-neighbour search.

**Seq2Seq (SageMaker)**
A SageMaker built-in algorithm for sequence-to-sequence tasks: translation, summarisation, and speech-to-text. Uses RecordIO-Protobuf with integer tokens.

**SHAP (SHapley Additive exPlanations)**
A game-theory-based method for explaining ML model predictions by attributing each prediction to individual features. Used by SageMaker Clarify.

**Shared Responsibility Model**
AWS's framework dividing security responsibilities: AWS is responsible for security *of* the cloud (hardware, infrastructure); customers are responsible for security *in* the cloud (data, IAM, network config).

**SMOTE (Synthetic Minority Oversampling Technique)**
A technique that generates new synthetic minority-class samples using nearest-neighbour interpolation. Generally outperforms simple oversampling.

**Softmax**
An activation function used on the output layer of multiclass classifiers. Converts logits into a probability distribution where all values sum to 1.

**Spot Instance**
An EC2 pricing model offering up to 90% savings by using spare AWS capacity. Can be interrupted. Best suited for task nodes in EMR and training jobs in SageMaker with checkpointing.

---

## T

**Temperature**
An LLM parameter that controls randomness in token selection. Higher temperature → more creative/varied outputs. Lower temperature → more deterministic outputs.

**Tokenisation**
The process of converting text into tokens (numerical representations). Tokens can represent words, subwords, or characters.

**Top-k Sampling**
An LLM decoding strategy that restricts token selection to the top-k most probable tokens.

**Top-p Sampling (Nucleus Sampling)**
An LLM decoding strategy that selects tokens from the smallest set whose cumulative probability exceeds threshold p.

**Transfer Learning**
Adapting a pre-trained model to a new but related task by fine-tuning some or all of its parameters on task-specific data.

**Transformer**
A deep learning architecture introduced in *Attention Is All You Need* (2017) that uses self-attention instead of recurrence. The foundation of modern LLMs.

**True Negative (TN)**
A prediction where the model predicted negative and the true label is also negative.

**True Positive (TP)**
A prediction where the model predicted positive and the true label is also positive.

---

## U

**Underfitting**
When a model is too simple to capture the underlying patterns in the data. Signs: low training accuracy and low validation accuracy.

**Undersampling**
Reducing the majority class by removing samples. Discards information but can rebalance a dataset for imbalanced classification.

---

## V

**Vanishing Gradient**
A problem in deep networks where gradients become extremely small during backpropagation, slowing or stopping learning in early layers. Addressed by LSTM, ResNets, and ReLU activations.

**Vector Store / Vector Database**
A database that stores and retrieves embeddings based on similarity. Used as the knowledge base backing store in RAG applications. Examples: OpenSearch, Pinecone, Weaviate.

**VPC (Virtual Private Cloud)**
An isolated virtual network on AWS where you can launch resources with controlled networking. SageMaker can be launched within your own VPC for network isolation.

**VPC Endpoint (PrivateLink)**
A VPC component that allows private connectivity to AWS services (S3, Bedrock, SageMaker API) without traffic leaving the AWS network.

---

## W

**Weight Decay**
Another name for L2 regularisation. The `wd` hyperparameter in SageMaker Linear Learner controls this.

---

## X

**XGBoost**
An optimised gradient-boosted decision tree algorithm popular for structured/tabular data. Supports regression, classification, and ranking. Available as a SageMaker built-in algorithm and standalone framework.
