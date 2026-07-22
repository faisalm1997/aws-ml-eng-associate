# 07. Building GenAI Applications with Amazon Bedrock

> **Exam Domain:** Generative AI and Foundation Models (approx. 20% of exam)

## 🎯 Key Exam Topics
- Bedrock API endpoints: `bedrock`, `bedrock-runtime`, `bedrock-agent`, `bedrock-agent-runtime`
- Fine-tuning vs. continued pre-training: data requirements and use cases
- RAG: pros/cons vs. fine-tuning, vector store workflow
- Knowledge Bases: components (embedding model + vector store + chunking strategy)
- Chunking strategies: semantic, hierarchical, Lambda-based
- Guardrails: topic denial, word filters, PII masking, contextual grounding checks
- Agents: action groups, Lambda functions, `InvokeAgent`
- Bedrock deployment: on-demand vs. provisioned throughput

## ⚠️ Common Exam Traps
- **Fine-tuning needs *labeled* prompt-completion pairs; continued pre-training uses *unlabeled* text**
- **RAG is faster/cheaper than fine-tuning for injecting new information** but requires retrieval infrastructure
- **Contextual grounding checks** reduce hallucinations — not word filters or topic denial
- **`bedrock-agent-runtime`** is used to *invoke* an agent (`InvokeAgent`) — `bedrock-agent` is for *managing* agents
- **Semantic chunking** uses a foundation model; **hierarchical chunking** uses parent/child structure — they are not the same
- You must **request model access** in Bedrock before using any base model; Amazon Titan is typically approved fastest
- For sensitive Bedrock data, use **VPC + PrivateLink** — not just KMS encryption

---

# Amazon Bedrock

Amazon Bedrock is a fully managed service for building and scaling generative AI applications using foundation models from Amazon and third-party providers. It exposes models through APIs so you can focus on the application while Bedrock handles infrastructure and scaling.

## Key Features

1. Supports chat, text, and image models.
2. Allows use of prebuilt, fine-tuned, and custom models.
3. Uses AWS billing for third-party models.
4. Supports retrieval-augmented generation.
5. Supports LLM agents.

Bedrock is serverless and integrates well with SageMaker Canvas.

## Bedrock API Endpoints

Bedrock provides multiple endpoints for management and inference.

1. `bedrock`: manage, deploy, and train models.
2. `bedrock-runtime`: run inference against models.
3. `bedrock-agent`: manage, deploy, and train agents and knowledge bases.
4. `bedrock-agent-runtime`: run inference against agents and knowledge bases using operations such as `InvokeAgent`, `Retrieve`, and `RetrieveAndGenerate`.

## Model Access

Before using a base model, you must request access in Bedrock.

- Amazon Titan models are usually approved quickly.
- Third-party models may require additional information.
- Usage is billed at the provider's model rate through AWS.

## Fine-Tuning and Continued Pre-Training

Bedrock supports both fine-tuning and continued pre-training.

### Fine-Tuning

Fine-tuning adapts an LLM to a specific use case using your own data.

It can:

1. Reduce prompt size.
2. Save tokens.
3. Improve performance for domain-specific tasks.
4. Support use cases such as chatbots, newer training data, and proprietary data.

Supported model types include Titan, Cohere, and Meta models. Text models use labeled prompt-completion pairs, while image models use S3 paths and image descriptions. For sensitive data, use VPC and PrivateLink.

### Continued Pre-Training

Continued pre-training uses unlabeled data to expose the model to more domain text. It helps the model learn your corpus without needing to include that information in every prompt.

## RAG Fundamentals

Retrieval-augmented generation combines retrieval and generation so the model can ground responses in external knowledge.

Think of it as an open-book exam for LLMs: you query an external data source first, then use those results in the prompt.

Pros:

1. Faster and cheaper than fine-tuning for new information.
2. Updating knowledge is mostly a database update.
3. Can use semantic search through vector stores.
4. Helps reduce hallucinations for information the model was not trained on.

Cons:

1. Can become complex quickly.
2. Is sensitive to prompt templates.
3. Is still non-deterministic and can hallucinate.
4. Depends heavily on retrieval quality.

## Vector Stores and Knowledge Bases

Bedrock RAG implementations usually need an embedding model and a vector store.

Embeddings are vectors that place similar items near each other in multi-dimensional space. The retrieval flow is:

1. Convert the query into an embedding.
2. Search the vector database for the nearest matches.
3. Return the top-N most similar items.

Database options depend on the use case:

1. Graph databases: useful for product recommendations and relationships.
2. OpenSearch: useful for traditional text search.
3. Vector-capable databases: OpenSearch, Elasticsearch, SQL, Neptune, Redis, MongoDB, and Cassandra.
4. Purpose-built vector databases: Pinecone, Weaviate, Chroma, Margo, and Vespa.

## Amazon Bedrock Knowledge Bases

Knowledge Bases let you upload documents or structured data into Bedrock and use automatic RAG.

You need:

1. An embedding model.
2. A vector store.
3. Chunking strategy for splitting data into vectors.

Chunking strategies:

1. Semantic chunking: uses an FM to group relevant sentences.
2. Hierarchical chunking: uses smaller child chunks under larger parent chunks.
3. Lambda-based chunking: uses your own Lambda to control the strategy.

Knowledge Bases can also be used with agents for agentic RAG.

## Content Filtering and Guardrails

Bedrock guardrails help control prompts and responses.

They can:

1. Filter content in prompts and responses.
2. Work with text foundation models.
3. Filter words and topics, including profanities.
4. Mask or remove PII.
5. Perform contextual grounding checks to reduce hallucinations.
6. Be attached to agents and knowledge bases.

## Amazon Bedrock Agents

LLM agents can plan, remember, and use tools to answer requests.

Agent concepts:

1. Memory: chat history and external data stores.
2. Planning: breaks a task into sub-questions.
3. Tools: functions the agent can call during execution.

Action groups define tools, and the prompt tells the model how to use them. You define the parameters expected by the Lambda function behind the action group.

Agents can also use knowledge bases, and an optional code interpreter lets the agent write code or produce charts.

### Deploying Agents

1. Create an alias, which creates a snapshot of the agent.
2. Use on-demand throughput or provisioned throughput depending on quota and performance needs.
3. Call `InvokeAgent` through the Bedrock agent runtime endpoint using the alias ID.

## Additional Bedrock Features

1. Imported models: import models from S3 or SageMaker.
2. Model evaluation: automatic or human evaluation.
3. Provisioned throughput: purchase higher token throughput.
4. Watermark detection: detects whether an image was generated by Titan.
5. Bedrock Studio: a web workspace for Bedrock projects with SSO and collaboration support.

---

## 📋 Module 07 — Quick Summary

| Topic | Must-Know Fact |
|---|---|
| Fine-tuning | Labeled prompt-completion pairs; reduces prompt size |
| Continued pre-training | Unlabeled data; expose model to domain corpus |
| RAG | Retrieval + generation; faster/cheaper than fine-tuning for new info |
| Knowledge Bases | Managed RAG: embedding model + vector store + chunking |
| Semantic chunking | FM groups related sentences before creating vectors |
| Hierarchical chunking | Parent/child chunk structure |
| Guardrails — grounding checks | Reduces hallucinations by verifying responses against retrieved context |
| Guardrails — PII masking | Removes or masks personally identifiable information |
| Action groups | Tools for Bedrock agents; backed by Lambda functions |
| `InvokeAgent` | API call on `bedrock-agent-runtime` to run an agent |
| Provisioned throughput | Purchase higher token throughput for predictable workloads |
| Watermark detection | Detects if an image was generated by Amazon Titan |
