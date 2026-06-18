# Module 3: Encoder vs Decoder — Understanding the Two Core Components of a Transformer

## Learning Objectives

By the end of this module, you will understand:

- What an Encoder is
- What a Decoder is
- Why Transformers are divided into Encoder and Decoder
- How the Encoder works internally
- How the Decoder works internally
- The complete flow of data
- Encoder-only vs Decoder-only vs Encoder-Decoder models
- Why BERT uses only Encoder
- Why GPT uses only Decoder
- Why T5 uses both
- Encoder vs Decoder comparison
- Interview questions

---

# Introduction

When people first learn about Transformers, they often think that **Encoder** and **Decoder** are two separate models.

They are **not**.

They are two different components of the same Transformer architecture, each designed for a specific purpose.

Think of the Transformer as a factory.

```
Raw Material
      │
      ▼
  Encoder Department
      │
      ▼
Processed Information
      │
      ▼
 Decoder Department
      │
      ▼
 Finished Product
```

The Encoder **understands** the input.

The Decoder **generates** the output.

---

# Why Do We Need Two Parts?

Imagine a human translator.

Someone says:

> "I love programming."

The translator performs two different mental tasks.

### Step 1

They first understand the sentence.

They identify:

- Subject
- Verb
- Object
- Grammar
- Meaning
- Context

Only after understanding the sentence do they begin speaking in another language.

### Step 2

Now they generate the translated sentence.

The same happens inside a Transformer.

```
Understand

↓

Generate
```

These are fundamentally different tasks.

That is why the original Transformer separates them.

---

# What is an Encoder?

## Definition

An **Encoder** is the part of the Transformer responsible for **understanding the input**.

It reads the entire input sequence and produces a contextual representation for every token.

It **does not generate text**.

Instead, it answers the question:

> "What does this sentence actually mean?"

---

# The Goal of an Encoder

Suppose the input is:

```
The bank is near the river.
```

The word:

```
bank
```

could mean

- a financial institution
- the side of a river

The encoder examines every surrounding word.

```
river
```

helps it understand that

```
bank
```

means

```
river bank
```

This process is called **contextual understanding**.

---

# How the Encoder Works

The encoder processes the input in several stages.

```
Input Sentence

↓

Tokenizer

↓

Embeddings

↓

Positional Encoding

↓

Encoder Layer 1

↓

Encoder Layer 2

↓

Encoder Layer 3

↓

...

↓

Final Contextual Representation
```

Each layer improves the understanding of the sentence.

---

# What Happens Inside an Encoder Layer?

Every encoder layer has four major operations.

```
Input

↓

Multi-Head Self-Attention

↓

Add & Layer Normalization

↓

Feed Forward Network

↓

Add & Layer Normalization

↓

Output
```

The output becomes the input to the next encoder layer.

---

# Encoder Self-Attention

The encoder allows every word to look at every other word.

Example:

```
The dog chased the cat.
```

When processing

```
cat
```

the encoder also looks at

```
dog

chased

the
```

Every word communicates with every other word.

This allows the encoder to understand relationships.

---

# Encoder is Bidirectional

One of the most important characteristics of an encoder is that it is **bidirectional**.

It can look at:

- previous words
- future words

at the same time.

Example:

```
The bank is near the river.
```

When processing

```
bank
```

the encoder can see

```
The

bank

is

near

the

river
```

It uses the **entire sentence** to understand the meaning.

---

# Why is Bidirectional Understanding Powerful?

Consider:

```
He deposited money in the bank.
```

and

```
The fisherman sat on the bank.
```

The word

```
bank
```

appears in both.

Only by looking at the entire sentence can the model determine the correct meaning.

That is why encoder models excel at language understanding.

---

# What Does the Encoder Produce?

The encoder does **not** produce words.

It produces vectors.

Example:

```
Sentence

↓

Encoder

↓

[
Vector1,
Vector2,
Vector3,
...
]
```

Each vector now contains contextual information.

Instead of representing only a word,

it represents

> "this word inside this specific sentence."

---

# Applications of Encoder Models

Because encoders specialize in understanding,

