# Module 5 — Adding Intelligence to Your Application: Practice Questions

Scenario-based practice questions for the **Adding Intelligence to Your Application** module of the Professional Cloud Developer certification path. These questions are weighted toward the exam traps and decision-table distinctions from the deep dive — especially pre-trained API vs AutoML vs custom model vs generative AI, and the "narrow ML vs generative AI" boundary.

Work through the **Questions** section first, then check your answers against **Answer Key & Explanations**.

---

## Questions

**1.** A photo-sharing app wants to automatically tag which guests in a wedding photo are smiling, surprised, or wearing headwear like hats or glasses — purely by analyzing the image itself. Which capability should the developer use?

A. Vision AI, using its face detection capability
B. Natural Language AI, since it can detect sentiment
C. Video AI, since photos are treated as single-frame video
D. Document AI, since faces are a form of unstructured data

**2.** A travel app lets users upload a photo and asks the backend to identify the landmark in it. A user uploads a photo of a Sphinx statue, and the app correctly reports that it's the replica outside a Las Vegas hotel, not the original in Giza, Egypt. Which service is responsible for this level of contextual precision, out of the box?

A. A custom AutoML image classifier trained specifically on Sphinx photos
B. Generative AI producing an image caption
C. Vision AI's landmark detection
D. Document AI's structured extraction

**3.** An e-commerce site serves visitors worldwide and wants product descriptions shown in each visitor's browser language, generated on the fly at request time — not maintained as a set of pre-translated static files per locale. Which approach fits best?

A. Pre-generate and store a translated copy of every product page for each supported locale ahead of time
B. Document AI
C. Natural Language AI
D. Cloud Translation API, translating text dynamically at request time

**4.** A company wants to monitor social media mentions of its product to determine whether customers are satisfied, and also wants to auto-route messages based on whether the customer intends to buy, complain, or request support. A developer proposes routing every message through a general-purpose LLM with a custom prompt to extract sentiment and intent. Is this the best-fit approach?

A. Yes — only a general-purpose LLM (generative AI) can determine sentiment and intent from text
B. No — Natural Language AI is purpose-built to extract sentiment and intent from text and is the better fit for this narrow, well-defined task
C. No — this requires Video AI, since social posts often include images and video
D. Yes — Speech-to-Text should be used to process the text first

**5.** A media company has a large video archive and needs to find every moment a specific company logo appears on screen across hours of footage, along with the exact timestamp of each appearance. Which service is designed for this?

A. Video AI (Video Intelligence API), which annotates entities at the shot, frame, or video level along with timing
B. Vision AI, since it already detects logos in images
C. Document AI
D. Natural Language AI

**6.** A finance team has thousands of scanned invoices in inconsistent layouts and fonts. They don't just want the raw text pulled off the page — they want consistent, queryable fields like invoice date, total amount, and vendor name extracted automatically. Which service fits?

A. Vision AI's OCR capability, which extracts raw text from scanned pages
B. Translation AI
C. Document AI, which converts unstructured documents into structured data
D. Agent Platform AutoML

**7.** A boutique retailer has years of proprietary sales data in spreadsheets and wants to train a model to predict customer churn using that data. They have no machine learning engineers on staff and don't want to write any training code. Which option fits?

A. A pre-trained API, since it requires no ML knowledge
B. A custom model built with TensorFlow or PyTorch
C. Fine-tuning a large language model on their spreadsheet data
D. Agent Platform AutoML, training a custom model on their own data with no code

**8.** A robotics company needs a highly specialized model for a sensor-fusion problem that no standard Google Cloud API addresses. They employ a team of experienced ML engineers who want full control over the model architecture. Which option fits best?

A. Agent Platform AutoML
B. Build and train a custom model using TensorFlow or PyTorch
C. A pre-trained API
D. Generative AI content creation

**9.** A developer writes explicit logic: `if legs == 4 and ears == 2 and has_fur: classify as cat`. Which approach does this represent, and how does it relate to machine learning and generative AI?

