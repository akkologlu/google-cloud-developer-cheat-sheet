# Module 6 - Deploying Applications

---

# Overview

Building an application is only part of the software development lifecycle. The next challenge is deploying it safely, consistently, and automatically.

Google Cloud provides a complete deployment ecosystem that allows developers to:

- Build applications automatically
- Run automated tests
- Package applications into containers
- Store build artifacts securely
- Deploy applications to different environments
- Roll back quickly if something goes wrong

The main services introduced in this module are:

- Cloud Build
- Artifact Registry
- Cloud Deploy
- Cloud Monitoring
- Software Delivery Shield

---

# What is CI/CD?

CI/CD stands for **Continuous Integration** and **Continuous Delivery/Deployment**.

It is an automated pipeline that builds, tests, and deploys software.

Instead of manually building and deploying applications, every step happens automatically.

```text
Developer

↓

Git Commit

↓

CI

↓

Build

↓

Test

↓

Artifact

↓

CD

↓

Deploy

↓

Production
```

---

# Continuous Integration (CI)

Continuous Integration focuses on validating every code change.

Whenever a developer pushes code to the repository, an automated build process starts.

Typical CI steps:

1. Download source code
2. Install dependencies
3. Compile the project
4. Run unit tests
5. Build the application
6. Create a container image
7. Store the build artifact

Example:

```text
git push feature/login

↓

Cloud Build

↓

npm install

↓

npm test

↓

npm build

↓

Docker build

↓

Push Image
```

If any step fails, the build stops immediately.

This prevents broken code from reaching the main branch.

---

# Continuous Delivery (CD)

Continuous Delivery begins after CI succeeds.

The application is automatically deployed to testing environments.

Typical flow:

```text
Artifact Registry

↓

Cloud Deploy

↓

Staging

↓

Integration Tests

↓

Manual Approval

↓

Production
```

Production deployment requires human approval.

---

# Continuous Deployment

Continuous Deployment removes the manual approval step.

If every automated test passes, deployment happens automatically.

```text
CI

↓

Tests Passed

↓

Production
```

This approach is commonly used by companies with mature testing pipelines.

---

# Staging Environment

A staging environment is a production-like environment used for testing before releasing software.

It usually has:

- Same infrastructure
- Same configuration
- Same database schema
- Same application version

But it is used only by testers and developers.

```text
Developer

↓

Deploy

↓

Staging

↓

QA Testing

↓

Production
```

---

# Release Candidate (RC)

A Release Candidate is a build that has passed all automated testing and is considered ready for production.

Example:

```text
Version 2.3.0

↓

All Tests Passed

↓

Release Candidate
```

It is the final version before production deployment.

---

# Canary Deployment

Instead of deploying to every user immediately, traffic is gradually shifted.

Example:

```text
10%

↓

25%

↓

50%

↓

100%
```

Advantages:

- Lower risk
- Easier monitoring
- Quick rollback

---

# Blue-Green Deployment

Two production environments exist simultaneously.

```text
Blue → Current Version

Green → New Version
```

Initially:

```text
Users

↓

Blue
```

After validation:

```text
Users

↓

Green
```

If problems occur:

```text
Green Failed

↓

Switch Traffic

↓

Blue
```

Rollback is almost instantaneous.

---

# Rollback

Rollback means returning to the previous stable version.

```text
Version 2

↓

Bug Found

↓

Rollback

↓

Version 1
```

This minimizes downtime.

---

# Containers

Google Cloud primarily deploys applications as containers.

A container packages:

- Application code
- Runtime
- Libraries
- Dependencies
- Configuration

Example:

```text
Container

├── Application
├── Node.js Runtime
├── Libraries
├── Dependencies
└── Configuration
```

This package is called a **Container Image**.

---

# Virtual Machines vs Containers

## Virtual Machine

```text
Hardware

↓

Hypervisor

↓

Guest Operating System

↓

Application
```

Every VM contains its own operating system.

Pros:

- Strong isolation

Cons:

- Slow startup
- High resource usage

---

## Container

```text
Hardware

↓

Host Operating System

↓

Container Runtime

↓

Container A

↓

Container B

↓

Container C
```

Containers share the host operating system.

Advantages:

- Lightweight
- Fast startup
- Lower resource usage
- Higher density

