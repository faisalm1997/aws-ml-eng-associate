# 03. AWS Managed AI Services

> **Exam Domain:** ML Solution Design (approx. 30% of exam)

## 🎯 Key Exam Topics
- Which managed AI service fits each use case (Rekognition, Textract, Comprehend, Translate, Transcribe, Polly, Lex, Personalize, Kendra, A2I, Q for Business)
- `DetectModerationLabels` API for content moderation in Rekognition
- Transcribe accuracy improvements: custom vocabularies vs. custom language models
- Amazon Lex for chatbots with Lambda intent fulfillment
- Amazon Augmented AI (A2I) for human review workflows
- Amazon Q for Business for employee-facing GenAI with enterprise data source connectors

## ⚠️ Common Exam Traps
- **Textract vs. Rekognition:** Textract extracts structured data (forms, tables) from documents; Rekognition analyses images/videos for labels and moderation
- **Transcribe vs. Polly:** Transcribe = speech → text; Polly = text → speech
- **Kendra vs. Q for Business:** Kendra is a search service; Q for Business is a GenAI assistant with enterprise connectors and access control
- **Lexicons and SSML belong to Polly**, not Transcribe
- **Custom vocabularies** (Transcribe) hint words; **custom language models** (Transcribe) learn context — know the difference

---

# AWS AI Managed Services

AWS AI managed services are pre-trained ML services for common use cases. They are serverless or highly managed, scale across regions and AZs, and often use specialised hardware for cost and performance efficiency. Pricing is usually pay-as-you-go, with provisioned throughput available for predictable workloads.

## Amazon Comprehend

Amazon Comprehend is a fully managed, serverless NLP service for extracting insights and relationships from text.

It can:

1. Extract key phrases, places, people, brands, and events.
2. Detect sentiment and classify text.
3. Analyze text with tokenization and part-of-speech detection.
4. Organize collections of text documents by topic.

### Custom Classification

Custom classification lets you create your own text classifiers when the built-in categories do not fit your use case. It supports different document types and can run in real time or as an asynchronous batch job.

### Named Entity Recognition

1. Named entity recognition (NER): extracts general-purpose entities such as people and places.
2. Custom entity recognition: finds domain-specific terms and noun phrases such as policy numbers.

### Custom Models

You can create custom Comprehend models for entity recognition or document classification using your own data. Comprehend manages model versioning, and custom models can be shared across AWS accounts with the right IAM permissions.

## Amazon Translate

Amazon Translate provides natural-language translation for localizing websites and applications for international users. It is designed to translate large volumes of text efficiently.

## Amazon Transcribe

Amazon Transcribe converts speech to text using automatic speech recognition. It supports PII redaction, automatic language identification for multilingual audio, and common use cases such as call transcription and searchable media archives.

### Improving Accuracy

Transcribe accuracy can be improved for domain-specific or uncommon terms by using:

1. Custom vocabularies to hint new words.
2. Custom language models to learn domain-specific context.

Transcribe also includes toxicity detection, which uses speech cues to detect hate speech, profanity, and similar content.

## Amazon Polly

Amazon Polly turns text into lifelike speech using deep learning.

Key features:

1. Lexicons: define how specific text should be pronounced.
2. SSML: controls pronunciation, pacing, and emphasis.
3. Voice engines: generative, long-form neural, and standard.
4. Speech marks: indicate where words or sentences start and end in audio.

## Amazon Rekognition

Amazon Rekognition adds image and video analysis to applications. It can identify objects, people, text, scenes, activities, and inappropriate content.

### Custom Labels

You can train Rekognition with your own labeled images. It only needs a few hundred images, and new images are labeled based on the trained model.

### Content Moderation

Rekognition can detect explicit, suggestive, violent, or disturbing content in images and videos. It integrates with Amazon Augmented AI and supports custom moderation adaptors. The `DetectModerationLabels` API can be used for moderation workflows.

## Amazon Lex

Amazon Lex helps build chatbots with voice and text. It supports multiple languages and integrates with Amazon Kendra, Amazon Connect, Comprehend, and Lambda. Lex can infer user intent and invoke the right Lambda function to fulfill that intent, asking for slot values when needed.

## Amazon Personalize

Amazon Personalize is a fully managed service for real-time personalized recommendations.

It can be integrated into websites, apps, SMS, and email systems. Data is read from S3, and Amazon Personalize provides a real-time API for recommendations. Recipes are prebuilt algorithms for specific use cases.

Common recipes include:

1. User personalization.
2. Personalized ranking.
3. Related items.
4. Popular items.

## Amazon Textract

Amazon Textract automatically extracts text, handwriting, forms, and tables from scanned documents, PDFs, and images.

## Amazon Kendra

Amazon Kendra is a fully managed enterprise search service powered by ML. It can extract answers from PDFs, HTML, PowerPoint, Word documents, FAQs, and other content.

Kendra supports natural-language search, learns from user feedback, and can improve result relevance through incremental learning and freshness tuning.

## Amazon Augmented AI

Amazon Augmented AI (A2I) makes it easy to add human review workflows to ML applications.

It is used for tasks such as:

1. Content moderation.
2. Document analysis.
3. Image recognition.

A2I provides human oversight for ML predictions in production, whether the model is built on AWS or elsewhere.

## AWS Hardware for AI

Common hardware for AI workloads includes EC2 GPU instances. AWS Trainium and Inferentia are purpose-built accelerators for training and inference and are designed for better cost efficiency and lower environmental impact.

## Amazon Q for Business

Amazon Q for Business is a fully managed GenAI assistant for employees. It uses a company knowledge base and connected data sources to answer questions, summarize content, generate content, and automate routine tasks such as time-off requests or meeting invites.

It is built on Amazon Bedrock and includes managed retrieval-augmented generation. It can connect to 40+ enterprise data sources and supports plugins for third-party services.

Users authenticate through IAM Identity Center, and responses are limited to content they are authorized to access. Admin controls provide guardrails such as topic-level and global restrictions.

## Amazon Q Apps

Amazon Q Apps are applications built on top of Amazon Q for Business. They are designed for specific business use cases such as customer service, sales, and marketing.

## Amazon Q Developer

Amazon Q Developer helps developers build and deploy applications with AWS integrations. It includes tools for creating, managing, testing, and debugging applications, and can integrate with services such as Amazon S3 and Amazon DynamoDB.

It has been rebranded as Kiro.

---

## 📋 Module 03 — Quick Summary

| Service | Primary Use | Key Detail |
|---|---|---|
| Comprehend | NLP: entities, sentiment, topics | Custom classification + custom NER |
| Translate | Language translation | Batch translation of large volumes |
| Transcribe | Speech → text | Custom vocabularies + custom language models |
| Polly | Text → speech | Lexicons, SSML, multiple voice engines |
| Rekognition | Image/video analysis | `DetectModerationLabels` for content moderation |
| Lex | Chatbots | Intent detection + Lambda slot fulfillment |
| Personalize | Recommendations | Prebuilt recipes; no ML expertise required |
| Textract | Document extraction | Structured forms and tables from PDFs/scans |
| Kendra | Enterprise search | Natural language search over document repositories |
| A2I | Human review | Low-confidence predictions routed to human reviewers |
| Q for Business | Employee GenAI assistant | 40+ connectors; respects existing access controls |
