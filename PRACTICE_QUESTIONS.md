# AWS ML Engineer Associate — Practice Questions

Questions are grouped by domain. Each question is scenario-based, matching the style of the actual exam.

---

## Domain 1: Data Preparation and Feature Engineering (Module 02)

**Q1.** A data scientist notices that a numeric feature has 8% missing values, and the distribution is heavily right-skewed due to outliers. What is the most appropriate imputation strategy?

- A. Mean replacement
- B. Drop all rows with missing values
- C. Median replacement
- D. Replace with zero

<details><summary>Answer</summary>

**C — Median replacement.**
Median is preferred over mean when outliers are present because the mean is pulled toward extreme values. Dropping rows risks introducing bias if missingness is not random.

</details>

---

**Q2.** A training dataset for a fraud detection model has 98% negative examples and 2% positive examples. The initial model flags almost nothing as fraud. Which approach best addresses this?

- A. Train for more epochs
- B. Use SMOTE to synthesise minority-class samples
- C. Increase the learning rate
- D. Add more features

<details><summary>Answer</summary>

**B — SMOTE.**
SMOTE synthetically generates new minority-class samples using nearest neighbours and generally outperforms simple oversampling (duplication). The class imbalance is the root cause of the model ignoring the minority class.

</details>

---

**Q3.** A team uses Amazon EMR with Spark for a nightly batch transformation. The cluster is only needed for two hours each night. What is the most cost-effective setup?

- A. Long-running cluster with Reserved Instances for all node types
- B. Transient cluster with Spot Instances on task nodes
- C. Long-running cluster with On-Demand Instances
- D. Transient cluster with Reserved Instances

<details><summary>Answer</summary>

**B — Transient cluster with Spot Instances on task nodes.**
Transient clusters are shut down after the job, avoiding idle charges. Spot Instances on task nodes (which do not store HDFS data) reduce cost without risking data loss.

</details>

---

**Q4.** A feature engineering pipeline needs to reduce 200 numerical features to a smaller set while retaining the most variance. Which technique is most appropriate?

- A. One-hot encoding
- B. SMOTE
- C. PCA
- D. Binning

<details><summary>Answer</summary>

**C — PCA.**
Principal Component Analysis is an unsupervised dimensionality reduction technique that compresses many features into fewer components ranked by explained variance.

</details>

---

**Q5.** A developer wants to run Spark-based feature engineering without managing an EMR cluster. Which AWS service best fits this requirement?

- A. EMR on EC2
- B. EMR Serverless
- C. AWS Glue
- D. SageMaker Processing

<details><summary>Answer</summary>

**B — EMR Serverless.**
EMR Serverless runs Spark, Hive, or Presto applications without provisioning or managing clusters. It auto-scales and is billed per use, making it ideal for variable workloads.

</details>

---

## Domain 2: AWS Managed AI Services (Module 03)

**Q6.** A company wants to extract structured data (forms and tables) from scanned PDF invoices. Which AWS service should they use?

- A. Amazon Comprehend
- B. Amazon Rekognition
- C. Amazon Textract
- D. Amazon Transcribe

<details><summary>Answer</summary>

**C — Amazon Textract.**
Textract is specifically designed to extract text, handwriting, forms, and tables from scanned documents and PDFs, going beyond simple OCR.

</details>

---

**Q7.** A media company wants to detect violent and explicit content in uploaded videos before publishing. Which service should they use, and which API call is relevant?

- A. Amazon Rekognition — `DetectLabels`
- B. Amazon Rekognition — `DetectModerationLabels`
- C. Amazon Comprehend — Custom Classification
- D. Amazon Textract — `AnalyzeDocument`

<details><summary>Answer</summary>

**B — Amazon Rekognition — `DetectModerationLabels`.**
`DetectModerationLabels` is the API specifically designed for content moderation of images and videos, detecting explicit, violent, and disturbing content.

</details>

---

**Q8.** A call centre wants to transcribe customer calls and improve accuracy for internal jargon and product names. Which two Transcribe features should they use?

- A. Sentiment analysis and PII redaction
- B. Custom vocabularies and custom language models
- C. Speaker diarization and toxicity detection
- D. Lexicons and SSML

<details><summary>Answer</summary>

**B — Custom vocabularies and custom language models.**
Custom vocabularies hint new or uncommon words to the model. Custom language models learn domain-specific context from your own text data. Lexicons and SSML belong to Amazon Polly, not Transcribe.

</details>

---

**Q9.** A company needs to give employees a GenAI assistant that can answer questions based on internal HR documents, Jira tickets, and Salesforce data, while respecting existing access controls. Which service best fits this?

