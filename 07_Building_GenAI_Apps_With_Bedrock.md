# Amazon Bedrock

Amazon Bedrock is a fully managed service that makes it easy to build and scale generative AI applications using foundation models (FMs) from leading AI startups and Amazon’s own Titan FMs. With Bedrock, you can access a wide variety of FMs through an API, without the need for any infrastructure management. This allows you to focus on building your application while Bedrock handles the underlying infrastructure and scaling.

## Key Features of Amazon Bedrock

1. Able to invoke chat, text or image models
2. Pre-built, own fine tuned models can be used or your own models
3. Third party models bill you through AWS own pricing strategy
4. Support for RAG
5. Support for LLM Agents 

Bedrock is serverless and can integrate nicely with the sagemaker canvas.

## Bedrock API Endpoints

Bedrock provides several API endpoints to interact with the service:

1. Bedrock: Manage, deploy and train models
2. Bedrock-runtime: perform inference against models
3. Bedrock-agent: Manage, deploy and train LLM agents and knowledge bases
4. Bedrock-agent-runtime: Perform inference against agents and knowledge bases such as InvokeAgent, Retrieve, RetrieveAndGenerate

## Bedrock Model Access

Before using any base model, in bedrock, you must first request access to the model. Amazon's own titan models will approve immediately with third party models requiring the user to submit additional information. The user will be billed the third party models rates through AWS. Approval for models only takes a few minutes.

## Fine Tuning and Continuous, pre training with Bedrock

Bedrock allows you to fine-tune models with your own data, which can help improve the performance of the model on specific tasks. You can also use continuous pre-training to further enhance the model's capabilities. This is particularly useful for adapting the model to specific domains or use cases.

- Fine tuning: this can be otherwise be classes as adapting an LLM to a specific use case. Additional training can be undertaken using your own data which eliminates the need to build up a prompt and saves tokens.
    - The fine tuned model can be used like any other model
    - You can fine tune a fine tuned model which will make it smarter overtime
    - Applications: chatbot, training data which is more recent than what the lLM had, training with proprietary data
    - Fine tuning custom models: Titan, cohere and meta models can all be fine tuned. Text models can be fine tuned using labeled training pairs of prompts and completions whereas image models can be fine tuned using image s3 paths + image descriptions. Advised to use privatelink and VPC for sensitive training data.
- Continued pre training: similar to fine tuning but with unlabeled data. Its the act of feeding the model with text to fimilarise a model with documents and whatever other data is useful. Users can keep adding extra data into the model itself, so you dont need to include it in the prompts to get a more reasonable answer back. 

## RAG Fundamentals

Retrieval Augmented Generation (RAG) is a technique that combines retrieval-based methods with generative models to improve the quality of generated responses. In RAG, the model retrieves relevant information from a knowledge base or external sources and uses that information to generate more accurate and contextually relevant responses.

It is often referred to as an open book exam for LLMs, you query an external database for answers instead of relying on the sole LLM. Those answers are then worked into the prompt for the LLM to work with. 

Pros:
    - Faster and cheaper way to incorporate new information into GenAI vs fine tuning
    - Updating info is a matter of updating the database
    - Can leverage semantic search via vector stores
    - Can prevent hallucinations when you ask the model about something which it wasnt trained on

Cons:
    - RAGs can get overcomplicated
    - Very sensitive to prompt templates you use to incorporate data
    - Non deterministic and can still cause LLMs to hallucinate
    - Very sensitive to the relevancy of the information which the user retrieves

## Vector Stores and Knowledge Bases within Amazon Bedrock

Choosing a database: Users can use whatever database they want based on the type of data which is being retrieved.
    - Graph database: for retrieving product recommendations or relationships  between items
    - Opensearch: for traditional text search

Note: Elasticsearch, opensearch can be used as a vectorDB.

Embeddings: An embedding is a large vector which is associated with your data. Think of it as a multi dimensional space which are computed such that items which are similar to each other are close to each other in that space. 

Embeddings are vectors, so should be stored in a vector database. Within a vector store, leverage embeddings which you might already have for ML. 

Embedding retrieval: A retrieval starts by computing an embedded vector for the thing you want to search for, query the vector db for the top items close to that vector, get back the top-N most similar things. 

Examples of databases which can do vector search: opensearch, elasticsearch, SQL, netpune, redis, mongodb and cassandra.

Examples of purpose built vector databases: pinecone, weaviate, chroma, margo, vespa etc.

## Implementing RAG with amazon bedrock knowledge bases

Users are able to upload their own documents or structured data into bedrock knowledge bases. You must use an embedding model, for which model access is needed and you can control the vector dimension. A vector store is also needed to implement a RAG in bedrock in which users can control the 'chunking' of data ie how many tokens are represented by each vector.

Using knowledge bases: a knowledge base in bedrock is basically an automatic RAG which you can integrate into an application directly. Can also implement into an agent called Agentic RAG.

Chunking strategies in bedrock:
- Semantic Chunking - uses FM to group relevant sentences
- Hierarchical Chunking - uses smaller chunks as children of a larger chunk
- Lambda-based - bring your own Lambda that will control the strategy

## Content filtering with bedrock

Amazon bedrock has certain guardrails:

- Content filtering for prompts and responses
- Guardrails works with text foundation models
- Word and topic filtering for profanities
- PII removal or masking
- Contextual grounding check: helps prevent hallucination which measures grounding and relevance of the query
- Guardrails can be incorporated into agents and knowledge bases

## Amazon Bedrock Agents

LLM Agents: the agent has memory and an ability to plan how to answer a request, and tools it can use in the process. The memory is just the chat history and external data stores. The planning module gives the agent guidance on how to break the question down into sub questions. Prompts associated with each tool are used to guide it on how to use its tools.

'Tools' are just functions provided to the tools API. Prompts guide the LLM on how to use them, tools may access outside information such as retrievers and other py modules, services etc.

How do agents know which tools to use?: In bedrock, action groups define a tool and the prompt informs the foundation model how to use it. The user must define the parameters the lambda function expects.

Agents may also have knowledge bases associated with them, an optional add on 'code interpreter' allows the agent to write its own code to answer questions or produce charts. 

Deploying bedrock agents: Create an alias for an agent which creates a snapshot > on demand throughput allows agent to run quotas set at the account level > provisioned throughput allows you to purchase an increased rate and number of tokens for your agent > your app can use the invokeagent request using your alias ID and an agents for amazon bedrock runtime endpoint. 

## More bedrock features:

- Imported models: import models from s3, sagemaker
- Model evaluation: can be automatic or human using the AWS managed team or own team
- Provisioned throughput
- Watermark detection: detects if an image was generated by Titan
- Bedrock studio: creates a webapp workspace for bedrock without aws accounts, uses SSO on integration with IAM and your IDP, users can collaborate on projects and components.