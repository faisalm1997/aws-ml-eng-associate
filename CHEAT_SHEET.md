# AWS ML Engineer Associate — Cheat Sheet

A condensed reference for exam day. Use this for last-minute review.

---

## SageMaker Built-in Algorithms — Quick Reference

| Algorithm | Problem Type | Input Format | Key Hyperparameters | Notes |
|---|---|---|---|---|
| **Linear Learner** | Regression, Binary/Multiclass Classification | RecordIO-protobuf (preferred), CSV | `learning_rate`, `mini_batch_size`, `l1`, `wd` (L2), `target_recall`, `target_precision` | Normalises features automatically; multiple models trained in parallel |
| **XGBoost** | Regression, Classification, Ranking | CSV, libSVM, RecordIO-Protobuf, Parquet | `eta`, `max_depth`, `subsample`, `alpha` (L1), `lambda` (L2), `scale_pos_weight`, `eval_metric` | Use `scale_pos_weight` for imbalanced data; M5 instances preferred |
| **LightGBM** | Classification, Regression, Ranking | CSV, text | `learning_rate`, `num_leaves`, `max_depth`, `min_data_in_leaf`, `feature_fraction`, `bagging_fraction` | Faster than XGBoost on large datasets; CPU-optimised |
| **Seq2Seq** | Translation, Summarisation, Speech-to-text | RecordIO-Protobuf (integer tokens) + vocab files | — | Needs training + validation + vocabulary files; GPU (P3) required |

---

## SageMaker Input Modes

| Mode | Description | Best For |
|---|---|---|
| **S3 File Mode** | Copies all data from S3 to container before training | Default; small-to-medium datasets |
| **S3 Fast File Mode** | Streams from S3; training can start before full download | Sequential access on large datasets |
| **Pipe Mode** | Streams data directly from S3 | Large datasets with sequential access |
| **S3 Express One Zone** | High-performance single-AZ storage | Ultra-low latency; works with all modes |
| **FSx for Lustre** | High-throughput, low-latency; requires VPC | Very large datasets with random access |
| **EFS** | Data must already be in EFS; requires VPC | Shared datasets across jobs |

---

## SageMaker Inference Options

| Option | Latency | Use Case | Notes |
|---|---|---|---|
| **Real-time Inference** | Low (ms) | Interactive, synchronous requests | Persistent endpoint; auto scaling supported |
| **Serverless Inference** | Variable (cold starts) | Sporadic/unpredictable traffic | Pay per invocation; 1024–6144 MB memory |
| **Async Inference** | Minutes | Large payloads, long processing | Requests queued; results in S3 |
| **Batch Transform** | No real-time | Offline large-scale inference | No persistent endpoint; most cost-effective for bulk |

---

## SageMaker Deployment Strategies

| Strategy | Description | Rollback |
|---|---|---|
| **Blue/Green** | Full traffic shift from old to new environment | Manual or automatic |
| **Canary** | Small % to new model first, then full rollout | Automatic via CloudWatch alarm |
| **Linear** | Incremental equal steps (e.g., 10% every 5 min) | Automatic via CloudWatch alarm |
| **Shadow Test** | New model receives copy of traffic; responses not returned to users | N/A — comparison only |
| **A/B Test (Production Variants)** | Multiple models run simultaneously with variant weights | Manual weight adjustment |

---

## AWS Managed AI Services — When to Use What

| Service | Primary Use Case | Key Differentiator |
|---|---|---|
| **Comprehend** | NLP — sentiment, entities, key phrases, topic modelling | Custom classification + custom NER |
| **Translate** | Language translation at scale | Batch translation of large text volumes |
| **Transcribe** | Speech to text | Custom vocabularies + custom language models for accuracy |
| **Polly** | Text to speech | SSML, lexicons, multiple voice engines |
| **Rekognition** | Image/video analysis, content moderation | `DetectModerationLabels` for content moderation |
| **Lex** | Chatbots, conversational AI | Intent detection + Lambda fulfillment |
| **Personalize** | Real-time recommendations | Prebuilt recipes; no ML expertise needed |
| **Textract** | Extract text, forms, tables from documents | Structured extraction from PDFs/scans |
| **Kendra** | Enterprise search | Natural language search over documents |
| **Augmented AI (A2I)** | Human review of ML predictions | Integrates with Rekognition, Textract, or custom models |
| **Q for Business** | Employee GenAI assistant | 40+ connectors; respects existing access controls |

---

## Amazon Bedrock — Core Concepts

| Concept | Key Points |
|---|---|
| **Fine-tuning** | Requires labeled prompt-completion pairs; reduces prompt size; domain-specific tasks |
| **Continued Pre-training** | Uses unlabeled data; teaches model domain corpus; no explicit labels needed |
| **RAG** | Retrieval-augmented generation; open-book exam for LLMs; faster/cheaper than fine-tuning for new info |
| **Knowledge Bases** | Managed RAG; needs embedding model + vector store + chunking strategy |
| **Guardrails** | Topic denial, word filters, PII masking, contextual grounding checks (anti-hallucination) |
| **Agents** | Plan + remember + use tools; action groups = Lambda functions; `InvokeAgent` API |
| **Chunking — Semantic** | FM groups related sentences together |
| **Chunking — Hierarchical** | Small child chunks under larger parent chunks |
| **Chunking — Lambda** | Custom chunking logic via Lambda |