- A. Amazon Bedrock with a custom agent
- B. Amazon Q for Business
- C. Amazon Kendra with custom connectors
- D. SageMaker JumpStart

<details><summary>Answer</summary>

**B — Amazon Q for Business.**
Amazon Q for Business connects to 40+ enterprise data sources, enforces user-level access controls through IAM Identity Center, and is purpose-built for employee-facing GenAI assistants.

</details>

---

**Q10.** A developer wants to build a chatbot that can detect user intent and invoke different AWS Lambda functions based on context. Which service is most appropriate?

- A. Amazon Comprehend
- B. Amazon Lex
- C. Amazon Kendra
- D. Amazon Personalize

<details><summary>Answer</summary>

**B — Amazon Lex.**
Amazon Lex is specifically designed to build conversational interfaces (chatbots) with intent recognition and slot filling. It integrates natively with Lambda to fulfill intents.

</details>

---

## Domain 3: SageMaker Algorithms (Module 04)

**Q11.** A machine learning engineer needs to train a binary classification model on tabular data. The data is stored in CSV format and the engineer wants to use a built-in SageMaker algorithm. The training data has class imbalance. Which algorithm and hyperparameter combination is most appropriate?

- A. XGBoost with `scale_pos_weight` set to the ratio of negative to positive samples
- B. Linear Learner with `target_recall` set to 0.9
- C. LightGBM with `bagging_fraction` set to 0.5
- D. Seq2Seq with `eval_metric` set to AUC

<details><summary>Answer</summary>

**A — XGBoost with `scale_pos_weight`.**
`scale_pos_weight` is the standard XGBoost hyperparameter for handling imbalanced binary classification data. It sets the ratio of negative to positive examples to rebalance the loss function.

</details>

---

**Q12.** A model trained with SageMaker's XGBoost is overfitting on the training set. Which hyperparameters should be tuned to address this? (Select TWO)

- A. `eta` (learning rate)
- B. `num_class`
- C. `subsample`
- D. `eval_metric`
- E. `max_depth`

<details><summary>Answer</summary>

**A and C (or C and E).**
`subsample` controls the fraction of training data used per tree (lower values reduce overfitting). `max_depth` controls tree complexity (lower values reduce overfitting). `eta` can also help by slowing learning. `eval_metric` and `num_class` do not directly control overfitting.

</details>

---

**Q13.** A team needs to train a machine translation model using SageMaker's built-in Seq2Seq algorithm. What input format is required?

- A. CSV with the source and target sentences as separate columns
- B. RecordIO-Protobuf with integer tokens, plus training, validation, and vocabulary files
- C. JSON Lines with source and target pairs
- D. Parquet with tokenised embeddings

<details><summary>Answer</summary>

**B — RecordIO-Protobuf with integer tokens.**
Seq2Seq requires RecordIO-Protobuf format with integer tokens. It also needs separate training data, validation data, and vocabulary files. GPU instances (e.g., P3) are typically used.

</details>

---

**Q14.** A SageMaker training job is reading large files from S3. Training starts slowly because data must be fully downloaded before training begins. Which input mode should be used to allow training to start before the download is complete?

- A. S3 File Mode
- B. S3 Fast File Mode
- C. Amazon EFS
- D. Amazon S3 Express One Zone

<details><summary>Answer</summary>

**B — S3 Fast File Mode.**
S3 Fast File Mode is similar to Pipe Mode: training can begin before data is fully downloaded and it works best with sequential data access patterns.

</details>

---

## Domain 4: Model Training, Tuning and Evaluation (Module 05)

**Q15.** A deep learning model is training but loss stops decreasing after a few epochs. The validation loss has been increasing for 5 epochs. Which technique should be applied first?

- A. Add more layers to the network
- B. Increase the learning rate
- C. Apply early stopping
- D. Switch from ReLU to sigmoid activations

<details><summary>Answer</summary>

**C — Early stopping.**
The described pattern (training loss plateauing while validation loss increases) is classic overfitting. Early stopping terminates training when validation performance stops improving, preventing further overfitting.

</details>

---

**Q16.** A model for medical diagnosis needs to minimise false negatives (missed diagnoses) even at the cost of more false positives. Which metric should be optimised?

- A. Precision
- B. Specificity
- C. Recall
- D. F1 score

<details><summary>Answer</summary>

**C — Recall.**
Recall (sensitivity) measures the proportion of actual positives correctly identified. Minimising false negatives (missed diagnoses) means maximising recall. In SageMaker Linear Learner, `target_precision` can hold precision while maximising recall.

