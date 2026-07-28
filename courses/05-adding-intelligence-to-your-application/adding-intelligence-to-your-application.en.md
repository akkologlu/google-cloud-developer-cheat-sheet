# Module 5 – Adding Intelligence to Your Application

## Overview

Machine Learning (ML) enables computers to recognize patterns and make predictions from data.

Instead of building complex algorithms yourself, Google Cloud provides **pre-trained AI models** that are available through simple APIs. With only a few API calls, developers can add intelligent features such as image recognition, speech recognition, translation, and document processing to their applications.

This module also introduces **Generative AI**, which goes beyond analyzing data by generating entirely new content such as text, images, code, and audio.

---

# Google Cloud Pre-trained AI APIs

Google Cloud offers several ready-to-use AI services.

The biggest advantage is that **you don't need machine learning knowledge or model training**. You simply send data to an API and receive the AI-generated result.

---

## 1. Vision AI

Vision AI analyzes images.

Typical capabilities include:

- Object detection
- OCR (Optical Character Recognition)
- Face detection
- Logo detection
- Landmark recognition
- Explicit content detection
- Image labeling

### Example

Upload an image of a receipt.

Vision API extracts:

- Store name
- Date
- Total price

Upload a wedding photo.

Vision API can identify:

- Faces
- Smiling
- Emotional expressions

---

## 2. Speech-to-Text

Converts speech into text.

Supports more than 100 languages.

Common use cases:

- Voice assistants
- Meeting transcription
- Voice commands
- Call center transcription

Example:

```
Audio:
"Turn on the lights"

↓

Text:
"Turn on the lights"
```

---

## 3. Text-to-Speech

Converts text into realistic speech.

Common use cases:

- Accessibility
- Voice assistants
- Navigation systems
- Audiobooks

---

## 4. Translation AI

Automatically translates text into another language.

Example:

```
Hello

↓

Merhaba
```

Useful for:

- Multilingual websites
- Chat applications
- Customer support
- Global products

---

## 5. Natural Language AI

Analyzes text to understand its meaning.

Capabilities include:

- Sentiment Analysis
- Entity Extraction
- Syntax Analysis
- Intent Detection

### Example

Customer review:

> "The delivery was slow but the product is excellent."

Natural Language API can determine:

Sentiment

```
Positive
```

Entities

```
Product
Delivery
```

---

## 6. Video Intelligence AI

Analyzes videos.

It can detect:

- Objects
- Scenes
- Activities
- Time positions

Example:

A security camera video.

The API can report:

```
00:05 Person enters

01:10 Car detected

02:20 Dog detected
```

---

## 7. Document AI

Transforms unstructured documents into structured data.

Supported documents include:

- Invoices
- Contracts
- IDs
- Forms
- Receipts

Example:

Input

```
PDF Invoice
```

Output

```
Invoice Number

Customer Name

Total

Tax
```

Perfect for automating document processing.

---

## 8. AutoML

AutoML allows developers without ML expertise to train custom models.

Supported data types:

- Images
- Videos
- Tables

No coding is required.

Example:

A company has 20,000 product images.

Instead of using Google's generic image model, they train their own model to recognize their specific products.

---

## 9. Custom Machine Learning

If pre-trained APIs are insufficient, developers can build their own models.

Popular frameworks:

- TensorFlow
- PyTorch

This provides complete flexibility but requires ML knowledge.

---

# How Pre-trained APIs Work

The workflow is straightforward.

```
Application

↓

REST API

↓

Google AI Model

↓

JSON Response
```

Example

Application sends:

```
Image
```

Vision API returns:

```json
{
  "label": "Dog",
  "confidence": 98%
}
```

No model training is required.

---

# Example Use Case

Suppose you're building a social media application.

When a user uploads a photo:

1. Store image in Cloud Storage
2. Call Vision API
3. Receive labels
4. Save labels into Firestore
5. Enable image search

The application becomes "smart" with only a few API calls.

---

# Traditional Programming vs Machine Learning vs Generative AI

Understanding the difference between these three concepts is extremely important.

---

## Traditional Programming

You explicitly define every rule.

```
Rules
+
Input

↓

Answer
```

Example

```
IF animal has
4 legs
2 ears
fur

THEN

Cat
```

Problem:

Writing every possible rule is impossible.

---

## Machine Learning