they are excellent for tasks such as:

- Text Classification
- Sentiment Analysis
- Spam Detection
- Named Entity Recognition (NER)
- Question Answering
- Semantic Search
- Sentence Similarity

These tasks require understanding rather than generation.

---

# What is a Decoder?

## Definition

A **Decoder** is the part of the Transformer responsible for **generating output tokens** one token at a time.

Unlike the encoder,

its primary goal is:

> "Given everything generated so far, predict the next word."

---

# Why Does the Decoder Generate One Token at a Time?

Suppose we want to generate

```
I love AI.
```

The decoder cannot predict all words simultaneously.

Instead, it predicts:

```
I

↓

love

↓

AI

↓

.
```

Each newly generated word becomes part of the next input.

This process is called **autoregressive generation**.

---

# Decoder Workflow

```
Start Token

↓

Masked Self-Attention

↓

Encoder Attention (optional)

↓

Feed Forward

↓

Linear Layer

↓

Softmax

↓

Next Word

↓

Repeat
```

This continues until an End-of-Sequence (EOS) token is produced.

---

# Why is Decoder Attention Masked?

Imagine predicting the sentence:

```
I love AI.
```

While predicting

```
love
```

the decoder **must not** look at

```
AI
```

because that word has not been generated yet.

Allowing it to see future words would be cheating.

To prevent this,

the decoder uses **Masked Self-Attention**.

---

# What is Masked Self-Attention?

Masked Self-Attention allows each token to see only:

- itself
- previous tokens

Example:

```
I love AI
```

While generating

```
love
```

the decoder sees:

```
I

love
```

It cannot see:

```
AI
```

The future is hidden using a mask.

---

# Why is This Important?

Without masking,

the model could simply copy future words during training.

It would appear highly accurate during training but fail during inference, where future words are unavailable.

Masking ensures the training process matches real-world generation.

---

# Encoder-Decoder Attention

The original Transformer also includes **Encoder-Decoder Attention**.

This allows the decoder to access information extracted by the encoder.

Example:

English:

```
I love programming.
```

French Output:

```
J'aime programmer.
```

When generating

```
programmer
```

the decoder looks back at the encoder representation of

```
programming
```

This ensures accurate translation.

---

# Decoder Output

Unlike the encoder,

the decoder ultimately produces words.

Example:

```
Hidden Vector

↓

Linear Layer

↓

Softmax

↓

Word Probability

↓

Predicted Word
```

This process repeats until the sentence is complete.

---

# Decoder is Autoregressive

The decoder generates text one token after another.

```
Hello

↓

Hello my

↓

Hello my name

↓

Hello my name is

↓

Hello my name is John
```

Every prediction depends on all previous predictions.

---

# Why is the Decoder Good at Text Generation?

Because it learns:

> "Given the previous words, what is the most likely next word?"

This makes it ideal for:

- Chatbots
- Story generation
- Code generation
- Text completion
- Summarization
- Creative writing

---

# Encoder vs Decoder

| Feature | Encoder | Decoder |
|----------|----------|----------|
| Primary Goal | Understand input | Generate output |
| Reads Future Words? | Yes | No |
| Attention Type | Bidirectional Self-Attention | Masked Self-Attention |
| Generates Text? | No | Yes |
| Output | Contextual vectors | Next token probabilities |
| Best For | Understanding tasks | Generation tasks |

---

# Bidirectional vs Autoregressive

## Encoder

```
The

dog

chased

the

cat
```

Every word can attend to every other word.

```
← ← ← →

→ → → ←

All directions
```

The encoder has access to the entire sentence.

---

## Decoder

```
The

dog

chased

the

cat
```

When generating

```
chased
```

the decoder can only see:

```
The

dog

chased
```

It cannot see:

```
the

cat
```

Generation always moves left to right.

---

# Three Types of Transformer Models

Modern AI models use one of three architectures.

---

# 1. Encoder-Only Models

Architecture:

```
Input

↓

Encoder

↓

Prediction
```

No decoder is present.

Purpose:

Understand text.

Examples:

- BERT
- RoBERTa
- DistilBERT
- ALBERT

