# Practice Questions — Module 7: Compute Options for Your Application

Scenario-based practice questions for the **Compute Options for Your Application** module (Compute Engine, GKE, Cloud Run, App Engine). These are weighted toward the exam traps and decision-table distinctions covered in the [deep dive](../../../deep-dive/07-compute-options-for-your-application/compute-options-for-your-application.md) — read that first if you haven't.

Answer all questions, then check your answers against the **Answer Key & Explanations** section below.

---

## Questions

**1.** Your company is retiring its on-premises data center. One of the applications you must migrate is a decade-old inventory management system tied to a commercial database license that is bound to a specific virtual hardware configuration. Leadership wants the migration completed with minimal code changes and no re-architecture. Which compute option should you choose?

A. Cloud Run, because it lets you deploy any container without managing servers
B. GKE Autopilot, because Google manages the underlying infrastructure for you
C. Compute Engine, because it lets you replicate the existing VM and OS configuration with full control over hardware-bound licensing
D. App Engine flexible environment, because it supports custom runtimes

**2.** A platform team wants to run containerized workloads on Kubernetes but has explicitly stated they do not want to provision, patch, or manage node pools or nodes at all — they want to specify workloads and let Google handle everything else, including infrastructure hardening. Which GKE mode should they choose?

A. GKE Standard, because it gives the most configuration flexibility
B. GKE Autopilot, because Google manages the control plane, nodes, and node pools entirely
C. Compute Engine managed instance groups running a self-installed Kubernetes distribution
D. GKE Standard with node auto-provisioning enabled

**3.** You are deploying a self-managed relational database inside a GKE cluster. Each replica needs a stable network identity and its own persistent volume that survives pod rescheduling. Which Kubernetes resource should you use to define this workload?

A. StatefulSet, because it provides stable identity and per-replica persistent storage
B. Deployment, because it keeps a fixed number of replicas running
C. A Kubernetes Service, because it exposes pods over the network
D. A managed instance group, because it handles persistent disks automatically

**4.** You need to run a nightly reconciliation process that reads a batch of records, processes them across multiple independent parallel tasks, retries any task that fails, and then exits when finished. It does not need to accept incoming HTTP requests. What should you use?

A. A Cloud Run service invoked by Cloud Scheduler
B. A GKE Deployment scaled to zero replicas when idle
C. A Compute Engine preemptible VM with a cron job
D. A Cloud Run job, triggered on a schedule with Cloud Scheduler

**5.** You are building a public web API that must respond to HTTP requests from clients in real time, scale up automatically as traffic spikes, and scale down to zero — and stop billing — when there's no traffic. What should you deploy?

A. Cloud Run job
B. GKE StatefulSet
C. Cloud Run service
D. Compute Engine managed instance group with autoscaling

**6.** Your security team requires full control over how the container image is assembled — the exact base image, every layer, and every file added to it — before it runs in production. Which Cloud Run deployment option satisfies this requirement?

A. Source-based deployment using Google Cloud buildpacks
B. Container-based deployment, supplying your own pre-built image
C. Continuous deployment from a GitHub repository
D. Cloud Run functions, since they auto-generate the image

**7.** A developer wants to deploy a small Python HTTP service to Cloud Run with a single `gcloud run deploy` command, without writing a Dockerfile or worrying about how the container image gets built. What should they use?

A. Source-based deployment, letting Google Cloud buildpacks build the image
B. Container-based deployment with a manually built image
C. GKE Autopilot, since it also builds images automatically
D. Cloud Run jobs, since they don't require a container image

**8.** A colleague claims that Cloud Run functions run on a separate, dedicated compute product distinct from Cloud Run, with its own infrastructure model. Is this accurate?

A. Yes, Cloud Run functions run on Cloud Functions infrastructure, a completely separate product
B. Yes, functions run only on App Engine standard environment
C. No, Cloud Run functions require a dedicated GKE cluster
D. No, Cloud Run functions are deployed as Cloud Run services under the hood

**9.** Your data science team runs a large overnight batch job that reprocesses several terabytes of data on Compute Engine. The job can tolerate being interrupted and restarted, and the team's top priority is minimizing compute cost. What should you configure?