A. Traditional programming — a human wrote the rule by hand; this is a different, earlier approach than ML models that learn rules from data
B. Narrow machine learning — the machine learned this rule from labeled cat photos
C. Generative AI — the system is generating new classification rules on demand
D. Agent Platform AutoML — the rule was inferred automatically without writing code

**10.** A startup wants a feature where a user types "tell me everything you know about golden retrievers" and receives a rich, freely composed paragraph in response — not just a label like "dog: yes." Which paradigm fits, and why isn't a narrow pre-trained classification API enough here?

A. Vision AI — it can label the concept "dog" from an uploaded photo
B. Natural Language AI — it extracts structured entities from the query text
C. Generative AI (LLM) — trained on massive, multi-modal data to produce new, open-ended content rather than a narrow classification
D. Agent Platform AutoML — train a custom classifier on golden retriever images

**11.** Which statement correctly distinguishes a "foundation model" from a "large language model (LLM)"?

A. They are the same thing; the terms are interchangeable in every context
B. A foundation model is always narrow-task, while an LLM is always general-purpose
C. A foundation model is always fine-tuned before use, while an LLM is always used directly without fine-tuning
D. An LLM is the most popular type of foundation model and is trained only on text data; foundation models in general can also be trained on other data types, such as images or code

**12.** A legal-tech company wants a general-purpose LLM to produce contract language matching their firm's specific style and clause library. They further train the existing model on a small internal dataset of past contracts. What process are they performing?

A. Pre-training — building a brand-new foundation model from scratch using their contracts
B. Fine-tuning — further training the existing pre-trained model on a smaller, domain-specific dataset
C. Agent Platform AutoML — training a no-code image classifier
D. Prompt engineering only, with no additional model training

**13.** A product manager claims an LLM is called "large" only because of the number of parameters it has. Is this accurate?

A. No — "large" refers to both the massive scale of the training dataset (sometimes petabytes) and the huge number of parameters (billions to trillions)
B. Yes — "large" refers exclusively to parameter count
C. Yes — "large" refers exclusively to the size of the training dataset
D. No — "large" refers to the number of concurrent API requests the model can serve

**14.** Google's own meeting-room occupancy system works like this: a Pub/Sub notification fires every 30 seconds indicating whether motion was detected, plus notifications when a meeting starts or ends. If motion is detected between 6 and 8 minutes after a meeting's scheduled start, the room is marked occupied; otherwise it's released. What does this example primarily illustrate?

A. That every ML-powered feature requires a large, complex custom-trained model
B. That Video AI must be used to analyze the conference room camera feed frame by frame
C. That a simple ML signal (motion detected or not), combined with straightforward timing/business logic and event-driven messaging, can solve a real problem without a complex model
D. That this scenario requires a fine-tuned LLM to interpret the room's state

**15.** A developer new to a large legacy codebase wants an AI assistant to describe what an unfamiliar function does and how it works, before making any changes. Which Gemini-powered code-assistant capability are they using?

A. Code completion — suggesting the next line as they type
B. Code translation — converting the function to another programming language
C. Documentation generation — writing release notes for recent changes
D. Code explanation — describing what existing code does and how it does it

---

## Answer Key & Explanations

**1. Correct: A — Vision AI, using its face detection capability**
Vision AI's face detection returns information about detected faces, including emotional expressions (happy, surprised) and headwear. Natural Language AI is tempting because "sentiment" sounds similar, but sentiment analysis works on text, not on faces in an image.

**2. Correct: C — Vision AI's landmark detection**
The deep dive explicitly calls out this exact example: Vision AI's landmark detection can distinguish the Las Vegas replica from the original Egyptian Sphinx out of the box. Training a custom AutoML classifier is tempting but unnecessary — the pre-trained model already captures this contextual detail without any custom training.

**3. Correct: D — Cloud Translation API, translating text dynamically at request time**
The module stresses that Cloud Translation API is "highly responsive" and enables fast, dynamic translation at the moment it's needed. Pre-generating and storing static translated files (option A) is the older, traditional localization approach the API is meant to replace.

