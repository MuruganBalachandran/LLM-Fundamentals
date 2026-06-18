# Module 2: Transformer Architecture (Encoder–Decoder Architecture)

## Learning Objectives

By the end of this module, you will understand:

- The complete architecture of a Transformer
- Every component inside the Transformer
- Why each component exists
- How data flows through the architecture
- What happens during training and inference
- Why each block is important
- How all components work together

---

# The Complete Transformer Architecture

The Transformer is **not a single algorithm**.

It is a collection of multiple building blocks working together.

Each block has one specific responsibility.

```
                    INPUT SENTENCE
                           │
                           ▼
                  Input Embedding
                           │
                           ▼
                 Positional Encoding
                           │
                           ▼
                ┌────────────────────┐
                │    Encoder Stack    │
                │                    │
                │ Multi-Head Attention│
                │ Add & Normalize     │
                │ Feed Forward        │
                │ Add & Normalize     │
                └────────────────────┘
                           │
                  Encoder Output
                           │
                           ▼
                ┌────────────────────┐
                │    Decoder Stack    │
                │                    │
                │ Masked Attention    │
                │ Encoder Attention   │
                │ Feed Forward        │
                └────────────────────┘
                           │
                           ▼
                  Linear Layer
                           │
                           ▼
                      Softmax
                           │
                           ▼
                    Predicted Word
```

Every arrow represents data flowing through another computation.

---

# Big Picture

Think of a Transformer as a team of specialists.

Imagine a company building a product.

One employee reads requirements.

Another organizes information.

Another designs.

Another reviews.

Another delivers the final product.

Similarly,

| Component | Responsibility |
|------------|----------------|
| Embedding | Convert words into vectors |
| Positional Encoding | Tell the model word order |
| Encoder | Understand the sentence |
| Decoder | Generate output |
| Linear Layer | Convert hidden representation into scores |
| Softmax | Choose the most likely next word |

Each block performs one job exceptionally well.

---

# Step 1 — Input Tokens

Computers cannot understand text.

They only understand numbers.

Sentence:

```
I love AI
```

Tokenizer converts it into tokens.

```
["I", "love", "AI"]
```

Then into IDs.

```
[15, 782, 1045]
```

These IDs have no meaning yet.

They are simply dictionary indexes.

---

# Step 2 — Input Embeddings

## What are Embeddings?

An embedding converts each token ID into a dense vector.

Instead of

```
15
```

the model sees

```
[0.24, -1.10, 0.78, ..., 0.56]
```

This vector contains semantic information.

Words with similar meanings end up with similar vectors.

Example:

```
King
Queen
Prince
Princess
```

Their vectors become close together.

Whereas

```
Banana
Car
Computer
```

are far away.

---

## Why do we need embeddings?

Numbers like

```
15
782
1045
```

carry no meaning.

Embedding layers transform meaningless IDs into meaningful mathematical representations.

Without embeddings,

the model would never know that

```
cat
kitten
dog
puppy
```

are related.

---

## Learned Embeddings

The embedding matrix is not manually designed.

It starts as random numbers.

During training,

backpropagation gradually adjusts these vectors.

Eventually,

similar words occupy nearby regions in vector space.

This is one reason LLMs understand synonyms.

---

# Step 3 — Positional Encoding

## The Problem

Unlike RNNs,

Transformers process every word simultaneously.

Sentence:

```
The dog chased the cat.
```

Sentence:

```
The cat chased the dog.
```

Contain the same words.

Without positional information,

the model would see

```
dog
cat
the
the
chased
```

and lose word order.

---

## Why Position Matters

Language depends heavily on order.

Consider:

```
Dog bites man.
```

vs

```
Man bites dog.
```

Same words.

Completely different meanings.

Therefore,

the Transformer needs information about position.

---

## Solution

Each word receives

```
Word Embedding
+
Position Vector
```

Result:

```
Final Input Representation
```

Instead of only knowing