---

## Bedrock API Endpoints

| Endpoint | Purpose |
|---|---|
| `bedrock` | Manage, deploy, train models |
| `bedrock-runtime` | Run inference (`InvokeModel`) |
| `bedrock-agent` | Manage agents and knowledge bases |
| `bedrock-agent-runtime` | Invoke agents (`InvokeAgent`, `Retrieve`, `RetrieveAndGenerate`) |

---

## Evaluation Metrics — Quick Reference

| Metric | Formula | When to Use |
|---|---|---|
| **Accuracy** | (TP + TN) / Total | Balanced classes only |
| **Precision** | TP / (TP + FP) | Minimise false positives (e.g., spam detection) |
| **Recall (Sensitivity)** | TP / (TP + FN) | Minimise false negatives (e.g., disease detection) |
| **F1 Score** | 2 × (Precision × Recall) / (Precision + Recall) | Imbalanced classes; balance precision and recall |
| **AUC-ROC** | Area under ROC curve | Overall classifier performance; threshold-independent |
| **RMSE** | √(mean of squared errors) | Regression; penalises large errors |

---

## Regularisation Techniques

| Technique | How It Works | Effect |
|---|---|---|
| **L1 (Lasso)** | Adds sum of absolute weights as penalty | Feature selection; creates sparsity |
| **L2 (Ridge / Weight Decay)** | Adds sum of squared weights as penalty | Keeps all features; smooths weights |
| **Dropout** | Randomly removes neurons during training | Forces robust representations |
| **Early Stopping** | Stop when validation loss stops improving | Prevents overfitting without changing architecture |

---

## Activation Functions

| Function | Range | Best For |
|---|---|---|
| **ReLU** | [0, ∞) | Default for hidden layers; fast |
| **Leaky ReLU** | (-∞, ∞) | Fixes dying ReLU problem |
| **Sigmoid** | (0, 1) | Binary output; avoid in deep hidden layers |
| **Tanh** | (-1, 1) | RNN hidden layers; preferred over sigmoid |
| **Softmax** | (0, 1) per class, sums to 1 | Multiclass output layer only |

---

## Handling Common Data Problems

| Problem | Solution |
|---|---|
| Missing numeric values (skewed) | Median replacement |
| Missing numeric values (normal) | Mean replacement |
| Missing categorical values | Mode replacement or ML imputation |
| Class imbalance | SMOTE, oversampling, undersampling, `scale_pos_weight` |
| Too many features | PCA, feature selection |
| Outliers | Remove from training; use median; Random Cut Forest for detection |
| Sparse data (dimensionality) | PCA, k-means clustering for compression |

---

## Security — Key Exam Points

| Scenario | Answer |
|---|---|
| Keep SageMaker traffic off the public internet | VPC endpoints (PrivateLink) + VPC mode |
| Block all outbound internet from training container | `enable_network_isolation=True` |
| Encrypt data between distributed training nodes | `enable_inter_container_traffic_encryption=True` |
| Discover PII in S3 training data | Amazon Macie |
| Audit all SageMaker API calls | AWS CloudTrail |
| Enforce least privilege on training job | Scope SageMaker Execution Role to specific resources |
| Encrypt training data, model artifacts, EBS | AWS KMS (customer-managed or AWS-managed keys) |
| Sensitive Bedrock data stays in private network | VPC + PrivateLink for Bedrock |

---

## MLOps — Key Exam Points

| Scenario | Answer |
|---|---|
| Save money on training jobs | Managed Spot Instances (up to 90% savings) |
| Spot Instance interrupted mid-training | Checkpointing to S3 + `MaxWaitTimeInSeconds` |
| Detect data drift on live endpoint | SageMaker Model Monitor |
| Explain model predictions with feature contributions | SageMaker Clarify (SHAP values) |
| Find the best instance type for inference | SageMaker Inference Recommender |
| Chain preprocessing + model + postprocessing | SageMaker Inference Pipeline (2–15 containers) |
| Deploy model to edge devices | SageMaker Neo + IoT Greengrass |
| Version and share models across teams | SageMaker Model Registry |
| Automate the full ML pipeline | SageMaker Pipelines |

---

## Responsible AI — AWS Tools

| Pillar | AWS Tool |
|---|---|
| Fairness / Bias detection | SageMaker Clarify |
| Explainability (SHAP) | SageMaker Clarify |
| Model documentation | SageMaker Model Cards |
| Production drift monitoring | SageMaker Model Monitor |
| PII protection in S3 | Amazon Macie |
| Human review of predictions | Amazon Augmented AI (A2I) |

---

## EMR — Key Points

| Concept | Key Detail |
|---|---|
| Master node | Manages the cluster; single EC2 instance |
| Core node | Stores HDFS data + runs tasks; scaling down risks data loss |
| Task node | Runs tasks only; no data stored; safe for Spot Instances |
| Transient cluster | Shut down after job; best for occasional workloads |
| Long-running cluster | Use Reserved Instances for cost savings |
| EMR Serverless | No cluster management; auto-scales; variable workloads |
| EMRFS | Access S3 as if it were HDFS |
| Spark MLlib | ML within EMR: classification, regression, clustering (k-means), LDA |
