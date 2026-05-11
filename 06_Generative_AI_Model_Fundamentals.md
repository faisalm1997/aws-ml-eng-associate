# Generative AI Model Fundamentals

## The Transformer Architecture

The Transformer architecture is a deep learning model that has revolutionized natural language processing (NLP) and has been widely adopted in various applications, including generative AI. It was introduced in the paper "Attention is All You Need" by Vaswani et al. in 2017.

Transformers started as RNNs and LSTMs and they introduced a feedback loop for propagating information forward. They are useful in modelling sequential things such as time series forecasts, and language which is a series of words or tokens.

Encoder/decoder architecture: encoders and decoders are RNNs, but with one vector tying them together, it creates a information bottleneck where information from the start of the sequence can get lost.

'Attention is all you need' is a paper from 2017 which states:

1. A hidden state for each step in the encoder > decoder framework is called a token
2. Deals better with differences in word order
3. Starts to have a concept of relationships between words
4. But RNN's are still sequential in nature and cannot be parallelised

Transformers allow RNNs to become parallelised so that models can train on much more data, using self attention to ditch RNNs for FFNNs, feed forward neural networks.

Transformers can be used in many applications such as: chatbased apps, question answering, text classification, named entity recognition, summarisation, translation, code and text generation.

## Self attention and attention based neural networks

Self attention is a mechanism that allows the model to weigh the importance of different parts of the input when making predictions. It enables the model to focus on relevant information while processing sequences.

Attention-based neural networks, such as Transformers, utilize self attention to capture dependencies between different parts of the input sequence. This allows them to effectively model long-range dependencies and generate coherent outputs.

Each encoder or decoder has a list of embeddings (vectors) for each token, self attention produces a weighted average of all the token embeddings which results in tokens being tied to other tokens that are important for its context. A new embedding that captures its meaning in context.

There are three matrices of weights which are learned through back propagation:

1. Query Wq
2. Key Wk
3. Value Wv

The attention score is calculated as the dot product of the query and key matrices, which determines how much focus to place on each token in the input sequence. The value matrix is then weighted by the attention scores to produce the final output.

Dot product is just one similarity function users can use to compute a score for each token by multiplying its query with each key q * k. Softmax is then applied to normalise the scores.

### Masked self attention

In the decoder, masked self attention is used to prevent the model from attending to future tokens during training. This ensures that the model only has access to the current and previous tokens when generating predictions, which is crucial for autoregressive models.

GPT can do this effectively but BERT does something else, e.g. 'good' wouldnt be affected by 'novel' but 'novel' can be affected by 'good'. We can repeat the entire process for each token in parallel and get new weightings for each token embedding. This is passed into the feed forward neural network.

Note: multi headed self attention is when the q, k and v vectors are reshaped into matrices. Then each row of the matrix can be processed in parallel with the number of rows as the number of 'heads'.

## Generative Pre Trained Transformers (GPT)

Generative Pre Trained Transformers (GPT) are a type of transformer-based language model that is pre-trained on a large corpus of text data. GPT models are designed to generate coherent and contextually relevant text based on the input they receive. 

How do they work?

1. Pre-training: GPT models are pre-trained on a large dataset of text, such as books, articles, and websites. During pre-training, the model learns to predict the next word in a sentence given the previous words, which helps it understand language structure and context.
2. Fine-tuning: After pre-training, GPT models can be fine-tuned on specific tasks or domains by providing additional training data. This allows the model to adapt its knowledge to specific applications, such as chatbots, content generation, or question answering.

GPT models are decoder only, stacks of decoder blocks each consisting of a masked self attention layer and a forward feed neural network. (BERT only consists if encoders) in GPT, there is no concept of input as it generates the next token over and over again. Uses attention to maintain relationships between the words, a user can prompt based on their needs and the model keeps generating given the previous tokens.

There are hundreds of billions of parameters, and getting rid of the inputs and outputs is what allows users to train on unlabelled piles of text.

GPT: Input processing is done via tokenisation. Token encoding, tokem embedding which captures semantic relationships between tokens and token similarities. Postional encoding captures the position of the token in the input relative to other nearby tokens, uses an interleaved sinusoidal function so it works on any length.

GPT: Output processing. The stack of decoders outputs a vector at the end, this is multiplied with the token embeddings, and gives you a probability (logits) of each token being the right next token (word) in the sequence. You can randomise things a bit with the temperature parameter setting instead of always picking the highest probability.

## LLMs

Large Language Models (LLMs) are a type of language model that is trained on a massive amount of text data and has a large number of parameters. LLMs, such as GPT-3, have billions of parameters and can generate highly coherent and contextually relevant text.

Key Terms:

1. Tokens - numerical representations of words or parts of words
2. Embeddings - mathematical representation (vectors) that encode the meaning of a token
3. Top P - threshold probability for token inclusion (higher p = more random)
4. Top K - alternate mechanism where k candidates exist for token inclusion (higher k = more random) 
5. Temperature - controls randomness in token selection (higher temperature = more random)
6. Context window - the number of tokens an LLM can process at once
7. Max tokens - Limit for total number of tokens (on input or output)

## Transfer Learning (fine tuning) transformers

Transfer learning is a technique in machine learning where a model trained on one task is adapted to perform a different but related task. 

In the context of transformers, transfer learning involves taking a pre-trained transformer model and fine-tuning it on a specific task or dataset.

Users are able to freeze specific layers and retrain others, add a layer on top of the pre trained model.

Gpt-x or favourite pre trained model > fine tuning neural network > desired result.

## Generative AI in AWS

1. Foundation models - giant, pre trained transformer models which are fine tuned for specific tasks or new apps. GPT-n, BERT, DALL-E, Llama.
2. AWS foundation models - Jurassic2, claude from anthropic, stable diffusion from stableai, amazon titan.

Sagemaker studio has a jumpstart feature. Lets you quickly open up a notebook with a given model loaded up and ready to go.