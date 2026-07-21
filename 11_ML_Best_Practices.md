# 11. Machine Learning Best Practices

## Intro: Machine Learning Best Practices

Building effective ML systems goes beyond model accuracy. Best practices cover the entire lifecycle — from problem framing through to responsible deployment and ongoing monitoring. AWS provides a set of principles and tools to help teams build ML systems that are reliable, fair, secure, and cost-efficient.

Key themes in this section:
- **Responsible AI** — fairness, explainability, privacy, and accountability
- **ML Lifecycle** — a structured end-to-end process for developing ML systems
- **Well-Architected ML** — applying AWS architectural best practices to ML workloads

---

## Designing ML Systems with AWS: Responsible AI

**Responsible AI** is the practice of designing, building, and deploying ML systems in a way that is safe, fair, transparent, and accountable.

### Core Pillars of Responsible AI

| Pillar | Description |
|---|---|
| **Fairness** | Ensure model outputs do not discriminate against individuals or groups based on protected characteristics (e.g., gender, ethnicity, age) |
| **Explainability** | Make model predictions interpretable and understandable to stakeholders and end users |
| **Privacy** | Protect personally identifiable information (PII) throughout the ML lifecycle |
| **Robustness** | Ensure the model performs reliably across diverse inputs and is resistant to adversarial attacks |
| **Transparency** | Document model purpose, training data, limitations, and known biases |
| **Accountability** | Define clear ownership and governance processes for ML systems |
| **Safety** | Prevent harmful outcomes, particularly in high-stakes domains (healthcare, finance, legal) |

### AWS Tools for Responsible AI

| Tool | Purpose |
|---|---|
| **SageMaker Clarify** | Detects bias in training data and model predictions; provides feature importance explanations (SHAP values) |
| **SageMaker Model Cards** | Documents model purpose, training details, evaluation results, and known limitations |
| **SageMaker Model Monitor** | Detects data drift and model quality degradation in production |
| **Amazon Macie** | Discovers and protects PII in training datasets stored in S3 |
| **Amazon Augmented AI (A2I)** | Human review workflow for low-confidence or sensitive predictions |

### Bias Detection with SageMaker Clarify
- **Pre-training bias** — imbalance or unfairness in the training data before a model is trained
- **Post-training bias** — bias introduced or amplified by the model itself
- Common bias metrics: **Class Imbalance (CI)**, **Difference in Positive Proportions (DPP)**, **Disparate Impact (DI)**

### Explainability
- SageMaker Clarify uses **SHAP (SHapley Additive exPlanations)** to attribute prediction contributions to individual features
- Supports both global explanations (overall feature importance) and local explanations (per-prediction)

---

## ML Design Principles and Lifecycle

### ML Design Principles

1. **Start simple** — begin with a baseline model before investing in complexity
2. **Iterate quickly** — short experimentation cycles with clear evaluation criteria
3. **Data-centric thinking** — the quality and relevance of data is often more impactful than model choice
4. **Reproducibility** — version control data, code, and model artefacts
5. **Decouple components** — separate data ingestion, training, evaluation, and serving pipelines
6. **Monitor everything** — treat ML systems as living systems that degrade over time
7. **Design for failure** — build fallback mechanisms and graceful degradation into serving pipelines

### The ML Lifecycle

```
Business Goal Identification
        ↓
Problem Framing
        ↓
Data Collection & Processing
        ↓
Feature Engineering
        ↓
Model Development & Training
        ↓
Model Evaluation
        ↓
Deployment
        ↓
Monitoring & Feedback Loop
```

Each stage feeds back into previous stages — the lifecycle is iterative, not linear.

### AWS Tooling Across the Lifecycle

