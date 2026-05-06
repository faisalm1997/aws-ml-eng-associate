# AWS AI Managed Services

AWS AI services are pre trained ML services which are services for your use case. They have a certain degree of responsiveness and availability. They can be deployed across several regions and AZs. Have specialised CPUs and GPUs for specific use cases for cost saving. They have token based pricing - pay for what you use and provisioned throughput for predictable workloads, cost savings and predictable performance.

## AWS Comprehend

Uses for NLP - natural language processing. A fully managed and serverless service which is used for finding insights and relationships in text. Can extract phrases, places, people, brands or events. Understand how positive or negative sentiment the text is and analyse text using tokenization and part of speech. Comprehend can organise a colletion of text files by topic. 

### Custom classification

Custom classification is a feature of AWS Comprehend that allows you to create your own custom classifiers to categorize text based on your specific needs. This is particularly useful when the predefined categories provided by AWS Comprehend do not fit your use case. Supports different document types. Analysis can be done in real time or through an async batch process.

### Customer Entity Recognition

1. NER - named entity recognition extract predefined. general purpose entities like people, places and other standard categories from text.
2. Custom entity recognition - analysing text for specific terms and noun based phrases. Extract terms like policy numbers, or phrases. 

### Custom models - Comprehend

You can create custom models for entity recognition or document classification, these are trained on your own data. Comprehend manages the model versioning and custom models can be copied between AWS accounts by attaching IAM policy to a model version, authorising the other account. 

## AWS Translate

Natural and accurate language translation. Allows users to localise content such as websites and applications for international users. Can easily translate large volumes of text efficiently.

## AWS Transcribe

Auto convert speech to text, transcribe uses a deep learning process called automatic speech recognition ASR to convert speech to text quickly and accurately. Automatically removes PII using redaction and supports automatic language identification for multi lingual audio. Common use cases would be transcribing customer service calls, generating metadata for media assets to create a fully searchable archive.


### Improving Accuracy 

Transcribe accuracy can be improved. This can aid in capturing domain specific or non standard terms. Custom vocab for words can be added to increase recognition of a new word by providing hints. Custom language models can be added for context to train a transcribe model on your own domain specific text data. It is brilliant for allowing the model to learn the context associated with a given word. Both methods can be used for the highest transcription accuracy.

Toxicity detection is ML powered, voice based toxicity detection capability which leverages speech cues to determine toxicity, hate speech, profanity etc.

## AWS Polly

Polly turns text into lifelike speech using deep learning. Allows you to create applications that talk, polly has some advanced features:

1. Lexicons - defines how to read certain specific pieces of text
2. SSML - speech synthesis markup language which allows polly to read text and  tells it how to pronounce it
3. Voice engine - generative, long form neural, standard
4. Speech mark - encode where a sentence or word starts or ends in audio. Helpful for lip syncing or highlighting words as they're spoken

## AWS Rekognition

Rekognition is a service that makes it easy to add image and video analysis to your applications. It can identify objects, people, text, scenes, and activities in images and videos, as well as detect any inappropriate content. Rekognition can be used for a wide range of applications, such as security and surveillance, media analysis, and customer engagement.

### Custom Labels

Users are able to label their own training images and upload them to amazon rekognition. Rekognition only needs a few hundred images or less. It then creates a model on the images which have been uploaded, new images will then be labelled as per the model training.

### Content moderation

Rekognition can detect inappropriate content in images and videos. It can identify explicit or suggestive content, as well as violent or disturbing imagery. This feature is useful for applications that require content moderation, such as social media platforms or online marketplaces. Integrated with Amazon Augmented AI, as well as having custom moderation adaptors.

A content moderation API can also be configured to allow for the user to generate an image based on a chatbot app, DetectModerationLabels API is used.

## AWS Lex

Amazon Lex is used to build chatbots quickly for apps using voice and text. Supports multiple languages and can be integrated with amazon kendra, connect, comprehend and lambda. The bot can understand automatically the user to intent to invoke the correct lambda function to 'fulfill the intent'. Slot or input parameters are asked for by the bot if necessary.

## AWS Personalize

A fully managed ML service which is used to build apps with real time personalised recommendations. Integrates into existing websites, apps, SMS, email marketing systems etc. Implmenetation takes days and not months. Data is read from S3, amazon personalised API provides the real time integration and then personalise publishes a customised personalised API which can be integated with other third party systems.

Recipes - these are algorithms which are prepared for specific use cases. The user must provide the training config on top of the recipe such as USER_PERSONAIZATION, PERSONALIZED_RANKING, RELATED_ITEMS, POPULAR_ITEMS etc. recipes and personalize are used for recommendations.

## AWS Textract

Automatically extracts text, handwriting, and data from any scanned documents using AI + ML. Extract data from forms and tables, read and process any type of document such as PDFs, images.

## AWS Kendra

Kenda is a fully managed document search service which is powered by ML. Users can extract answers from within a document such as text, PDF, HTML, powerpoint, MS Word, FAQs. Kendra has natural language search capabilities. Kendra can learn from user interactions and feeback to promote preferred results which is called incremental learning. Kendra also has the ability to fine tune search results which gives data freshness importance.

## AWS Augmented AI

Amazon Augmented AI (A2I) is a service that makes it easy to build human review workflows into your machine learning applications. A2I allows you to create human review workflows for tasks such as content moderation, document analysis, and image recognition. A2I can be used to improve the accuracy of your machine learning models by allowing humans to review and correct the output of the models.

Basic human oversight of machine learnng predictions in production. The ML model can be built on AWS or elsewhere.

## AWS Hardware for AI

Most common hardware used for AI is EC2. GPU based EC2 instances are used for AI hardware. AWS Trainium, Inferentia are the names of GPU based EC2 instances which are used. They have the lowest environmental footprint. 

## AWS Q for Business

Fully managed GenAI assistant for employees. The assistant trains and is used based on the companies knowledge base and data. Can answer questions, provide summaries and generate content as well as automate tasks. Can also perform routine actions e.g. submit time off requests, send meeting invites.

Amazon Q for business is built on amazon bedrock. It has a fully managed RAG and can connect to 40+ popular enterprise data sources. Has plugins which allow you to interact with 3rd party services. 

For Amazon Q business, users must authennticate through IAM identity center. Users receive responses generated only from the documents they have access to. IAM Identity Center can be configured with external identity providers.

Admin controls - controls and customise response for organisational needs. They are essential guard rails, and block specific words or topics. They only allow the service or system to respond with internal informaton. More granular rules can be in place such as global controls and topic level controls. 

## AWS Q Apps

AWS Q apps are applications built on top of Amazon Q for business. They are designed to provide specific functionalities and use cases for businesses. AWS Q apps can be used for a wide range of purposes, such as customer service, sales, marketing, and more. They are built using the Amazon Q for business platform and can be customized to meet the specific needs of a business.

## AWS Q Developer

AWS Q developer is a tool that allows developers to build and deploy applications on the Amazon Q for business platform. It provides a user-friendly interface for creating and managing applications, as well as tools for testing and debugging. AWS Q developer also allows developers to integrate their applications with other AWS services, such as Amazon S3, Amazon DynamoDB, and more. With AWS Q developer, developers can quickly and easily create powerful applications that leverage the capabilities of Amazon Q for business.

Rebranded as Kiro.