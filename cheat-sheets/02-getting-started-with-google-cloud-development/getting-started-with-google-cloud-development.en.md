Harika. Bence bu format tam istediğin gibi: **kitap gibi okunuyor, öğretici, sınav odaklı ve gereksiz ayrıntıya girmiyor.** Bundan sonra da tüm modülleri bu formatta hazırlayabiliriz.

---

````md
# Module 2 - Getting Started with Google Cloud Development

> Learn how applications communicate with Google Cloud services and discover the tools Google provides for cloud development.

---

# 🎯 Learning Objectives

After completing this module, you will be able to:

- Explain how applications communicate with Google Cloud.
- Understand the purpose of Cloud APIs.
- Differentiate between REST and gRPC.
- Understand the role of the Google Cloud SDK.
- Know when to use the gcloud CLI, Cloud Client Libraries, Cloud Shell, and Cloud Code.
- Explain how Google Cloud simplifies the developer experience.

---

# The Big Picture

Imagine you're developing a web application using Node.js.

A user uploads a profile picture, and you want to store it in **Cloud Storage**.

How does your application communicate with Google's infrastructure?

This module answers that question.

The communication flow is always similar:

```text
Application
      │
      ▼
Cloud API
      │
      ▼
Google Cloud Service
```

Every Google Cloud service is accessed through an API.

---

# 1. Cloud APIs

Cloud APIs are the foundation of Google Cloud.

Every Google Cloud service exposes one or more APIs that allow applications to interact with it.

Examples include:

- Cloud Storage API
- Compute Engine API
- Firestore API
- BigQuery API
- Pub/Sub API

When your application wants to upload a file, create a virtual machine, or query a database, it sends a request to the corresponding Cloud API.

For example, when uploading a file:

```text
User
 │
 ▼
Application
 │
 ▼
Cloud Storage API
 │
 ▼
Cloud Storage
```

Your application never communicates directly with Google's storage infrastructure.

Everything goes through a Cloud API.

---

# 2. REST and gRPC

Cloud APIs support two communication protocols.

## REST

REST is the most common API style.

It uses:

- HTTP
- JSON

Example:

```http
POST /buckets

{
  "name": "images"
}
```

Advantages:

- Easy to understand
- Human-readable
- Simple to debug

REST is commonly used by developers and third-party integrations.

---

## gRPC

gRPC is Google's high-performance communication protocol.

Instead of JSON, it uses a compact binary format called Protocol Buffers.

Advantages:

- Faster communication
- Lower latency
- Smaller payloads
- Better performance

Most developers never use gRPC directly.

Google Cloud Client Libraries automatically use gRPC whenever possible.

---

# REST vs gRPC

| REST                  | gRPC                        |
| --------------------- | --------------------------- |
| Human-readable        | Binary protocol             |
| Uses HTTP + JSON      | Uses Protocol Buffers       |
| Easy to debug         | High performance            |
| Great for public APIs | Great for internal services |

---

# 3. Authentication

Google Cloud never trusts an incoming request automatically.

Before executing any operation, it asks:

> **Who are you?**

Every API request must include valid credentials.

Common authentication methods include:

- OAuth 2.0
- Service Accounts

Without authentication, Cloud APIs reject the request.

Authentication protects your Google Cloud resources from unauthorized access.

---

# 4. Google Cloud SDK

The Google Cloud SDK is a collection of tools that help developers interact with Google Cloud.

Think of it as a toolbox.

It includes tools such as:

- gcloud
- gcloud storage
- kubectl
- bq
- Local emulators

The SDK itself does not communicate with Google Cloud directly.

Instead, its tools use Cloud APIs behind the scenes.

---

# 5. Google Cloud CLI (gcloud)

The Google Cloud CLI is a command-line interface used to manage Google Cloud resources.

Example:

```bash
gcloud compute instances list
```

This command lists all Compute Engine virtual machines in your project.

Although the command looks simple, the CLI performs several tasks automatically:

1. Authenticates the user.
2. Builds the API request.
3. Sends the request to the appropriate Cloud API.
4. Displays the response.

Communication flow:

```text
Developer
     │
     ▼
gcloud CLI
     │
     ▼
Cloud API
     │
     ▼
Google Cloud
```

---

# 6. gcloud storage, gsutil, and bq

Google Cloud provides specialised command-line tools.

### gcloud storage

The modern command-line tool for managing Cloud Storage.

Examples:

- Upload files
- Download files
- Create buckets
- Delete objects

Google recommends using **gcloud storage** instead of **gsutil** for new projects.