A. Standard Compute Engine VMs with committed use discounts
B. GKE Autopilot with vertical pod autoscaling
C. Preemptible (Spot) VMs, which offer at least 60% off standard pricing but can be terminated by Google
D. Cloud Run jobs with maximum concurrency set to one

**10.** A company operates a service with constant, highly predictable traffic 24/7 and wants the most predictable monthly bill possible. Which compute approach best fits this pricing need?

A. Cloud Run, because it only bills for what's used
B. Compute Engine or GKE, using dedicated VM capacity with predictable pricing
C. Cloud Run functions, because they scale to zero
D. Cloud Run with GPU support, for maximum performance

**11.** Your team wants to serve a large language model for inference. Traffic is unpredictable, and you want the platform to scale down to zero — and stop paying for GPU time — when there are no requests, without managing any servers or reserving GPU capacity in advance. What should you use?

A. Cloud Run with GPU support
B. Compute Engine VM with a GPU attached directly
C. GKE Standard with a GPU-enabled node pool
D. GKE Autopilot with a fixed-size GPU node pool

**12.** You're migrating a containerized application that communicates over a custom TCP protocol (not HTTP or gRPC) and needs to run consistently across both an on-premises data center and Google Cloud. Which platform fits both requirements?

A. Cloud Run, because it can run any container
B. App Engine flexible environment
C. Cloud Run functions with a custom trigger
D. Google Kubernetes Engine (GKE)

**13.** A team is starting a brand-new serverless microservice from scratch and is deciding between App Engine and Cloud Run. There's no existing App Engine investment to consider. Which should they choose, and why?

A. App Engine, because it has been around longer and is more mature
B. Either is equally recommended by Google for new projects
C. Cloud Run, because it's the recommended platform for new serverless services, offering more flexibility and faster scaling
D. App Engine flexible environment, because it supports custom containers

**14.** A startup is hesitant to start on Cloud Run, worried that if their requirements grow later, they'll be locked in and forced to rewrite the application to move to a platform with more infrastructure control. Is this concern justified?

A. Yes, moving off Cloud Run always requires a full rewrite
B. No, as long as the application is built using Cloud Client Libraries and containerized, it can generally move between platforms without a rewrite
C. Yes, but only if the application uses more than one Google Cloud service
D. No, but only App Engine applications are portable, not Cloud Run applications

**15.** An engineering organization consists entirely of application developers with no dedicated operations or platform team. They need to deploy several stateless containerized services and want to minimize the amount of infrastructure they have to think about. Which platform best matches their team structure?

A. Cloud Run, since it requires no infrastructure or node management
B. GKE Standard, for maximum configuration flexibility
C. Compute Engine, for full control over the environment
D. GKE Autopilot, since it still requires managing Kubernetes resources and YAML manifests

---

## Answer Key & Explanations

**1. Correct answer: C** — Compute Engine.
Lift-and-shift migrations with hardware/license-bound software are the textbook Compute Engine signal — you can replicate the existing VM and OS configuration with zero code changes. Cloud Run and GKE Autopilot are tempting because they minimize operational effort, but neither lets you run software with hardware-specific licensing without containerizing and re-architecting first, which the scenario explicitly rules out.

**2. Correct answer: B** — GKE Autopilot.
"Don't want to manage nodes at all" is the direct signal for Autopilot — in GKE Standard, Google manages only the control plane, and node pool provisioning, patching, and lifecycle remain your responsibility by default. Option D is the classic trap: enabling node auto-provisioning still keeps you on Standard's shared-responsibility model, not Autopilot's fully managed one.

**3. Correct answer: A** — StatefulSet.
Stable network identity plus per-replica persistent storage is exactly what StatefulSet provides; Deployment is designed for interchangeable, stateless replicas where any pod can serve any request. Deployment is the tempting wrong answer because it's the more common, default workload type, but the scenario's stateful requirements rule it out explicitly.

**4. Correct answer: D** — Cloud Run job.
"Runs, does its work, exits" with independently retryable parallel tasks is the definition of a Cloud Run job — unlike a Cloud Run service, it never listens on a port. Option A is the tempting distractor because Cloud Run services can technically be invoked on a schedule too, but a service is built around listening for HTTP requests, not running a batch task to completion.