Instead of writing rules, you provide examples.

```
Data
+
Correct Answers

↓

Model learns rules

↓

Prediction
```

Example

Show the model:

- 1,000 cat pictures
- 1,000 dog pictures

The model learns the differences.

When a new image arrives:

```
Prediction:

Cat
```

Machine Learning is excellent for solving one specific problem.

---

## Generative AI

Generative AI is much broader.

Instead of learning one task, it learns knowledge from enormous datasets.

```
Internet

Books

Images

Code

Videos

↓

Training

↓

Foundation Model

↓

Prompt

↓

Generated Response
```

Rather than simply recognizing data, it creates new content.

Examples:

- Write articles
- Generate code
- Answer questions
- Create images
- Summarize documents

---

# Foundation Models

A Foundation Model is a very large AI model trained on massive datasets.

It already understands:

- Language
- Images
- Logic
- Programming
- Reasoning

Instead of training from scratch, developers simply use the existing model.

Examples include:

- Gemini
- GPT
- Claude

---

# Large Language Models (LLMs)

LLMs are Foundation Models specialized for language.

They predict the next most likely word based on context.

Example prompt:

```
Explain Cloud Run
```

The model generates a complete explanation.

---

## Why are they called "Large"?

Because they have:

### Huge datasets

Sometimes petabytes of data.

### Billions or trillions of parameters

Parameters represent everything the model has learned during training.

More parameters generally mean:

- Better reasoning
- Better language understanding
- Better predictions

---

# Pre-training vs Fine-tuning

## Pre-training

Google trains a general-purpose model using enormous datasets.

The result is a Foundation Model.

---

## Fine-tuning

Later, a company can continue training that model using its own data.

Example

General model:

Knows medicine.

Hospital fine-tunes it using:

- Medical guidelines
- Internal documentation
- Research papers

Now the model becomes a medical assistant.

---

# Prompt

A Prompt is simply the instruction you give the model.

Examples:

```
Summarize this article.

Explain Kubernetes.

Generate React code.

Translate this sentence.
```

The better the prompt, the better the response.

---

# Generative AI Use Cases

## Content Creation

Generate:

- Articles
- Emails
- Stories
- Images
- Marketing content

---

## Knowledge Summarization

Examples:

- Summarize PDFs
- Summarize meetings
- Summarize videos
- Summarize long articles

---

## Search & Discovery

Examples:

- Semantic search
- Product recommendations
- Document search

---

## Workflow Automation

Examples:

- Contract extraction
- Ticket classification
- Invoice processing
- Customer support automation

---

# AI for Software Development

Generative AI is transforming software development.

Modern coding assistants (such as Gemini) can:

## Generate Code

Example

```
Create a React login page.
```

---

## Explain Code

```
Explain this function.
```

---

## Fix Bugs

```
Find the bug and fix it.
```

---

## Generate Unit Tests

```
Generate Jest tests.
```

---

## Complete Code

While typing, AI predicts the remaining code.

---

## Translate Code

Example

```
Python

↓

Java
```

---

## Generate Documentation

AI can automatically produce:

- Comments
- README files
- API documentation
- Release notes

---

# Summary

| Service              | Purpose                                |
| -------------------- | -------------------------------------- |
| Vision AI            | Analyze images                         |
| Speech-to-Text       | Audio → Text                           |
| Text-to-Speech       | Text → Audio                           |
| Translation AI       | Language translation                   |
| Natural Language AI  | Understand text                        |
| Video Intelligence   | Analyze videos                         |
| Document AI          | Extract structured data from documents |
| AutoML               | Train custom ML models without coding  |
| TensorFlow / PyTorch | Build fully custom ML models           |

---

# Key Takeaways

- Google Cloud provides pre-trained AI APIs that require no ML expertise.
- Vision AI, Speech, Translation, Natural Language, Video AI, and Document AI solve common AI problems.
- AutoML enables custom model training without writing ML code.
- Traditional programming uses manually written rules.
- Machine Learning learns rules from labeled examples.
- Generative AI learns from massive datasets and creates new content.
- Foundation Models are large pre-trained models that can be used directly or fine-tuned.
- Large Language Models (LLMs) specialize in language understanding and generation.
- Prompts are the instructions given to generative AI models.
- AI coding assistants such as Gemini help developers generate, explain, optimize, and test code.