Best suited for:

- Classification
- Search
- NER
- Sentiment Analysis

---

# Why Does BERT Use Only an Encoder?

BERT's objective is understanding language.

It does not need to generate long passages.

Instead,

it creates high-quality contextual representations.

This makes it ideal for language understanding tasks.

---

# 2. Decoder-Only Models

Architecture:

```
Prompt

↓

Decoder

↓

Generated Text
```

No encoder exists.

Examples:

- GPT
- LLaMA
- Mistral
- DeepSeek
- Claude
- Gemini (generation components)

These models specialize in predicting the next token repeatedly.

---

# Why Does GPT Use Only a Decoder?

GPT is designed for text generation.

Its objective is:

> Predict the next token.

Since there is no separate input to understand before generation, an encoder is unnecessary.

The prompt itself becomes the context for generation.

---

# 3. Encoder-Decoder Models

Architecture:

```
Input

↓

Encoder

↓

Decoder

↓

Output
```

Examples:

- T5
- BART
- Original Transformer
- FLAN-T5

These models first understand the input and then generate an output.

---

# Why Does T5 Use Both?

Tasks such as:

- Translation
- Summarization
- Question Answering

require both understanding and generation.

Example:

```
Long Article

↓

Understand

↓

Generate Summary
```

This naturally fits the encoder-decoder architecture.

---

# Real-World Analogy

Imagine a classroom.

## Encoder

The student listens carefully to the teacher and understands the lesson.

No speaking yet.

Only understanding.

---

## Decoder

The student now answers questions using what they understood.

The encoder is the listener.

The decoder is the speaker.

---

# Which Architecture Powers ChatGPT?

ChatGPT is based on a **Decoder-Only Transformer**.

Reason:

Its primary task is generating the next token repeatedly to produce coherent text.

---

# Which Architecture Powers Google Search Ranking?

Models like BERT (Encoder-Only) are commonly used because ranking and understanding search queries require deep language understanding rather than generation.

---

# Summary Table

| Model | Architecture | Purpose |
|--------|--------------|---------|
| BERT | Encoder Only | Understand text |
| RoBERTa | Encoder Only | Understand text |
| GPT | Decoder Only | Generate text |
| LLaMA | Decoder Only | Generate text |
| Mistral | Decoder Only | Generate text |
| Claude | Decoder Only | Generate text |
| DeepSeek | Decoder Only | Generate text |
| T5 | Encoder + Decoder | Understand and Generate |
| BART | Encoder + Decoder | Summarization & Translation |
| Original Transformer | Encoder + Decoder | Machine Translation |

---

# Common Interview Questions

### What is the role of the Encoder?

The encoder converts the input sequence into contextual representations by allowing each token to attend to every other token.

---

### What is the role of the Decoder?

The decoder generates the output sequence one token at a time using masked self-attention and, when applicable, encoder-decoder attention.

---

### Why is the Decoder masked?

To prevent the model from seeing future tokens during training, ensuring it learns to predict the next token based only on previous context.

---

### Why are Encoder models better for classification?

Because they can view the entire input sequence simultaneously, producing rich contextual representations suitable for understanding tasks.

---

### Why are Decoder models better for generation?

Because they are trained autoregressively to predict the next token, making them naturally suited for generating coherent text.

---

### Which architecture does GPT use?

Decoder-Only Transformer.

---

### Which architecture does BERT use?

Encoder-Only Transformer.

---

### Which architecture does T5 use?

Encoder-Decoder Transformer.

---

# Key Takeaways

- The Encoder understands the input but does not generate text.
- The Decoder generates text one token at a time.
- Encoders use bidirectional self-attention.
- Decoders use masked self-attention to prevent seeing future tokens.
- Encoder-Only models (e.g., BERT) excel at language understanding.
- Decoder-Only models (e.g., GPT, LLaMA, Claude) excel at text generation.
- Encoder-Decoder models (e.g., T5, BART) combine understanding and generation, making them ideal for tasks like translation and summarization.
- Modern Generative AI systems are predominantly built on Decoder-Only Transformers because of their efficiency and effectiveness in autoregressive text generation.