| Stage | AWS Service |
|---|---|
| Data collection | S3, Kinesis, Glue, Lake Formation |
| Data processing | Glue, EMR, SageMaker Processing |
| Feature engineering | SageMaker Feature Store, Glue DataBrew |
| Experimentation | SageMaker Experiments |
| Training | SageMaker Training Jobs, SageMaker Autopilot |
| Evaluation | SageMaker Clarify, SageMaker Experiments |
| Deployment | SageMaker Endpoints, Batch Transform |
| Monitoring | SageMaker Model Monitor, CloudWatch |
| MLOps / Automation | SageMaker Pipelines, SageMaker Model Registry |

---

## ML Business Goal Identification

Before writing a single line of code, you must clearly define the business problem and determine whether ML is the right solution.

### Questions to Ask

- What is the business outcome we are trying to achieve?
- What decisions will the model inform or automate?
- What does success look like — in business terms, not just model metrics?
- What is the cost of a wrong prediction (false positive vs. false negative)?
- Is there sufficient labelled data, or would rule-based systems suffice?
- What are the regulatory or ethical constraints?

### Defining Success Metrics

Align ML metrics to business KPIs:

| Business Goal | ML Metric | Business KPI |
|---|---|---|
| Reduce customer churn | AUC-ROC on churn classifier | % reduction in churned customers |
| Improve fraud detection | Precision / Recall on fraud labels | £ losses prevented per month |
| Automate document classification | F1 score on labelled categories | Hours of manual review saved |
| Recommend products | NDCG / Hit Rate | Revenue uplift per session |

### ML vs. Non-ML Decision

Use ML when:
- The problem is too complex to encode as explicit rules
- Large volumes of labelled historical data exist
- The pattern changes over time and the model can adapt

Avoid ML when:
- A simple rule or lookup table suffices
- Data is insufficient or too expensive to label
- The decision requires full human accountability (regulatory requirement)

---

## Framing the ML Problem

Once the business goal is defined, translate it into a concrete ML problem formulation.

### Problem Types

| Problem Type | Description | Example |
|---|---|---|
| **Binary Classification** | Predict one of two classes | Fraud / Not Fraud |
| **Multi-class Classification** | Predict one of N classes | Product category |
| **Multi-label Classification** | Predict multiple labels simultaneously | Tags on an article |
| **Regression** | Predict a continuous value | House price, demand forecast |
| **Ranking** | Order items by relevance | Search results, recommendations |
| **Clustering** | Group unlabelled data | Customer segmentation |
| **Anomaly Detection** | Identify unusual patterns | Network intrusion, equipment failure |
| **Time Series Forecasting** | Predict future values from historical sequences | Sales, energy demand |
| **NLP Tasks** | Text classification, NER, summarisation, translation | Customer support routing |
| **Computer Vision** | Image classification, object detection, segmentation | Defect detection |

### Defining Inputs and Outputs

- **Features (inputs)** — what data is available at inference time?
- **Label (target output)** — what are you trying to predict?
- **Data availability** — is the label available historically for supervised learning?
- **Latency requirements** — real-time inference or batch prediction?
- **Throughput requirements** — how many predictions per second?

### Train/Validation/Test Split Strategy

- **Random split** — suitable when data is i.i.d. (independently and identically distributed)
- **Time-based split** — required for time series; train on past, validate on future periods
- **Stratified split** — preserves class distribution; important for imbalanced datasets
- **Group split** — ensures no data leakage between related records (e.g., all rows for a customer go to one split)

### Data Leakage
- Occurs when information from the future or from the test set influences the model during training
- Common causes: target encoding without cross-validation, using post-event features, normalising before splitting
- Always apply preprocessing (scaling, encoding) **after** splitting, using parameters fit only on training data

---

## Data Processing

High-quality data is the foundation of a performant ML model. Data processing encompasses collection, cleaning, transformation, and validation.

### Data Quality Dimensions