</details>

---

**Q17.** A neural network for time series prediction is not learning long-term dependencies. The team suspects vanishing gradients. Which architectural change best addresses this?

- A. Replace ReLU with sigmoid
- B. Add dropout layers
- C. Use LSTM cells instead of simple RNN cells
- D. Increase batch size

<details><summary>Answer</summary>

**C — Use LSTM cells.**
LSTM (Long Short-Term Memory) cells are specifically designed to preserve long-term state and mitigate the vanishing gradient problem in sequential models.

</details>

---

**Q18.** A multiclass classification model outputs raw logits from its final layer. Which activation function should be applied to convert these into class probabilities that sum to 1?

- A. ReLU
- B. Sigmoid
- C. Tanh
- D. Softmax

<details><summary>Answer</summary>

**D — Softmax.**
Softmax is used on the output layer of multiclass classifiers to convert logits into a probability distribution where all class probabilities sum to 1.

</details>

---

## Domain 5: Generative AI Fundamentals (Module 06)

**Q19.** A developer wants to make an LLM's responses more creative and varied. Which parameter should be increased?

- A. Top-k
- B. Max tokens
- C. Temperature
- D. Context window

<details><summary>Answer</summary>

**C — Temperature.**
Temperature controls randomness in token selection. Higher temperature makes output more varied and creative; lower temperature makes it more deterministic and focused.

</details>

---

**Q20.** A pre-trained transformer model performs well on general text but poorly on a company's internal legal documents. The company wants to adapt the model without providing explicit input-output pairs. Which approach is most appropriate?

- A. Fine-tuning with prompt-completion pairs
- B. Continued pre-training on unlabelled internal documents
- C. RAG with a vector store
- D. Prompt engineering with few-shot examples

<details><summary>Answer</summary>

**B — Continued pre-training on unlabelled internal documents.**
Continued pre-training exposes the model to more domain text without requiring labeled pairs. Fine-tuning requires labeled prompt-completion data. RAG retrieves at inference time but does not update model weights.

</details>

---

**Q21.** GPT-style models use a decoder-only architecture. Which attention mechanism prevents the model from attending to future tokens during training?

- A. Multi-head attention
- B. Cross-attention
- C. Masked self-attention
- D. Positional encoding

<details><summary>Answer</summary>

**C — Masked self-attention.**
Masked self-attention applies a causal mask that prevents each position from attending to future positions. This is essential for autoregressive generation where the model predicts the next token only from previous context.

</details>

---

## Domain 6: Building GenAI Apps with Bedrock (Module 07)

**Q22.** A team wants to reduce hallucinations in a Bedrock RAG application by grounding responses in retrieved documents. Which Bedrock Guardrails feature directly addresses this?

- A. Word filters
- B. Topic denial
- C. Contextual grounding checks
- D. PII masking

<details><summary>Answer</summary>

**C — Contextual grounding checks.**
Contextual grounding checks in Bedrock Guardrails compare the model response against the retrieved context to detect and reduce hallucinations.

</details>

---

**Q23.** A company wants to build a Bedrock agent that can look up order status by calling a backend API. How should the agent be configured to invoke the correct function?

- A. Define a knowledge base with order data
- B. Create an action group with a Lambda function and define the expected parameters
- C. Use continued pre-training to teach the model about the API
- D. Set up a guardrail to filter order-related topics

<details><summary>Answer</summary>

**B — Create an action group with a Lambda function.**
Action groups define the tools an agent can use. Each action group maps to a Lambda function, and the agent is told what parameters the function expects. The model uses the action group during planning to fulfill user requests.

</details>

---

**Q24.** A Bedrock application handles sensitive customer data. The team must ensure traffic between Bedrock and its data sources does not traverse the public internet. Which configuration achieves this?

- A. Enable KMS encryption on all S3 buckets
- B. Use IAM resource-based policies
- C. Configure VPC endpoints (PrivateLink) for Bedrock
- D. Enable Bedrock guardrails with PII masking

<details><summary>Answer</summary>

**C — Configure VPC endpoints (PrivateLink) for Bedrock.**
VPC endpoints using AWS PrivateLink allow traffic to remain within the AWS network, ensuring it never traverses the public internet.

</details>

---

**Q25.** Which chunking strategy in Bedrock Knowledge Bases uses a foundation model to group semantically related sentences together before creating vectors?

- A. Hierarchical chunking
- B. Lambda-based chunking
- C. Fixed-size chunking
- D. Semantic chunking

<details><summary>Answer</summary>