---

# Benefits of Containers

## Portability

The same container image runs everywhere.

```text
Developer Laptop

↓

Container Image

↓

Cloud Run

↓

Same Application
```

---

## Isolation

Different applications can use different runtime versions without conflicts.

Example:

Project A

```text
Node.js 18
```

Project B

```text
Node.js 22
```

Both can run on the same server.

---

## Consistency

The same image is deployed to:

- Development
- Testing
- Production

This eliminates environment differences.

---

# Container Image

A container image is a read-only package containing everything needed to run an application.

```text
Container Image

↓

Run

↓

Container
```

Image = Template

Container = Running instance

---

# Cloud Build

Cloud Build is Google Cloud's managed build service.

It automatically:

- Builds applications
- Runs tests
- Builds Docker images
- Pushes images to Artifact Registry

Workflow:

```text
Git Push

↓

Cloud Build

↓

Docker Image

↓

Artifact Registry
```

No build servers need to be managed manually.

---

# Build Triggers

Cloud Build starts automatically based on events.

Examples:

```text
Push to main

↓

Build
```

```text
Push to release/*

↓

Build
```

```text
Git Tag

↓

Build
```

---

# cloudbuild.yaml

Cloud Build pipelines are defined in a YAML file.

Example:

```yaml
steps:
  - npm install
  - npm test
  - docker build
  - docker push
```

Each step runs inside its own Docker container.

---

# Workspace

Cloud Build creates a shared directory:

```text
/workspace
```

Every build step can read and write files there.

```text
Step 1

↓

Generate Files

↓

/workspace

↓

Step 2

↓

Use Files

↓

/workspace

↓

Step 3
```

This allows artifacts to be shared across build steps.

---

# Artifact Registry

Artifact Registry stores build outputs.

Examples include:

- Docker Images
- Maven Packages
- npm Packages
- Python Packages

Example:

```text
my-app

├── v1.0
├── v1.1
└── v2.0
```

Cloud Run and GKE pull images directly from Artifact Registry during deployment.

---

# CI/CD Security

Modern software supply chains require more than automated deployments.

The entire build pipeline must also be secure.

Google Cloud provides multiple services for securing the software delivery process.

---

# Software Delivery Shield

Software Delivery Shield protects the complete CI/CD pipeline.

It provides:

- Secure build infrastructure
- Trusted artifacts
- Vulnerability scanning
- Deployment validation

Its goal is to ensure that only trusted software reaches production.

---

# Assured Open Source Software

Most applications depend on open-source libraries.

Examples:

- React
- Axios
- Lodash

Google verifies and continuously scans selected open-source packages for vulnerabilities.

Developers can safely use these verified packages.

---

# Artifact Analysis

Artifact Analysis scans images stored in Artifact Registry.

It detects:

- Vulnerabilities
- Outdated dependencies
- Security issues

Scanning continues even after deployment as new vulnerabilities are discovered.

---

# Binary Authorization

Binary Authorization ensures that only trusted images can be deployed.

Typical deployment policy:

- Built by Cloud Build
- Successfully scanned
- Properly signed
- Security checks passed

If any requirement is missing, deployment is blocked.

This prevents unauthorized or malicious images from reaching production.

---

# Module Summary

```text
Developer
    │
    ▼
Git Commit
    │
    ▼
Cloud Build (CI)
    │
    ├── Build
    ├── Test
    └── Docker Image
    │
    ▼
Artifact Registry
    │
    ▼
Cloud Deploy (CD)
    │
    ▼
Staging
    │
    ▼
Approval (Continuous Delivery)
    │
    ▼
Production (Cloud Run / GKE)
    │
    ▼
Cloud Monitoring
```

---

# Key Takeaways

- CI automatically builds and tests every code change.
- CD deploys validated builds to staging and production environments.
- Containers package applications with all dependencies for consistent deployment.
- Cloud Build automates builds and creates container images.
- Artifact Registry stores versioned build artifacts.
- Cloud Deploy automates deployments across environments.
- Canary and Blue-Green deployments reduce deployment risk.
- Rollback enables rapid recovery from failed releases.
- Software Delivery Shield secures the entire software supply chain.
- Binary Authorization ensures that only trusted artifacts are deployed.
