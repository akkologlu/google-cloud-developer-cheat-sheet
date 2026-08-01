````md
# Module 4 – Authentication & Authorization

> **Course:** Developing Applications with Google Cloud – Foundations

---

# Authentication vs Authorization

This is the most important concept in this module.

People often confuse these two terms, but they solve completely different problems.

## Authentication (AuthN)

Authentication answers the question:

> **"Who are you?"**

It verifies identity.

Examples:

- Logging in with Google
- Logging in with GitHub
- Logging in with email/password
- A Cloud Run service proving its identity to Google Cloud

Examples:

- User → "I am Abdullah."
- Application → "I am backend-service@project.iam.gserviceaccount.com"

---

## Authorization (AuthZ)

Authorization answers the question:

> **"What are you allowed to do?"**

After identity is verified, Google checks permissions.

Example:

```
User:
✔ Read Storage Bucket
✔ Upload Files
❌ Delete Bucket
```

Authentication happens first.

Authorization happens second.

```
Authentication
      ↓
Who are you?

Authorization
      ↓
What can you do?
```

---

# IAM (Identity and Access Management)

IAM is Google's authorization system.

It defines

- WHO can access
- WHICH resource
- WITH WHICH permissions

Google describes IAM with three concepts:

```
Principal
↓

Role
↓

Resource
```

Example:

```
Principal:
abdullah@gmail.com

Role:
Storage Object Viewer

Resource:
photos-bucket
```

Result:

Abdullah can only read objects inside the bucket.

---

# IAM Principals

A Principal is simply an identity.

Possible principals are:

## Google Account

Represents a real person.

Example:

```
john@gmail.com
```

Used for:

- Developers
- Administrators
- End users

---

## Service Account

Represents an application instead of a human.

Example:

```
backend-service@project.iam.gserviceaccount.com
```

This is probably the most important concept in Google Cloud.

Imagine your application needs to read files from Cloud Storage.

Who is making the request?

Not the user.

The application itself.

Therefore the application also needs an identity.

That identity is a Service Account.

Think of it as an employee badge for your application.

```
Human
↓

Google Account

Application
↓

Service Account
```

Instead of giving permissions to your code, you give permissions to the Service Account.

Example:

```
Cloud Run

↓

Attached Service Account

↓

Storage Admin

↓

Cloud Storage
```

Whenever Cloud Run calls Cloud Storage,

Google sees

> "This request is coming from this Service Account."

and checks its IAM roles.

---

## Google Group

A collection of users.

Instead of granting permissions one by one,

grant the permission to the group.

Example

```
Backend Team

↓

Alice
Bob
Charlie
David
```

Give the group one role.

Everyone inherits it.

---

## Google Workspace

Represents your entire company.

Example:

```
company.com
```

Useful for managing company-wide permissions.

---

## Cloud Identity

Similar to Workspace,

but without Gmail, Docs, Drive etc.

Identity management only.

---

# Roles

Permissions are never assigned directly.

Instead,

Google groups permissions into Roles.

Example

```
Storage Viewer

contains

storage.objects.get
storage.objects.list
```

Assign the role,

not individual permissions.

---

## Types of Roles

### Basic Roles

Very broad.

Examples

```
Viewer

Editor

Owner
```

Mostly for testing.

Avoid in production.

---

### Predefined Roles

Created by Google.

Example

```
Cloud Run Invoker

Storage Object Viewer

BigQuery Admin
```

Most commonly used.

---

### Custom Roles

Created by you.

Useful when Google's predefined roles are too permissive.

Example

```
Can Read Bucket

Can Upload Images

Cannot Delete
```

---

# Principle of Least Privilege

Always give the minimum permissions required.

Bad

```
Cloud Storage Admin
```

Good

```
Storage Object Viewer
```

If an account is compromised,

damage is limited.

---

# API Authentication Methods

Applications can authenticate to Google Cloud APIs in several ways.

## 1. API Key

Very simple.

Just a string.

```
API Key

↓

Google API
```

Mostly used for

- Maps
- Public APIs

Not suitable for sensitive operations.

Why?

Because anyone who gets the key can use it.

---

## 2. OAuth User Account

Represents a human user.

```
User

↓

Google Login

↓

OAuth Token

↓

Google API
```

The OAuth token

- expires automatically
- is more secure than an API key
- carries the user's permissions

---

## 3. Service Account

Represents an application.

```
Application

↓

Service Account

↓

OAuth Token

↓

Google APIs
```

This is the preferred authentication method for backend applications.

---

# Why Service Accounts Exist

Imagine this backend:

```
Frontend

↓

Backend

↓

Cloud Storage
```

Who should access Cloud Storage?

Not every user.

Only the backend.

Therefore:

```
Backend

↓

Service Account

↓

Cloud Storage
```

Users never receive Storage credentials.

The backend performs operations on their behalf.

---

# Service Account Keys