| Dimension | Description |
|---|---|
| **Completeness** | Are there missing values? What proportion? |
| **Consistency** | Are values consistent across records and sources? |
| **Accuracy** | Do values reflect ground truth? |
| **Timeliness** | Is the data up-to-date and relevant? |
| **Uniqueness** | Are there duplicate records? |
| **Validity** | Do values conform to expected formats and ranges? |

### Handling Missing Data

| Strategy | When to Use |
|---|---|
| **Drop rows** | When missingness is rare and random |
| **Mean / Median imputation** | Numerical features; simple baseline |
| **Mode imputation** | Categorical features |
| **Forward / Backward fill** | Time series data |
| **Model-based imputation** | When missingness is complex (e.g., KNN, regression imputation) |
| **Indicator flag** | Add a binary column noting that a value was missing |

### Handling Imbalanced Data

- **Oversampling** — duplicate or synthesise minority class examples (e.g., **SMOTE**)
- **Undersampling** — reduce majority class samples
- **Class weights** — penalise misclassification of minority class more heavily during training
- **Threshold adjustment** — lower the decision threshold for the minority class at inference
- Use **precision, recall, F1, AUC-ROC** rather than accuracy when classes are imbalanced

### Feature Engineering

- **Numerical** — normalisation (min-max), standardisation (z-score), log transforms for skewed distributions, binning
- **Categorical** — one-hot encoding, ordinal encoding, target encoding, embeddings
- **Text** — TF-IDF, word embeddings (Word2Vec, GloVe), transformer-based embeddings (BERT)
- **Date/Time** — extract day of week, hour, month, seasonality flags, time since event
- **Interaction features** — multiply or combine features to capture non-linear relationships

### AWS Data Processing Tools

| Tool | Use Case |
|---|---|
| **AWS Glue** | Serverless ETL; schema discovery, transformation, cataloguing |
| **Amazon EMR** | Large-scale distributed processing (Spark, Hive) |
| **SageMaker Processing** | Run custom processing scripts (scikit-learn, Spark) on managed infrastructure |
| **Glue DataBrew** | No-code data profiling and transformation |
| **SageMaker Feature Store** | Centralised feature storage and retrieval for training and inference |

---

## Model Development

Model development covers algorithm selection, training, hyperparameter tuning, and evaluation.

### Algorithm Selection Guidelines

| Factor | Consideration |
|---|---|
| **Data size** | Deep learning typically requires large datasets; tree-based models work well with tabular data of moderate size |
| **Interpretability** | Linear models and decision trees are more interpretable than neural networks |
| **Training time** | Simpler models train faster; consider compute budget |
| **Data type** | Structured/tabular → XGBoost, Random Forest; Image → CNN; Text → Transformers; Time series → LSTM, DeepAR |
| **Labels available** | Supervised (labels exist) vs. unsupervised (no labels) vs. semi-supervised |

### Bias-Variance Trade-off

- **High bias (underfitting)** — model is too simple; poor performance on training and validation data
  - Fix: increase model complexity, add features, reduce regularisation
- **High variance (overfitting)** — model memorises training data; poor generalisation
  - Fix: more training data, regularisation (L1/L2, dropout), cross-validation, ensemble methods, early stopping

### Regularisation Techniques

| Technique | Effect |
|---|---|
| **L1 (Lasso)** | Drives some weights to exactly zero; performs feature selection |
| **L2 (Ridge)** | Shrinks all weights towards zero; penalises large weights |
| **Elastic Net** | Combination of L1 and L2 |
| **Dropout** | Randomly deactivates neurons during training (neural networks) |
| **Early stopping** | Stop training when validation loss stops improving |

### Hyperparameter Tuning

- **Manual tuning** — adjust parameters based on intuition; inefficient
- **Grid search** — exhaustive search over a predefined parameter grid; expensive
- **Random search** — randomly sample combinations; more efficient than grid for high-dimensional spaces
- **Bayesian optimisation** — uses results of past trials to guide future search; most efficient
- **SageMaker Automatic Model Tuning (AMT)** — managed Bayesian optimisation; define objective metric and parameter ranges