---

### gsutil

The original Cloud Storage command-line tool.

It is still supported but is gradually being replaced by **gcloud storage**.

---

### bq

The command-line tool for BigQuery.

It is primarily used to:

- Run SQL queries
- Manage datasets
- Manage tables

---

# 7. Cloud Client Libraries

Applications rarely call Cloud APIs directly.

Instead, Google recommends using **Cloud Client Libraries**.

For example, in Node.js:

```javascript
const { Storage } = require("@google-cloud/storage");

const storage = new Storage();
```

The library automatically handles:

- Authentication
- Retries
- Error handling
- Request formatting
- gRPC optimisation

This allows developers to focus on writing application logic instead of networking code.

---

# 8. Cloud Shell

Cloud Shell is a browser-based Linux environment provided by Google Cloud.

It includes:

- Google Cloud SDK
- Git
- kubectl
- Docker

No installation is required.

You simply open Cloud Shell from the Google Cloud Console and start working immediately.

Cloud Shell is ideal when working from different computers or when you do not want to install the SDK locally.

---

# 9. Cloud Code

Cloud Code is a set of IDE extensions for Google Cloud.

It is available for:

- Visual Studio Code
- JetBrains IDEs
- Cloud Shell Editor

Cloud Code allows developers to:

- Deploy Cloud Run services
- Develop Kubernetes applications
- Browse Cloud APIs
- Access Secret Manager
- View logs
- Edit Kubernetes YAML files with autocomplete

Instead of switching between the IDE and the Google Cloud Console, many cloud operations can be performed directly inside the editor.

---

# 10. Local Emulators

Developing directly against cloud services can be slow and expensive.

Google provides local emulators for several services.

Examples include:

- Firestore
- Pub/Sub
- Bigtable
- Datastore
- Spanner

Your application connects to the emulator instead of the real cloud service.

Benefits:

- Faster development
- No internet connection required
- No cloud resource costs
- Safe testing environment

---

# 11. Cloud Workstations

Cloud Workstations provides fully managed cloud development environments.

Instead of installing software locally, developers receive a preconfigured development machine.

Benefits include:

- Consistent environments
- Faster onboarding
- Improved security
- Browser-based access
- Remote development from anywhere

Since the development environment runs inside the customer's Google Cloud project, source code remains secure.

---

# Module Summary

This module teaches a single fundamental concept:

Every interaction with Google Cloud follows a similar communication path.

```text
                 Developer
                      │
        ┌─────────────┴─────────────┐
        │                           │
   Application                  Terminal
        │                           │
Cloud Client Library          gcloud CLI
        │                           │
        └─────────────┬─────────────┘
                      │
                  Cloud API
                      │
                      ▼
           Google Cloud Service
```

Understanding this architecture makes it much easier to learn every Google Cloud product that follows.

---

# Key Takeaways

- Every Google Cloud service exposes one or more Cloud APIs.
- REST and gRPC are two supported communication protocols.
- Authentication is required for every API request.
- The Google Cloud SDK is a collection of developer tools.
- The gcloud CLI simplifies Cloud API usage from the terminal.
- Cloud Client Libraries are the recommended way to access Google Cloud from applications.
- Cloud Shell provides a ready-to-use development environment.
- Cloud Code integrates Google Cloud directly into your IDE.
- Local emulators allow offline development without consuming cloud resources.
- Cloud Workstations provide secure, cloud-hosted development environments.

---

# Certification Tips

For the Google Cloud Developer Certification, remember these distinctions:

| Concept              | Remember                                         |
| -------------------- | ------------------------------------------------ |
| Cloud API            | Programmatic interface to Google Cloud services  |
| REST                 | HTTP + JSON communication                        |
| gRPC                 | High-performance binary communication            |
| Google Cloud SDK     | Collection of developer tools                    |
| gcloud CLI           | Command-line tool included in the SDK            |
| Cloud Client Library | Recommended for application development          |
| Cloud Shell          | Browser-based development environment            |
| Cloud Code           | Google Cloud integration for IDEs                |
| Local Emulator       | Local version of a cloud service for development |
| Cloud Workstations   | Managed cloud development environment            |

A simple way to remember this module is:

> **Application → Client Library (or CLI) → Cloud API → Google Cloud Service**
````

Bu formatın bence en büyük avantajı şu: **Google'ın anlattığı sırayı koruyor**, ama transcript'i birebir kopyalamıyor. Okuyan biri videoyu izlemeden de konuyu öğrenebilir ve sertifika için gerekli bilgiyi edinir.