```
"cat"
```

the model now knows

```
"cat at position 5"
```

---

## Why use vectors instead of position numbers?

Simply adding

```
Position = 5
```

isn't expressive enough.

Instead,

each position has its own vector.

These vectors capture relationships like

- before
- after
- nearby
- far away

allowing the model to reason about order mathematically.

---

# Step 4 — Encoder Stack

Now the sentence enters the Encoder.

The original Transformer contains **6 identical encoder layers**.

Modern models may use dozens or even hundreds.

Example:

GPT-3

96 Transformer blocks.

Llama 3

Many stacked Transformer layers.

DeepSeek

Hundreds of millions to hundreds of billions of parameters distributed across many Transformer layers.

---

Each encoder layer contains:

```
Multi-Head Self Attention

↓

Add & Normalize

↓

Feed Forward Network

↓

Add & Normalize
```

These four operations repeat again and again.

Each repetition helps the model understand the sentence better.

---

# Component 1 — Multi-Head Self Attention

This is the heart of the Transformer.

Every word asks:

> Which other words should I pay attention to?

Example:

```
The boy ate the apple because it was delicious.
```

When processing

```
it
```

the model examines every word.

```
The
boy
ate
the
apple
because
it
was
delicious
```

It learns

```
it → apple
```

instead of

```
it → boy
```

This ability makes Transformers excellent at understanding context.

---

## Why "Self" Attention?

Because attention happens within the same sentence.

Each word interacts with every other word.

---

# Multi-Head Attention

Instead of learning one relationship,

the Transformer learns many simultaneously.

Different attention heads specialize in different patterns.

One head might learn:

Subject ↔ Verb

Another:

Verb ↔ Object

Another:

Pronouns

Another:

Grammar

Another:

Meaning

All these perspectives are combined.

This is called **Multi-Head Attention**.

Think of it as multiple experts examining the same sentence from different viewpoints.

---

# Component 2 — Add & Normalize

After attention,

the output is added back to the original input.

```
Output = Attention + Original Input
```

This is called a **Residual Connection**.

Then Layer Normalization is applied.

---

## Why Residual Connections?

Deep networks can suffer from vanishing gradients.

Residual connections create shortcut paths.

Benefits:

- Faster training
- More stable optimization
- Easier learning in very deep models

Without residual connections,

modern Transformers with dozens or hundreds of layers would be much harder to train.

---

## Why Layer Normalization?

Values flowing through the network can become too large or too small.

Layer Normalization keeps activations in a stable range.

Benefits:

- Stable gradients
- Faster convergence
- Better training stability

---

# Component 3 — Feed Forward Network (FFN)

Attention mixes information between words.

The Feed Forward Network processes each word independently to learn richer representations.

Think of it as giving each word its own tiny neural network.

Structure:

```
Linear

↓

Activation (ReLU or GELU)

↓

Linear
```

Each token passes through the same FFN with shared weights.

---

## Why is FFN needed?

Attention answers:

> Which words matter?

FFN answers:

> How should this word's internal representation be transformed?

Without FFNs,

the model could relate words but would struggle to build powerful feature representations.

---

# Component 4 — Add & Normalize Again

Another residual connection and layer normalization follow the FFN.

```
FFN Output

+

Original Input

↓

LayerNorm
```

This completes one encoder layer.

The output becomes the input to the next encoder layer.

---

# Why Stack Many Encoder Layers?

One layer captures simple relationships.

Many layers gradually build deeper understanding.

Example:

Layer 1

```
Recognizes words.
```

Layer 3

```
Recognizes phrases.
```

Layer 8

```
Recognizes sentence meaning.
```

Layer 24

```
Captures abstract concepts and long-range dependencies.
```

Higher layers represent increasingly sophisticated semantic information.

---

# Encoder Output

After the final encoder layer,

every token has a rich contextual representation.

Example:

```
Apple
```

is no longer just the word "Apple."

