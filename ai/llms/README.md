# Large Language Models (LLMs)

## Overview

This section contains my notes and learning journey about Large Language Models (LLMs) - the technology behind ChatGPT, Claude, Gemini, and many other modern AI systems.

My goal is not only to understand how to _use_ LLMs, but to understand how they work internally, how they are trained, and how they integrate with real-world software systems.

---

## Learning Goals

- Understand what an LLM actually is
- Learn how language is represented as tokens
- Understand embeddings and vector representations
- Learn how Transformer architecture works
- Understand attention mechanisms
- Learn what model parameters (weights, biases, embeddings) are
- Understand next-token prediction
- Learn the relationship between LLMs, RAG, and Vector Search
- Build a tiny educational LLM from scratch

---

## Relationship to Other Topics

This section builds on concepts learned elsewhere in this repository.

### MongoDB Vector Search

Previously learned:

- document embeddings
- vector similarity search
- Retrieval-Augmented Generation (RAG)

Connection to LLMs:

- LLMs also use embeddings internally.
- RAG chunks consist of many LLM tokens.
- Vector Search retrieves relevant information for an LLM but is not part of the LLM itself.

### LangGraph

LangGraph orchestrates LLM calls.

An LLM is the reasoning engine inside many LangGraph nodes, while LangGraph manages workflow, memory, tools, and execution.

---

## Current Understanding

An LLM is a neural network (нейронная сеть) trained on massive amounts of text to predict the next token in a sequence.

Unlike a database, an LLM does not store facts explicitly. Instead, it learns statistical relationships encoded within billions (or trillions) of learned parameters.

The model tokenizes text into tokens, converts each token into an embedding vector, processes those embeddings through many Transformer layers, and predicts the next token.

---

## Planned Topics

- What is an LLM?
- Tokens
- Tokenization
- Embeddings
- Parameters
- Neural Networks
- Transformer Architecture
- Attention Mechanism
- Context Window
- Training
- Fine-Tuning
- RLHF
- Inference
- Prompt Engineering
- Function Calling
- Multimodal Models
- Mixture of Experts (MoE)
- Quantization
- Distillation

---

## Core Concepts

### What is an LLM?

LLM stands for Large Language Model.

It's a computer program trained on enormous amounts of text (books, websites, articles, conversations, code, etc.) to learn patterns in language.

At its core, an LLM does one surprisingly simple thing: Given some text, predict what text is likely to come next.

Even though the underlying task is "predict the next token," this ability scales into answering questions, writing essays, coding, translating languages, and much more.

An LLM is trained on huge amounts of text—often trillions of words or tokens.

### Tokens

LLMs don't actually see words.

They see tokens, which are chunks of text.

Examples:

"cat" → one token
"unhappy" → might be split into:
"un" + "happy"

"ChatGPT" → might be split differently

The model predicts one token at a time.

### Tokenization

### Embeddings

### Parameters

When people say an LLM has "100 billion parameters," they're counting all learned numbers, including:

Weights (by far the majority)
Biases
Embedding vectors

All of these are just learned numbers that change during training.

Each parameter by itself knows almost nothing.

Knowledge emerges from billions of parameters working together rather than from individual parameters storing facts.

Параметры LLM - это «память» нейросети, выраженная в миллионах или миллиардах значений (весов и смещений). Чем больше параметров, тем сложнее закономерности в языке может уловить модель и тем качественнее генерируемый текст.

Параметры — это обучаемые числовые значения модели (веса, смещения и векторные представления), которые постепенно изменяются во время обучения и позволяют модели улавливать статистические закономерности языка.

### Neural Networks

Нейронная сеть (нейросеть) — Я представляю себе нейронную сеть как огромный конвейер математических слоев.
Каждый слой немного преобразует входные данные.
Похоже на промежуточное ПО Express, за исключением того, что каждый слой выполняет математические преобразования вместо обработки запросов. Обучаясь на огромных массивах данных, искусственные нейроны находят скрытые закономерности, распознают образы и генерируют контент.

Как устроена нейросеть? - Она состоит из множества простых элементов — искусственных нейронов, которые объединяются в слои:

1. Входной слой: принимает исходные данные (например, пиксели картинки или слова в тексте).
2. Скрытые слои: анализируют информацию, передавая сигналы дальше. Чем больше таких слоев, тем глубже обучение.
3. Выходной слой: формирует окончательный ответ (например, результат перевода или вероятность того, что на фото изображен кот).
   Каждая связь между нейронами имеет свой **«вес»** — коэффициент важности. При обучении сеть корректирует эти веса, добиваясь максимальной точности.

---

## Connections

### MongoDB Vector Search

Document

↓

Embedding

↓

Stored in Atlas

↓

Cosine Similarity

↓

Retrieved for the LLM

---

### LLM

Sentence

↓

Tokenizer

↓

Tokens

↓

Embeddings

↓

Transformer

↓

Next Token

---

## Things That Finally Clicked

### Parameters are not stored knowledge

An LLM does not contain a database of facts.

Instead, it contains billions of learned numbers (parameters).

Each parameter contributes only a tiny amount.

Knowledge emerges from the interaction of all parameters rather than being stored explicitly.

---

### Embeddings in MongoDB and LLMs

MongoDB Vector Search stores embeddings.

LLMs also use embeddings.

Conceptually they are the same type of object (vectors representing semantic meaning).

The difference is how they are used.

MongoDB:
Embedding → Similarity Search

LLM:
Embedding → Transformer Layers → Next Token Prediction

---

### RAG Chunks vs Tokens

A RAG chunk contains many tokens.

Chunks exist for retrieval.

Tokens exist for language processing.

---

## Future Project

Build a tiny educational LLM in Python to better understand:

- tokenization
- embeddings
- parameters
- attention
- training
- next-token prediction

The objective is learning, not creating a production-quality language model.