**5. Correct answer: C** — Cloud Run service.
Continuously listening for HTTP requests with automatic scale-up and scale-to-zero is exactly what a Cloud Run service does. A Cloud Run job is the tempting-but-wrong pick here because it's the "other" Cloud Run mode, but jobs never listen for HTTP traffic — they run to completion and exit.

**6. Correct answer: B** — Container-based deployment.
Full control over the base image and every added file is only guaranteed when you build and supply the container image yourself; buildpacks (source-based deploy) intentionally hide those details behind an automated, secure default build. Source-based deploy is the tempting distractor because it's also a valid Cloud Run path, but it trades control for convenience — the opposite of what this scenario asks for.

**7. Correct answer: A** — Source-based deployment (buildpacks).
Deploying straight from source code with no Dockerfile is exactly what buildpacks-based source deployment is for — Cloud Build and buildpacks detect the language (Python is supported) and produce a production-ready image automatically. Option B is the tempting-but-wrong choice because it's also a valid Cloud Run deploy path, but it requires the developer to build and manage the image themselves, which the scenario explicitly wants to avoid.

**8. Correct answer: D** — No, they're deployed as Cloud Run services.
Functions are a deployment option built on top of Cloud Run — they're deployed and run as Cloud Run services, not a separate infrastructure product. This is a common point of confusion because functions feel conceptually different (single-purpose, event-triggered) from a typical long-running service, but the underlying runtime is the same.

**9. Correct answer: C** — Preemptible (Spot) VMs.
A large, interruption-tolerant batch job is the exact use case preemptible VMs were built for — at least 60% cheaper than standard VMs, in exchange for Google being able to reclaim the capacity at any time. Committed use discounts help with steady, predictable workloads you plan to run long-term, not one-off batch jobs, which is why they're the tempting-but-wrong answer for this specific scenario.

**10. Correct answer: B** — Compute Engine or GKE with dedicated VM capacity.
Steady, predictable, round-the-clock traffic is best matched with dedicated VM capacity (Compute Engine/GKE), which gives consistent, forecastable billing. Cloud Run's pay-per-use model is the tempting-but-wrong pick because it sounds cost-efficient in general, but for constant load it loses the idle-time savings that make pay-per-use attractive, while offering less pricing predictability than dedicated capacity.

**11. Correct answer: A** — Cloud Run with GPU support.
Fully managed, reservation-free, scale-to-zero GPU access is a capability specific to Cloud Run — it's the only option here where you pay nothing when idle and manage no infrastructure at all. GKE with a GPU node pool is the tempting distractor since GKE does support GPUs, but the node pool still represents provisioned capacity that isn't inherently scale-to-zero the way Cloud Run's request-driven model is.

**12. Correct answer: D** — Google Kubernetes Engine (GKE).
Cloud Run is built around HTTP/HTTPS and gRPC traffic and does not support arbitrary TCP protocols, so it's disqualified the moment "custom TCP protocol" appears — GKE (or Compute Engine) is required. GKE is the better fit than Compute Engine here specifically because the scenario also needs consistent container orchestration across hybrid on-premises and cloud environments, which is a core GKE use case.

**13. Correct answer: C** — Cloud Run.
For new services, Cloud Run is the explicitly recommended path — it scales up and down faster than App Engine and offers deeper integration with the rest of Google Cloud and third-party services. "App Engine still exists and is supported" is the classic trap: continued support for existing App Engine apps does not mean it's the recommended starting point for something new.

**14. Correct answer: B** — No, the decision is reversible.
Applications written against Cloud Client Libraries and packaged as containers generally port across Compute Engine, GKE, and Cloud Run without a rewrite — the platform choice is a reversible decision, not a permanent commitment. This is why the recommended default is to start simple and serverless, then move to a platform with more control only if a real need arises.

**15. Correct answer: A** — Cloud Run.
A developer-only organization with no ops function maps directly to Cloud Run, which needs nothing beyond deploying the application — no nodes, clusters, or YAML manifests to manage. GKE Autopilot is the tempting distractor since it also reduces infrastructure burden significantly, but it still requires understanding Kubernetes concepts (pods, deployments, manifests) that a pure-developer team without any platform expertise would rather avoid.