Originally,

applications downloaded a JSON key file.

```
service-account.json
```

This file contains the private key.

Using it,

the application requests OAuth access tokens.

Problem:

If someone steals this JSON,

they become your application.

That is why Google recommends avoiding downloaded keys whenever possible.

---

# Authentication Decision Tree

Google recommends different authentication methods depending on where your application runs.

## Scenario 1

Application runs locally.

Example:

```
VS Code

↓

Node.js

↓

Google APIs
```

Use

```
gcloud auth application-default login
```

This stores temporary user credentials on your machine.

Good for development only.

---

## Scenario 2

Application runs on Google Cloud

Examples

- Cloud Run
- Compute Engine
- Cloud Functions

Attach a Service Account directly.

```
Cloud Run

↓

Attached Service Account

↓

Google APIs
```

No JSON key required.

This is Google's recommended production approach.

---

## Scenario 3

Application runs on GKE

Do not use service account JSON files.

Instead use

```
Workload Identity
```

Flow:

```
Pod

↓

Kubernetes Service Account

↓

IAM Service Account

↓

Google APIs
```

This automatically exchanges Kubernetes credentials for Google credentials.

More secure.

Easier to manage.

---

## Scenario 4

Application runs outside Google Cloud

Examples

- AWS
- Azure
- On-premises

If possible,

use

```
Workload Identity Federation
```

Instead of storing a Google key,

Google trusts another identity provider.

Flow:

```
AWS

↓

OIDC Token

↓

Google

↓

Temporary Access Token

↓

Google APIs
```

No service account key is stored.

---

## Scenario 5

Federation is impossible

Last resort:

```
Service Account JSON Key
```

Google strongly recommends avoiding this whenever possible.

---

# Application Default Credentials (ADC)

ADC is one of the most important concepts in Google Cloud.

It is **not** a credential.

It is a mechanism.

Its job is simply:

> "Find credentials automatically."

Instead of writing code like this:

```python
client = StorageClient(key="service-account.json")
```

You simply write:

```python
client = StorageClient()
```

The library asks ADC:

> "Please find credentials for me."

ADC then searches in this order.

---

## Step 1

Check

```
GOOGLE_APPLICATION_CREDENTIALS
```

If this environment variable exists,

ADC loads that JSON file.

---

## Step 2

If not found,

look for

```
gcloud auth application-default login
```

credentials.

Used during development.

---

## Step 3

If still nothing,

look for an attached Service Account.

Example

```
Cloud Run

↓

Attached Service Account
```

ADC automatically uses it.

---

## Why ADC Exists

Because your code never changes.

Local:

```
Developer

↓

ADC

↓

User Credentials
```

Production:

```
Cloud Run

↓

ADC

↓

Attached Service Account
```

Same code.

Different credentials.

No code modification required.

---

# OAuth 2.0

Sometimes your application must access resources that belong to the user.

Example:

```
User's Google Drive

User's BigQuery Dataset
```

The application asks for permission.

The user approves.

Google returns an OAuth token.

Your application uses that token on behalf of the user.

---

# Identity-Aware Proxy (IAP)

Normally,

developers protect applications themselves.

Example:

```
if user is admin:
    allow
else:
    deny
```

IAP moves this responsibility to Google.

```
User

↓

IAP

↓

Cloud Run
```

Google checks identity before the request reaches your application.

No authentication code required inside your app.

---

# Firebase Authentication

Firebase Authentication is designed for application users.

Supports:

- Email/password
- Google
- Apple
- GitHub
- Phone authentication

It provides

- Login UI
- SDKs
- Token generation
- Password recovery

Ideal for mobile and web applications.

---

# Identity Platform

Identity Platform is essentially Firebase Authentication for enterprise environments.

Additional features include:

- Multi-factor Authentication (MFA)
- OpenID Connect
- SAML
- Better IAM integration
- Identity-Aware Proxy integration

---

# Secret Manager

Applications often need secrets such as:

- API keys
- Passwords
- Certificates
- Database credentials

Never store these in:

- source code
- Git repositories
- configuration files

Instead use Secret Manager.

```
Application

↓

Secret Manager

↓

Database Password
```

Benefits:

- IAM-controlled access
- Versioning
- Audit logs
- Encryption
- Cloud KMS integration

Only authorised users and services can retrieve secrets.

---

# Module Summary

By the end of this module, you should understand:

- The difference between Authentication and Authorization.
- How IAM controls access using Principals, Roles and Resources.
- Why Service Accounts represent applications.
- Why downloaded Service Account keys are discouraged.
- How ADC automatically discovers credentials.
- When to use Local Login, Attached Service Accounts, Workload Identity, Workload Identity Federation, or Service Account Keys.
- The purpose of OAuth 2.0, IAP, Firebase Authentication and Identity Platform.
- Why Secret Manager is the recommended place for storing sensitive credentials.
````