**D — Semantic chunking.**
Semantic chunking uses a foundation model to identify and group semantically related sentences, producing more meaningful chunks than fixed-size splitting.

</details>

---

## Domain 7: MLOps on AWS (Module 08)

**Q26.** A company is rolling out a new recommendation model. They want a small percentage of live traffic to go to the new model first, with an automatic rollback if error rates spike. Which deployment strategy should they use?

- A. Blue/green deployment with manual rollback
- B. Canary deployment with a CloudWatch alarm-based rollback
- C. Shadow testing
- D. Linear deployment without alarms

<details><summary>Answer</summary>

**B — Canary deployment with a CloudWatch alarm-based rollback.**
Canary deployments send a small traffic percentage to the new variant first. SageMaker deployment guardrails support automatic rollback when a CloudWatch alarm is triggered during the traffic shift.

</details>

---

**Q27.** A SageMaker training job is interrupted due to Spot Instance reclamation. The training must resume without restarting from scratch. Which feature must be enabled?

- A. Auto scaling
- B. Managed Spot Instances with checkpointing to S3
- C. SageMaker Inference Recommender
- D. Production variants

<details><summary>Answer</summary>

**B — Managed Spot Instances with checkpointing to S3.**
Checkpointing saves training progress to S3 at regular intervals. When a Spot Instance is reclaimed and a new one provisions, training resumes from the last checkpoint rather than epoch 0.

</details>

---

**Q28.** A production SageMaker endpoint is receiving inconsistent traffic throughout the day. The team wants the endpoint to scale down during quiet periods and scale up during peak hours automatically. What should they configure?

- A. Managed Spot Instances
- B. SageMaker Serverless Inference
- C. Auto scaling with a scaling policy targeting `SageMakerVariantInvocationsPerInstance`
- D. SageMaker Batch Transform

<details><summary>Answer</summary>

**C — Auto scaling with a scaling policy.**
SageMaker auto scaling adjusts instance count based on CloudWatch metrics. `SageMakerVariantInvocationsPerInstance` is the standard target metric for scaling real-time inference endpoints.

</details>

---

**Q29.** A data scientist deployed a model that performed well at launch, but prediction quality has degraded over the past month as customer behaviour has changed. Which SageMaker feature would have detected this earliest?

- A. SageMaker Debugger
- B. SageMaker Experiments
- C. SageMaker Model Monitor — model quality drift
- D. SageMaker Clarify

<details><summary>Answer</summary>

**C — SageMaker Model Monitor — model quality drift.**
SageMaker Model Monitor continuously monitors deployed endpoints and can detect model quality drift (degrading prediction accuracy) and data quality drift (statistical changes in input features) against an established baseline.

</details>

---

**Q30.** A team needs to run inference on a batch of 10 million records overnight with no latency requirements. Which SageMaker inference option is most cost-effective?

- A. Real-time inference endpoint
- B. Serverless inference
- C. Async inference
- D. Batch transform

<details><summary>Answer</summary>

**D — Batch transform.**
Batch transform runs inference on large datasets offline, processes them in batches, and stores results in S3. There is no persistent endpoint to pay for after the job completes, making it the most cost-effective option for large offline workloads.

</details>

---

## Domain 8: Security (Module 09)

**Q31.** A SageMaker training job must not have any outbound internet access, and all traffic to S3 must stay within the AWS network. Which two configurations achieve this?

- A. Enable network isolation (`enable_network_isolation=True`) and configure a VPC endpoint for S3
- B. Use security groups to block port 80 and port 443
- C. Enable KMS encryption and use customer-managed keys
- D. Set IAM policies to deny internet access

<details><summary>Answer</summary>

**A — Enable network isolation and configure a VPC endpoint for S3.**
`enable_network_isolation=True` blocks all outbound internet traffic from the training container. A VPC endpoint for S3 (via PrivateLink) ensures S3 traffic stays within the AWS network without traversing the internet.

</details>

---

**Q32.** A security team wants to automatically discover and classify PII stored in S3 buckets used for ML training data. Which AWS service should they use?

- A. AWS Config
- B. Amazon Macie
- C. Amazon Inspector
- D. AWS Trusted Advisor

<details><summary>Answer</summary>

**B — Amazon Macie.**
Amazon Macie uses ML to automatically discover, classify, and protect sensitive data (including PII) in S3. It is the go-to service for data classification in S3.

</details>

---

**Q33.** A SageMaker execution role currently has `s3:*` on `*` (all S3 resources). A security review flags this as a violation. What is the correct remediation?

- A. Switch from an IAM Role to an IAM User
- B. Apply a permission boundary capping the maximum permissions
- C. Scope the S3 actions to specific buckets using resource ARNs
- D. Enable MFA on the IAM Role