### Cross-Validation

- **k-fold** — split data into k folds; train on k-1, validate on 1; repeat k times; average results
- **Stratified k-fold** — preserves class distribution in each fold
- **Time-series cross-validation** — expanding or sliding window; never use future data to predict the past

### Model Evaluation Metrics

**Classification:**
- **Accuracy** — proportion correct; misleading for imbalanced classes
- **Precision** — of predicted positives, how many are actually positive (TP / (TP + FP))
- **Recall (Sensitivity)** — of actual positives, how many did we capture (TP / (TP + FN))
- **F1 Score** — harmonic mean of precision and recall
- **AUC-ROC** — area under the ROC curve; measures discrimination ability across thresholds
- **Confusion Matrix** — breakdown of TP, TN, FP, FN

**Regression:**
- **MAE** — Mean Absolute Error; interpretable, robust to outliers
- **MSE / RMSE** — penalises large errors more heavily
- **R²** — proportion of variance explained by the model

### SageMaker Experiments
- Track training runs, hyperparameters, metrics, and artefacts
- Compare runs side-by-side to identify the best model configuration
- Integrates natively with SageMaker Training Jobs and Autopilot

---

## Deployment

Deploying a model means making it available to serve predictions in a production environment.

### Deployment Patterns

| Pattern | Description | Use Case |
|---|---|---|
| **Real-time Endpoint** | Low-latency synchronous inference | Customer-facing APIs, fraud detection |
| **Batch Transform** | Asynchronous inference on large datasets | Overnight scoring, bulk predictions |
| **Asynchronous Inference** | Queue-based; returns a result via S3 when complete | Large payloads, variable load |
| **Serverless Inference** | Auto-scales to zero; pay per invocation | Spiky or infrequent traffic |

### Deployment Strategies

| Strategy | Description | Benefit |
|---|---|---|
| **Blue/Green** | Run two environments; switch traffic instantly | Zero-downtime rollout; easy rollback |
| **Canary** | Gradually shift a small % of traffic to new model | Validate new model on real traffic safely |
| **Shadow** | New model receives traffic but responses are not served | Compare outputs without user impact |
| **A/B Testing** | Split traffic between model variants | Compare business metrics across versions |

### Model Packaging
- Models are packaged in **Docker containers** for deployment
- SageMaker uses **ECR (Elastic Container Registry)** to store and version container images
- Model artefacts (weights, config) stored in **S3**; container retrieves them at startup

### Infrastructure Considerations
- **Instance type selection** — CPU (`ml.m5`) for simple models; GPU (`ml.g4dn`, `ml.p3`) for deep learning
- **Auto Scaling** — configure SageMaker endpoint auto scaling based on `InvocationsPerInstance` metric
- **Multi-model endpoints** — host multiple models on a single endpoint to reduce cost
- **Elastic Inference** — attach fractional GPU acceleration to CPU instances (cost-effective for inference)

---

## Monitoring

Production ML systems degrade over time. Continuous monitoring is essential to detect and respond to quality issues.

### Types of Drift

| Type | Description |
|---|---|
| **Data drift (covariate shift)** | Distribution of input features changes; model was trained on different data |
| **Label drift (prior probability shift)** | Distribution of the target variable changes |
| **Concept drift** | The relationship between features and target changes (e.g., customer behaviour shifts) |
| **Prediction drift** | Distribution of model outputs changes without a known cause |

### SageMaker Model Monitor

Four built-in monitoring types:

| Monitor Type | What It Checks |
|---|---|
| **Data Quality Monitor** | Detects statistical drift in input features vs. a baseline |
| **Model Quality Monitor** | Compares predictions to ground truth labels (requires label collection) |
| **Bias Drift Monitor** | Detects changes in fairness metrics over time |
| **Feature Attribution Drift Monitor** | Detects changes in SHAP feature importance over time |