**4. Correct: B — No, Natural Language AI is purpose-built for this narrow, well-defined task**
This is the module's central exam trap: pre-trained APIs solve narrow, specific questions (here, sentiment and intent extraction), while generative AI is meant for general, open-ended problems. Reaching for a general-purpose LLM is technically possible but is not the best-fit, purpose-built solution when Natural Language AI already solves exactly this.

**5. Correct: A — Video AI (Video Intelligence API)**
Video AI adds the temporal dimension that Vision AI lacks — it can report not just that a logo appears, but exactly when and for how long, at the shot, frame, or video level. Vision AI is tempting because it also does logo detection, but only on individual images, not across time in a video archive.

**6. Correct: C — Document AI**
Document AI is specifically designed to convert unstructured documents into structured, queryable data (like the specific fields "date," "amount," "vendor") regardless of layout. Vision AI's OCR is tempting because it does extract text, but it only returns raw text — it doesn't produce consistent structured fields across varying invoice formats.

**7. Correct: D — Agent Platform AutoML**
AutoML lets a company with no ML engineers train a custom model on their own proprietary data, with no code. A generic pre-trained API (option A) also requires no ML knowledge, but it can't be trained on this retailer's specific sales data — it only offers Google's general-purpose model.

**8. Correct: B — Build and train a custom model using TensorFlow or PyTorch**
This scenario is the "far end of the spectrum" case from the deep dive: a highly specific problem not covered by any standard API, plus a team with real ML expertise wanting full architectural control. AutoML is tempting since it also trains on custom data, but it's designed for teams without deep ML expertise and doesn't offer the same level of architectural control.

**9. Correct: A — Traditional programming**
The rule (`legs == 4 and ears == 2 and has_fur`) was written by hand by a human, which is the definition of traditional programming in the module's three-way comparison. Narrow ML is tempting because it also classifies things, but ML rules are learned from labeled data, not hand-coded — and the module's exam trap explicitly warns that these three approaches (traditional programming, narrow ML, generative AI) are an evolution, not interchangeable alternatives.

**10. Correct: C — Generative AI (LLM)**
The scenario calls for open-ended, freely composed new content rather than a narrow yes/no or label-based answer — exactly the gap generative AI is designed to fill. Natural Language AI is tempting because it also processes text, but it extracts structured meaning from existing text (entities, sentiment) rather than generating new, open-ended content.

**11. Correct: D**
An LLM is the most popular type of foundation model and is trained only on text; foundation models as a broader category can be trained on other data types too, such as images or code. Option B is a common misreading — the module frames narrow-vs-general as the pre-trained-API-vs-generative-AI distinction, not as a property of foundation models themselves.

**12. Correct: B — Fine-tuning**
Fine-tuning is further training an already pre-trained model on a smaller, domain-specific dataset (here, the firm's past contracts) to adapt it to a specific purpose. Pre-training is tempting because it's also "training," but pre-training is the original, large-scale process that creates the general-purpose foundation model in the first place.

**13. Correct: A**
The module defines "large" in LLM as covering two things at once: the massive scale of the training dataset (sometimes petabytes) and the huge number of parameters (billions to trillions) the model learns during training. Options B and C are each tempting because they capture half of the correct definition, but neither alone is complete.

**14. Correct: C**
This example is explicitly framed in the module as a reminder that "not every ML problem requires a massive model" — a simple motion-detected/not-detected signal, combined with a timing rule (6–8 minutes) and Pub/Sub event messaging, is enough to solve a real business problem. Option A is the tempting-but-wrong assumption that "ML-powered" always implies a large, complex model.

**15. Correct: D — Code explanation**
Code explanation is specifically about describing what unfamiliar existing code does and how, which is exactly what's valuable when onboarding onto an unfamiliar codebase. Code completion is tempting because it's also a Gemini coding capability, but completion predicts new code as you type — it doesn't explain code that already exists.