<details><summary>Answer</summary>

**C — Scope S3 actions to specific resource ARNs.**
The principle of least privilege requires scoping permissions to the minimum necessary resources. Replacing `*` with specific bucket ARNs (`arn:aws:s3:::my-training-bucket/*`) removes access to all other S3 buckets.

</details>

---

## Domain 9: Management and Governance (Module 10)

**Q34.** A SageMaker endpoint is experiencing intermittent high latency. The team wants to be alerted automatically when `ModelLatency` exceeds 500ms for more than 5 minutes. Which service should they configure?

- A. AWS CloudTrail
- B. Amazon CloudWatch Alarm
- C. AWS Config Rule
- D. SageMaker Model Monitor

<details><summary>Answer</summary>

**B — Amazon CloudWatch Alarm.**
CloudWatch Alarms trigger on metric thresholds over specified evaluation periods. `ModelLatency` is a native SageMaker endpoint metric, making a CloudWatch Alarm the right choice for threshold-based alerting.

</details>

---

**Q35.** A compliance team needs an audit trail showing who called `CreateTrainingJob` on SageMaker, from which IP address, and at what time. Which service provides this?

- A. Amazon CloudWatch Logs
- B. AWS CloudTrail
- C. SageMaker Model Monitor
- D. AWS Config

<details><summary>Answer</summary>

**B — AWS CloudTrail.**
CloudTrail records all AWS API calls including who made them, from where, and when. It is the primary service for security auditing and compliance investigations.

</details>

---

## Domain 10: ML Best Practices (Module 11)

**Q36.** A model predicts loan approvals and produces a higher denial rate for one demographic group. A stakeholder wants to understand which features drive this disparity. Which AWS tool provides both bias metrics and per-feature contribution scores?

- A. SageMaker Model Monitor
- B. SageMaker Debugger
- C. SageMaker Clarify
- D. Amazon Augmented AI

<details><summary>Answer</summary>

**C — SageMaker Clarify.**
SageMaker Clarify detects pre-training and post-training bias using metrics like Disparate Impact and Class Imbalance. It also uses SHAP values to provide feature-level explanations for model predictions.

</details>

---

**Q37.** A financial services company wants to flag low-confidence fraud predictions for human review before taking action. Which AWS service adds a human review step to an ML pipeline?

- A. Amazon Rekognition Custom Labels
- B. Amazon Augmented AI (A2I)
- C. SageMaker Model Monitor
- D. Amazon Comprehend Custom Classification

<details><summary>Answer</summary>

**B — Amazon Augmented AI (A2I).**
A2I is designed specifically to add human review workflows to ML predictions. It routes low-confidence or high-risk predictions to human reviewers before a final decision is made.

</details>

---

**Q38.** After deploying a model to production, prediction accuracy drops significantly six weeks later without any code changes. What is the most likely root cause?

- A. The model has too many parameters
- B. Data drift — the statistical distribution of production inputs has shifted from the training distribution
- C. The learning rate was too high during training
- D. The model was undertrained due to early stopping

<details><summary>Answer</summary>

**B — Data drift.**
Model degradation over time without code changes is a classic symptom of data drift (also called concept drift or covariate shift). The real-world data distribution has changed since the model was trained. SageMaker Model Monitor detects this automatically.

</details>

---

## Bonus: Cross-Domain Scenario Questions

**Q39.** A startup is building a product recommendation engine. They have a small team and want the fastest path to real-time recommendations without building custom ML models. Which AWS service should they use?

- A. SageMaker with XGBoost
- B. Amazon Personalize
- C. Amazon Kendra
- D. SageMaker Autopilot

<details><summary>Answer</summary>

**B — Amazon Personalize.**
Amazon Personalize is a fully managed recommendation service with prebuilt recipes for user personalization, related items, and popular items. It provides a real-time API with no ML expertise required.

</details>

---

**Q40.** A team needs to ensure their Bedrock-powered application never discusses competitor products and always responds in formal English. Which Bedrock feature should they configure?

- A. Fine-tuning with labeled examples of formal responses
- B. Continued pre-training on formal text
- C. Bedrock Guardrails with topic denial and word filters
- D. A system prompt in the inference request

<details><summary>Answer</summary>

**C — Bedrock Guardrails with topic denial and word filters.**
Guardrails are the correct Bedrock mechanism for enforcing consistent topic restrictions (deny competitor mentions) and content policies. They apply to all prompts and responses through the guardrail, not just when a system prompt is included.

</details>