Depending on context, it could represent:

- the fruit
- the technology company

The encoder determines the correct meaning using surrounding words.

This contextual understanding is one of the biggest breakthroughs of the Transformer.

---

# Step 5 — Decoder Stack

The decoder is responsible for generating text.

It receives:

1. Previously generated tokens
2. Encoder output

Each decoder layer contains:

```
Masked Multi-Head Attention

↓

Add & Normalize

↓

Encoder–Decoder Attention

↓

Add & Normalize

↓

Feed Forward Network

↓

Add & Normalize
```

---

# Masked Self-Attention

When predicting the next word,

the decoder must not see future words.

Example:

```
I love AI
```

While predicting

```
love
```

it cannot peek at

```
AI
```

Otherwise,

the model would cheat during training.

A mask hides future positions.

Only previous words remain visible.

---

# Encoder–Decoder Attention

The decoder now looks back at the encoder output.

Example:

English:

```
I love programming.
```

French output:

```
J'aime programmer.
```

While generating

```
programmer
```

the decoder attends to

```
programming
```

inside the encoder output.

This mechanism allows the decoder to use information extracted from the input sentence.

---

# Decoder Feed Forward Network

Just like in the encoder,

each token passes through a Feed Forward Network.

This further refines the token representation before prediction.

---

# Linear Layer

The decoder produces hidden vectors.

These vectors are not words.

The Linear Layer projects each hidden vector into a space where each dimension corresponds to a vocabulary word.

If the vocabulary contains 50,000 words,

the output becomes a vector with 50,000 scores.

Each score represents how likely that word is to be the next token.

---

# Softmax Layer

The scores are converted into probabilities.

Example:

| Word | Probability |
|------|-------------|
| apple | 0.71 |
| orange | 0.10 |
| banana | 0.08 |
| table | 0.01 |

The highest probability is selected (or sampled, depending on decoding strategy).

This becomes the next generated token.

The process then repeats until an end-of-sequence token is produced.

---

# End-to-End Data Flow

```
Input Sentence

↓

Tokenizer

↓

Token IDs

↓

Embedding Layer

↓

Positional Encoding

↓

Encoder Stack

↓

Contextual Representations

↓

Decoder Stack

↓

Linear Layer

↓

Softmax

↓

Next Token

↓

Repeat Until Output Complete
```

---

# Why is this Architecture So Powerful?

The Transformer architecture combines several strengths:

- Processes all tokens in parallel during training
- Captures long-range dependencies using attention
- Learns contextual word meanings
- Scales effectively to billions of parameters
- Forms the foundation of modern Large Language Models (LLMs)

Its modular design allows researchers to build encoder-only, decoder-only, or encoder–decoder models depending on the task.

---

# Common Interview Questions

### Why do we need embeddings?

To convert discrete token IDs into continuous vector representations that capture semantic meaning.

---

### Why do we need positional encoding?

Because Transformers process tokens in parallel and need explicit information about word order.

---

### Why are residual connections important?

They improve gradient flow, stabilize training, and enable very deep Transformer networks.

---

### What is the role of the Feed Forward Network?

It transforms each token's representation independently after attention, enabling richer feature learning.

---

### Why is Layer Normalization used?

To stabilize activations, improve gradient flow, and speed up convergence during training.

---

### Why is Softmax applied at the end?

To convert raw output scores into a probability distribution over the vocabulary.

---

# Key Takeaways

- A Transformer is built from multiple specialized components working together.
- Embeddings convert token IDs into meaningful vectors.
- Positional Encoding preserves word order.
- Multi-Head Self-Attention lets each token gather information from all other tokens.
- Feed Forward Networks refine token representations independently.
- Residual Connections and Layer Normalization stabilize deep learning.
- The Encoder builds contextual understanding of the input.
- The Decoder generates the output one token at a time.
- Linear and Softmax layers convert hidden representations into predicted words.
- This architecture powers nearly all modern Large Language Models.