### Monitoring Workflow
1. Capture inference data using **SageMaker Data Capture** (logs requests and responses to S3)
2. Create a **baseline** from a representative sample of training/validation data
3. Schedule monitoring jobs to compare live traffic against the baseline
4. Violations generate **CloudWatch metrics** and trigger **CloudWatch Alarms** or **SNS notifications**

### What to Monitor Beyond the Model
- **Infrastructure metrics** — CPU, memory, GPU utilisation, latency, error rates (via CloudWatch)
- **Data pipeline health** — freshness of features, ETL job failures
- **Business KPIs** — downstream business metrics to detect silent degradation
- **Model staleness** — time since last retrain; schedule periodic retraining even without detected drift

### Retraining Strategies
- **Scheduled retraining** — retrain on a fixed cadence (daily, weekly)
- **Trigger-based retraining** — retrain when drift or quality metrics breach a threshold
- **Continuous training** — online learning or frequent mini-batch updates (advanced)
- Automate with **SageMaker Pipelines** and **EventBridge** triggers

---

## AWS Well-Architected Machine Learning Lens

The **AWS Well-Architected Framework** provides architectural best practices across six pillars. The **Machine Learning Lens** extends these pillars to ML-specific workloads.

### The Six Pillars Applied to ML

| Pillar | ML-Specific Considerations |
|---|---|
| **Operational Excellence** | Automate ML pipelines (SageMaker Pipelines); version data, code, and models; use CI/CD for ML (MLOps) |
| **Security** | Encrypt data at rest and in transit (KMS); use VPC isolation for training; apply least privilege IAM roles; audit with CloudTrail |
| **Reliability** | Design for model and infrastructure failure; implement fallback mechanisms; use multi-AZ deployments for endpoints |
| **Performance Efficiency** | Select appropriate instance types; use Spot Instances for training; leverage managed services to avoid undifferentiated heavy lifting |
| **Cost Optimisation** | Use SageMaker Savings Plans; Spot Training for fault-tolerant jobs; right-size endpoints; use Serverless Inference for bursty workloads |
| **Sustainability** | Choose energy-efficient instance families; schedule batch workloads during off-peak hours; reduce unnecessary data processing |

### ML Lens Design Principles

1. **Define and prioritise business objectives first** — model metrics must map to business outcomes
2. **Use managed ML services** — reduce undifferentiated heavy lifting
3. **Automate the ML lifecycle** — pipelines, model registration, deployment, and monitoring
4. **Treat data as a first-class citizen** — invest in data quality, lineage, and governance
5. **Build for reproducibility** — every training run should be fully reproducible from versioned inputs
6. **Implement responsible AI practices** — bias detection, explainability, and human review where appropriate

### MLOps Maturity Model

| Level | Description |
|---|---|
| **Level 0** | Manual process; scripts and notebooks; no automation |
| **Level 1** | ML pipeline automation; automated training and evaluation |
| **Level 2** | CI/CD for ML; automated deployment; model registry; full monitoring |

---

## Key ML Best Practices Summary

| Area | Best Practice |
|---|---|
| **Business alignment** | Define success metrics in business terms before selecting models |
| **Problem framing** | Choose the simplest problem formulation that meets the business need |
| **Data quality** | Invest more time in data than in model tuning — garbage in, garbage out |
| **Data leakage** | Always split before preprocessing; fit transformers on training data only |
| **Model selection** | Start with a baseline; only add complexity when justified by evaluation |
| **Fairness** | Run bias detection (SageMaker Clarify) pre- and post-training |
| **Explainability** | Use SHAP values and model cards to document and communicate model behaviour |
| **Deployment** | Use canary or blue/green deployments to safely roll out new model versions |
| **Monitoring** | Monitor data drift, model quality, and business KPIs continuously |
| **Automation** | Automate the end-to-end pipeline with SageMaker Pipelines and MLOps practices |
