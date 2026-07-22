# 06. Generative AI Model Fundamentals

> **Exam Domain:** Generative AI and Foundation Models (approx. 20% of exam)

## 🎯 Key Exam Topics
- Transformer architecture: self-attention, multi-head attention, positional encoding
- Masked self-attention: what it does and which models use it (GPT)
- GPT = decoder-only; BERT = bidirectional encoder
- LLM inference parameters: temperature, top-k, top-p, max tokens, context window
- Transfer learning with transformers: pre-training → fine-tuning
- Fine-tuning vs. continued pre-training vs. RAG
- AWS foundation models: Titan, Claude, Stable Diffusion, Jurassic-2, Llama

## ⚠️ Common Exam Traps
- **Temperature** controls randomness — higher = more creative/varied, lower = deterministic
- **Top-p vs. Top-k:** top-p = cumulative probability threshold; top-k = fixed number of candidates
- **Context window** is the *combined* input + output token limit, not just input
- **Continued pre-training uses *unlabeled* data; fine-tuning uses *labeled* prompt-completion pairs**
- **Masked self-attention** prevents future token attendance (GPT) — not the same as standard self-attention (BERT)
- Transformers replaced RNNs primarily because they allow *parallel training*, not because they are more accurate per se

---

# Generative AI Model Fundamentals

## The Transformer Architecture

The Transformer architecture is a deep learning model that changed natural language processing and is widely used in generative AI. It was introduced in the paper *Attention Is All You Need* by Vaswani et al. in 2017.

Transformers replaced the sequential bottleneck of RNNs and LSTMs by using attention instead of relying on a strict step-by-step feedback loop. That makes them useful for sequential data such as language and time series, while also allowing much more parallel training.

The key ideas from *Attention Is All You Need* are:

1. A token is the basic unit of the input sequence.
2. Transformers handle word order better than older sequence models.
3. They learn relationships between words more directly.
4. They remove the sequential limitation that makes RNNs hard to parallelise.

Transformers are used for chat apps, question answering, text classification, named entity recognition, summarisation, translation, and code generation.

## Self-Attention

Self-attention lets the model weigh the importance of different parts of the input when making predictions. This helps the model focus on the most relevant information in a sequence.

Attention-based networks use self-attention to capture long-range dependencies and produce context-aware representations.

For each token, the model learns three weight matrices:

1. Query (`Wq`).
2. Key (`Wk`).
3. Value (`Wv`).

The attention score is computed by comparing the query and key vectors. The value vectors are then weighted by those scores to produce the final output. Softmax is applied to normalise the scores.

### Masked Self-Attention

Masked self-attention is used in the decoder to stop the model from attending to future tokens during training. That is important for autoregressive generation, where the model should only use current and previous tokens.

GPT uses masked self-attention, while BERT uses a different bidirectional approach.

### Multi-Head Attention

Multi-head attention splits the query, key, and value vectors into multiple heads so the model can learn different relationships in parallel. Each head learns a different view of the input, and the outputs are combined afterward.

## Generative Pre-Trained Transformers

Generative Pre-Trained Transformers, or GPT models, are transformer-based language models pre-trained on large text corpora.

How GPT works:

1. Pre-training: the model learns to predict the next token from previous tokens.
2. Fine-tuning: the model is adapted to a specific task or domain.

GPT models are decoder-only stacks. Each decoder block typically contains masked self-attention and a feed-forward neural network. Because they learn from unlabelled text and predict the next token, they do not need explicit input-output labels in the traditional supervised sense.

Input and output processing:

- Tokenisation converts text into tokens.
- Token embeddings capture semantic similarity between tokens.
- Positional encoding preserves token order.
- The decoder stack outputs logits for the next token.
- Temperature can be used to make token selection more or less random.

## Large Language Models

Large Language Models, or LLMs, are language models trained on massive text datasets and parameter counts.

Key terms:

1. Tokens: numerical representations of words or parts of words.
2. Embeddings: vectors that capture token meaning.
3. Top-p: probability threshold for token inclusion.
4. Top-k: selects from the top k token candidates.
5. Temperature: controls randomness in token selection.
6. Context window: number of tokens the model can process at once.
7. Max tokens: limit on the total number of input or output tokens.

## Transfer Learning for Transformers

Transfer learning adapts a model trained on one task to a different but related task.

In transformers, this usually means:

1. Starting from a pre-trained model.
2. Freezing some layers.
3. Fine-tuning the remaining layers on task-specific data.
4. Optionally adding a new layer on top of the pre-trained model.

## Generative AI in AWS

AWS supports generative AI through foundation models and managed services.

Key ideas:

1. Foundation models: large pre-trained transformer models that can be adapted for new tasks or applications.
2. Examples: GPT-style models, BERT, DALL-E, and Llama.
3. AWS foundation models: Jurassic-2, Claude, Stable Diffusion, and Amazon Titan.

SageMaker Studio includes JumpStart, which lets you quickly open a notebook with a model already loaded and ready to use.

---

## 📋 Module 06 — Quick Summary

| Topic | Must-Know Fact |
|---|---|
| Transformer | Introduced in "Attention Is All You Need" (2017); replaced RNNs |
| Self-attention | Each token attends to all others to build context |
| Masked self-attention | Prevents attending to future tokens; used in GPT (decoder-only) |
| Multi-head attention | Multiple parallel attention heads; each learns different relationships |
| GPT | Decoder-only; autoregressive; uses masked self-attention |
| BERT | Encoder-only; bidirectional; not autoregressive |
| Temperature | Higher = more random/creative; lower = more deterministic |
| Top-k | Selects from k most probable tokens |
| Top-p | Selects from tokens whose cumulative probability ≤ p |
| Context window | Combined input + output token limit |
| Continued pre-training | Unlabeled data; teaches domain vocabulary |
| Fine-tuning | Labeled prompt-completion pairs; adapts model to a task |
| Transfer learning | Freeze some layers; fine-tune remaining layers on new task |
