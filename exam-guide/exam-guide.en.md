# Exam Guide — Professional Cloud Developer

This is a pre-exam cram reference, not a tutorial. It distills the exam traps, decision tables, and "key distinctions" callouts scattered across the eight [deep dives](../deep-dive) into scannable, scenario-based tables.

## How to use this guide

- **Deep dives** teach *why* a service works the way it does. Read them first, once, to build understanding.
- **Glossary** (`glossary/glossary.en.md`) gives you a one-line definition per term when you forget what something means.
- **This guide** assumes you already understand the material and just need to drill the decision: *given this scenario, which service, and why?*

Each module section below is a table: **if a question describes X, the answer is usually Y, because Z.** The final section collects services that get confused **across** modules — the pairs and groups that don't sit next to each other in the deep dives, so the contrast never gets drilled unless you do it deliberately.

Read the deep dive first. Use this guide the night before the exam.

---

## Module 1 — Fundamentals

| If the question says... | The answer is usually... | Because... |
| --- | --- | --- |
| "Does changing the project display name change how Google identifies the project?" | No — **Project ID** is immutable; **project name** is the one that can change | Project ID is the permanent handle Google and your tooling use; only the display name is editable |
| "...resets after a fixed time window" vs "...limits how many resources you can hold" | **Rate quota** (resets over time) vs **allocation quota** (caps a count) | Both are project-level, but one is about pace, the other about inventory |
| A policy is set on a folder — does it reach the projects inside? | Yes, policies **inherit downward** through the resource hierarchy | Org → folder → project → resource is a one-way inheritance chain |
| Two conflicting policies, one allow and one deny, on the same principal | **Deny always wins** — IAM checks deny policies before allow policies | Deny is a hard override regardless of any role granted |
| "Can I apply a custom role at the folder level?" | No — custom roles apply only at **project or organization** level | Folder-level custom roles are not supported; predefined/basic roles can still apply there |
| Choosing between basic, predefined, and custom roles | **Predefined** is the default choice for real scenarios; **custom** only when predefined is still too broad; **basic** rarely in production | Predefined roles are Google-maintained and service-specific; basic roles violate least privilege at scale |
| "Is a service account an identity, a resource, or both?" | **Both** — it authenticates as an identity, and you can also apply IAM roles to it as a resource | This dual nature lets you control who can *act as* a service account separately from what the service account itself can do |
| "Do VPC networks span regions?" | Yes — **VPC is global**; **subnets are regional** and can span the zones within that region | This is the single most common surprise for new users of Google Cloud networking |
| HTTP(S) traffic needing content-based routing vs raw TCP/UDP | **Application Load Balancer** vs **Network Load Balancer** | Application LB operates at L7 (HTTP/HTTPS); Network LB operates at L4 (TCP/UDP/other IP protocols) |
| "Does the load balancer terminate the connection and does it preserve the source IP?" | **Proxy** Network LB terminates the connection; **Passthrough** Network LB does not, and preserves the client's source IP | Passthrough is for workloads needing direct server return or the original client IP |
| Need guaranteed uptime SLA for hybrid connectivity | **Dedicated Interconnect / Partner Interconnect** (up to 99.99% SLA), not Direct/Carrier Peering | Peering is not covered by a Google SLA; Interconnect is |
| Data accessed constantly vs about once a month vs about once a quarter vs almost never (compliance/archive) | **Standard → Nearline → Coldline → Archive** | Cloud Storage classes are ordered purely by access frequency and cost tradeoff; Archive has a 365-day minimum storage duration |
| "I want the highest level of abstraction that still meets my requirements" | Move up the ladder: **IaaS (Compute Engine) → PaaS (App Engine) → Serverless (Cloud Run) → SaaS** | Less abstraction = more control but more operational burden; the exam favors "highest abstraction that satisfies requirements" |
| "I don't want to manage GKE nodes at all" | **GKE Autopilot**, not Standard | Autopilot manages control plane *and* nodes; Standard only manages the control plane |
| Full long-running container/service vs a single-purpose function triggered by an event | **Cloud Run** vs **Cloud Run functions** | Functions are event-driven and single-purpose; Cloud Run runs a full stateless container listening for requests — both are billed to the nearest 100ms |
| A prompt with no examples vs one example vs two-or-more examples vs an assigned persona | **Zero-shot / one-shot / few-shot / role prompting** | These are the four prompt types; more examples generally improve accuracy on non-trivial tasks |

---

## Module 2 — Getting Started with Google Cloud Development

| If the question says... | The answer is usually... | Because... |
| --- | --- | --- |
| "Where should database credentials or API endpoints live?" | **Environment variables**, never hardcoded constants in source | Lets the same tested code run unchanged across dev/test/prod — the twelve-factor app principle |
| "Should I refactor this monolith into microservices?" | Evaluate **cost vs. benefit** — not "microservices are always better" | Microservices buy independent scaling/deployment at the cost of operational complexity; the exam rejects "modern = correct" reasoning |
| Loosely coupling services with an event trigger vs a message queue | **Eventarc** (event queue) vs **Pub/Sub** (message queue) | Both decouple producers from consumers and buffer traffic spikes; pick based on whether you're reacting to an event or passing a message |
| An HTTP API consumer should only read the fields it needs from a payload | Bind loosely to **specific fields**, not the whole payload | Lets the publisher add new fields without breaking existing consumers (backward compatibility) |
| Component design that scales horizontally without a shared bottleneck | **Stateless** components + a separate database for state (e.g. Firestore) | Shared state is the classic scalability bottleneck; stateless workers can be added/removed freely |
| A request fails due to a flaky network blip vs a backend that's been down for minutes | **Retry with exponential backoff** (transient) vs **circuit breaker** (persistent) | Retrying a persistently broken backend wastes resources and worsens the outage; Cloud Client Libraries retry transient errors automatically |
| Caching application data (session, computed values) vs caching web/static content | **Memorystore** (Redis/Memcached, in-memory) vs **Cloud CDN** (Google's edge network) | Different layers: app-data cache vs edge content cache |
| "I need rate limiting, quotas, and a facade in front of legacy backend APIs" | **Apigee** | It's Google Cloud's API gateway/management platform |
| "I don't want to write my own login system" | **Identity Platform** / **Firebase Authentication** | Delegating identity avoids the security risk of building password storage, MFA, and session management yourself |
| "How should a serverless app write logs?" | Write to **stdout**, let the infrastructure collect it — never manage log files | Serverless platforms have no persistent disk to manage; stdout is the natural event-stream approach |
| Commit to a feature branch triggers a build+test, but doesn't reach production automatically | **Continuous Integration** | CI stops at "build compiles and tests pass," it does not deploy |
| Push to main triggers staging tests and a release-candidate build, but a human must approve before production | **Continuous Delivery** | Delivery automates everything up to "ready to deploy" — the go/no-go call stays human |
| Push to main and, if tests pass, the change reaches production with zero human involvement | **Continuous Deployment** | The only gate left is the test suite itself |
| Roll out a new version to a small percentage of users first, then increase | **Canary release** | Limits blast radius of a bad release to a small user slice |
| Two identical environments, instant traffic cutover, instant rollback | **Blue/green release** | Atomic switch between two live environments, not a gradual percentage ramp |
| Gradually replacing pieces of a legacy monolith during a migration | **Strangler pattern** | A facade routes requests to old or new components as they're replaced piece by piece, avoiding a risky "big bang" rewrite |
| Managing Cloud Storage buckets/objects from the CLI | **`gcloud storage`**, not `gsutil` | `gcloud storage` is the modern, faster, preferred tool; `gsutil` still works but is legacy |
| Calling Google Cloud APIs from application code | **Cloud Client Libraries**, not raw API calls | They add automatic authentication, retry with backoff, and idiomatic language conventions |
| A quick, single-user, throwaway command-line environment vs a persistent, standardized, secure team dev environment | **Cloud Shell** vs **Cloud Workstations** | Cloud Shell is ephemeral (terminates after an hour idle, 5 GB persistent home); Cloud Workstations is a managed, reproducible environment for teams, running in ephemeral Compute Engine VMs inside your VPC |
| Developing against Firestore/Pub/Sub/Spanner/Bigtable/Datastore without touching the real service or paying for it | **Local emulators** | Code doesn't change — only an environment variable switches between emulator and real service |

---

## Module 3 — Storage

| If the question says... | The answer is usually... | Because... |
| --- | --- | --- |
| Static website, large binary files, user-uploaded photos/videos, backups | **Cloud Storage** | Unstructured object storage; key = object name, content is opaque bytes, up to 5 TB/object |
| Mobile/web app needing flexible documents, real-time sync, and offline support | **Firestore** | Document/collection model with strong consistency and built-in offline caching + sync |
| Billions of rows, sub-10ms key-value lookups, high-throughput single-keyed data (IoT, clickstream, time series) | **Bigtable** | Sparse wide-column NoSQL, optimized purely for fast key-based access at massive scale |
| "Bigtable" and "BigQuery" appear in the same question | They are **opposites** despite the similar name — Bigtable is operational NoSQL, BigQuery is an analytics warehouse | This name collision is the single most common trap in the whole storage module |
| Classic relational web app, OLTP, MySQL/PostgreSQL/SQL Server migration with minimal refactor | **Cloud SQL** | General-purpose managed relational DB; Google handles replication, failover, backups |
| PostgreSQL-compatible app needing much higher performance, or a mix of transactional + analytical (HTAP) workloads | **AlloyDB** | PostgreSQL-only, but with compute/storage separation giving 4x transactional and up to 100x analytical performance via the Columnar Engine |
| Mission-critical, globally distributed, strongly consistent relational data needing the highest SLA | **Spanner** | The only service offering horizontal scalability *and* strong consistency *and* 99.999% SLA together |
| "Analytics", "data warehouse", "BI reporting", "scan petabytes with SQL" | **BigQuery** | Serverless OLAP warehouse; not built for millisecond single-row transactions |
| Need a fast in-memory cache backed by Redis or Memcached | **Memorystore** | Fully managed, protocol-compatible with both engines; not meant as a system of record |
| "Does the read see the update immediately, or might it lag?" | **Strong consistency** (Spanner, Firestore's storage layer) vs **eventual consistency** | Strong consistency guarantees the latest write is visible immediately everywhere |
| Need secure DB access without managing IP allowlists or SSL certs | **Cloud SQL Auth Proxy** / **AlloyDB Auth Proxy** | A local proxy client opens a secure tunnel to the server-side counterpart |
| "Which single database should I use for my whole app?" | Trick question — **no one-size-fits-all**; use the service that fits each workload | You are never limited to one database, and size limits are per-database, not global |

---

## Module 4 — Authentication and Authorization

| If the question says... | The answer is usually... | Because... |
| --- | --- | --- |
| "Can this principal type authenticate an API request?" for a Google group / Workspace account / Cloud Identity domain | **No** — none of the three can create identity, they only simplify bulk permission management | Only a **Google Account** (person) or **service account** (app/workload) can actually authenticate a request |
| Google Workspace account vs Cloud Identity domain | Workspace account has access to Workspace apps (Gmail, Docs, Drive); Cloud Identity domain does **not** | Cloud Identity is for orgs that want centralized identity without buying Workspace |
| A permission string like `pubsub.subscriptions.consume` | Format is always **`service.resource.verb`**, and it's granted only via a **role**, never assigned directly | Roles bundle permissions into a manageable unit |
| Low-security, read-only, no real identity needed | **API key** — but note most Google APIs don't even accept them | A leaked API key gives long-lived, unrestricted access to the associated project |
| Acting on behalf of a real person, needing consent | **User account / OAuth 2.0** token | Time-limited and scoped to what the user actually authorized |
| An app-to-app, server-side, unattended call to a Google API | **Service account** | Its OAuth token's access is scoped to the roles attached to the service account |
| Downloaded service account key leaked into a public repo, or used to escalate privilege, or used to hide the real actor in logs | The three named risks: **credential leakage**, **privilege escalation** (revoking the key does *not* undo privileges already granted), **identity masking** | This is why downloaded SA keys are the last-resort authentication method |
| Local development, testing code against Google Cloud | `gcloud auth application-default login` | Feeds Application Default Credentials (ADC), not the CLI's own session |
| Running `gcloud compute instances list` from a terminal | `gcloud auth login` | This authenticates the CLI itself, not your application code |
| Production code running on Compute Engine or Cloud Run (not GKE) | **Attached service account** | Preferred production pattern — no key file to manage; Google handles the credential lifecycle |
| Production code running inside GKE | **Workload Identity** | Lets a Kubernetes service account impersonate an IAM service account — fine-grained per-workload identity |
| Production code running outside Google Cloud (another cloud, on-premises), with an OIDC-capable identity provider | **Workload Identity Federation** | Exchanges an external OIDC token for a short-lived Google Cloud access token — no service account key needed |
| No federation possible at all | **Service account key**, as an explicit last resort, with the "upload your own public key" and least-privilege mitigations | Every other option in the decision tree is preferred over this one |
| "I want to control access to my HTTPS app without writing any authorization code, and without a VPN" | **Identity-Aware Proxy (IAP)** | Application-level access control based on identity, not network location |
| Fast, drop-in login for a mobile/web app: password, phone, Google/Apple/GitHub sign-in | **Firebase Authentication** | Backend + SDKs + prebuilt UI, targeted at app developers |
| Need SAML/OIDC federation, MFA, or IAP integration for enterprise customers | **Identity Platform** | Same foundation as Firebase Auth, plus enterprise-grade features |
| Storing an API key, password, or certificate securely | **Secret Manager**, not an environment variable | Adds versioning (immutable, deletable), IAM-based least-privilege access, audit logging, and optional Cloud KMS encryption — none of which env vars provide |

---

## Module 5 — Adding Intelligence to Your Application

| If the question says... | The answer is usually... | Because... |
| --- | --- | --- |
| Label objects, read text (OCR), detect faces/logos/landmarks, or flag explicit content in an image | **Vision AI** | Pre-trained image understanding, no ML expertise required |
| Convert speech to text or text to speech, in one of 110+ languages | **Speech-to-Text / Text-to-Speech** | A matched pair for building voice interfaces |
| Translate arbitrary text dynamically, on demand | **Translation AI** | Fast, responsive, no pre-baked translation files needed |
| Extract sentiment, entities, or intent from customer text/social posts | **Natural Language AI** | Turns raw text into structured meaning |
| Find when/where an entity appears across a video's shots, frames, or full length | **Video AI** | Vision AI's counterpart with a time dimension |
| Turn scanned invoices, contracts, or forms into structured, queryable fields | **Document AI** | Converts unstructured documents into structured data |
| Train a model on your own data without writing ML code | **Agent Platform AutoML** | No-code training on images, tabular data, or video |
| Standard pre-trained APIs don't cover a highly specific problem | **TensorFlow / PyTorch** custom model | Full control, but requires real ML expertise |
| "Answer a narrow yes/no question about content" vs "generate brand-new content" | **Pre-trained narrow API** vs **Generative AI** | Narrow ML answers a specific classification; generative AI produces new, open-ended output |
| "What makes a model a foundation model, and what makes it an LLM specifically?" | **Foundation model** = general term for models trained on massive multimodal data; **LLM** = the most popular subtype, trained on text only | All LLMs are foundation models; not all foundation models are LLMs |
| A model trained broadly, then adapted with a small domain-specific dataset | **Pre-training** (broad) followed by **fine-tuning** (specific) | Pre-training builds general capability; fine-tuning specializes it |
| A model confidently produces a wrong or nonsensical answer | **Hallucination**, caused by insufficient training data, noisy data, insufficient context, or insufficient constraints in the prompt | Good prompt engineering (context + examples + persona + concise instructions) reduces but doesn't eliminate this |
| Using an AI assistant to generate, explain, fix, complete, document, or translate code | **Gemini** code assistance | Covers the full developer workflow, not just autocomplete |

---

## Module 6 — Deploying Applications

| If the question says... | The answer is usually... | Because... |
| --- | --- | --- |
| Commit to a feature branch auto-triggers build + unit tests | **Continuous Integration** | Stops at "the code compiles and passes tests" |
| Push to main triggers staging tests and a release candidate, but production deployment needs **manual approval** | **Continuous Delivery** | The artifact is production-ready; a human decides when it actually ships |
| Push to main and — if tests pass — production deployment happens with **no human step** | **Continuous Deployment** | Delivery and Deployment are nearly identical; the only difference is whether a human approves the final step |
| Roll out to a small traffic percentage first, then increase gradually | **Canary release** | Limits exposure to a subset of users |
| Two identical environments, instant traffic switch and instant rollback | **Blue/green release** | Atomic cutover rather than gradual percentage ramp |
| "Are these open-source Java/Python packages verified and continuously scanned by Google?" | **Assured OSS** | Only covers Java and Python |
| "Can I prove this container image was built from a trusted source through a trusted process?" | **Cloud Build's verifiable build metadata** | A provenance record, not a scan |
| "Scan my stored images for known vulnerabilities, and keep re-checking them for new CVEs" | **Artifact Analysis** | It observes and reports — it does **not** block anything |
| "Block any image that hasn't passed my required checks from ever running" | **Binary Authorization** | Enforces policy via attestations — the enforcement layer that Artifact Analysis lacks |
| Sequential rollout across environments with one-click approval and rollback | **Cloud Deploy** | The delivery/orchestration piece of the pipeline |
| "Is a container just a lightweight VM?" | No — a VM virtualizes **hardware** (each has its own OS copy); a container virtualizes the **OS** (process isolation + namespaces) | This is why containers start in a fraction of a second and VMs take a minute or more |
| A build pipeline step running in its own isolated tool environment | Every Cloud Build **step is a Docker container** (`name` = which container, `images` = the image to produce) | Keeps each step's tools from contaminating the others |
| Passing files/output from one build step to the next | **`/workspace`** directory | Mounted into every step's container; persists across the whole pipeline |
| Build should start only on commits to a specific branch or tag | **Trigger type** — branch-based or tag-based | Determines what commit condition kicks off Cloud Build |

---

## Module 7 — Compute Options for Your Application

| If the question says... | The answer is usually... | Because... |
| --- | --- | --- |
| Lift-and-shift migration, custom OS, licensed software tied to specific hardware, or a non-HTTP TCP protocol | **Compute Engine** | Maximum control, but you own OS patching, scaling config, and health checks yourself |
| Large, interruption-tolerant batch job, cost is the priority | **Preemptible VM** (Compute Engine) | At least 60% discount; Google can reclaim the capacity at any time |
| Containerized workload needing hybrid/multi-cloud portability, stateful components, or non-HTTP protocols | **GKE** | Orchestrates containers across on-prem and other clouds; supports StatefulSets and arbitrary TCP |
| "I don't want to manage nodes at all, just my workloads" | **GKE Autopilot** | Google manages control plane *and* nodes; also enforces hardening best practices automatically |
| "I need fine-grained control over node type, networking, or GPU/TPU node pools" | **GKE Standard** | Google manages only the control plane; you manage node pools |
| A workload with no identity/storage requirements vs one needing stable network identity and persistent storage | **Deployment** (stateless) vs **StatefulSet** (stateful) | Any replica can serve a stateless request; a database-like workload needs a stable identity |
| Stateless container, want zero infrastructure management | **Cloud Run** | Fully managed, scales to zero, billed to the nearest 100ms |
| "I just want my source code to become an HTTPS endpoint, I don't want to think about the Dockerfile" | **Cloud Run source-based deploy** (buildpacks + Cloud Build) | Auto-detects the language and builds a secure, consistent image for you |
| "I need full control over exactly how the image is built" | **Cloud Run container-based deploy** | You supply the image directly |
| Single-purpose code triggered by an event (Pub/Sub, Eventarc, HTTP) | **Cloud Run functions** | Deployed *as* a Cloud Run service under the hood — not a separate product |
| A task that runs once or on a schedule, does not listen on a port, and exits when done | **Cloud Run jobs** | Distinct from Cloud Run services, which listen continuously for requests |
| AI inference (LLM), video transcoding, or 3D rendering, but you still want serverless economics | **Cloud Run with GPU** | Fully managed GPU, on-demand, no reservation, scales to zero |
| Need a GPU/TPU bound to one specific machine | **Compute Engine** | Direct hardware attachment |
| Need a GPU/TPU shared across an orchestrated container fleet | **GKE** (via node pools) | The GPU is attached to a node pool, not a single VM |
| Predictable, steady, 24/7 traffic | **Compute Engine / GKE** dedicated pricing | More predictable billing for consistent capacity needs |
| Bursty, unpredictable traffic, want to avoid paying for idle capacity | **Cloud Run** pay-per-use | You are never billed for idle time |
| "Should I build a brand-new serverless service on App Engine?" | No — use **Cloud Run** | Cloud Run is the modern successor; App Engine is not recommended for new projects |
| "If I pick the wrong compute platform, am I stuck?" | No — apps written against **Cloud Client Libraries** port across platforms with little rework | This makes "start serverless, move to more control later if needed" a safe default strategy |

---

## Module 8 — Monitoring and Performance Tuning

| If the question says... | The answer is usually... | Because... |
| --- | --- | --- |
| General health dashboard, trend over time, custom or out-of-the-box | **Cloud Monitoring** | Collects metrics, events, and metadata across Google Cloud, other clouds, and on-prem |
| Alert when a metric crosses a threshold | **Cloud Monitoring** alerting policy | Active triggering, not passive dashboard viewing |
| Every dashboard should track these four metrics at minimum | **Four Golden Signals**: latency, traffic, errors, saturation | The universal starting point for any service's health metrics |
| "Does my latency metric need to separate successful from failed requests?" | Yes — a fast HTTP 500 will artificially lower average latency and hide real health | This is the golden-signal trap the exam tests most often |
| "Is a 200 OK response always a success?" | No — wrong content in a 200, or an SLA/policy violation with no error code at all, both count as **errors** | Error tracking is broader than just HTTP 5xx |
| "Does saturation only matter at 100% utilization?" | No — systems commonly degrade **before** hitting 100% | Set utilization targets based on real observed degradation points, not a naive 100% assumption |
| Want notification the instant a specific log pattern appears | **Log-based alert** | Fires on a single matching event |
| Want to count occurrences over time and alert when a threshold is crossed | **Log-based metric** | Built for trend/volume tracking, not instant single-event notification |
| Prefer text logs or structured (JSON) logs for searchability and log-level filtering | **Structured logs** (`jsonPayload`, `severity`, `message`) | Text logs (`textPayload`) have no log level and are hard to query |
| Collecting logs/metrics from third-party software (NGINX, Tomcat) on a Compute Engine VM | **Ops Agent** (Fluent Bit for logs, OpenTelemetry Collector for metrics) | Purpose-built dual-component agent, zero-config for standard system metrics |
| "Will my GKE pod logs still be there after the pod is gone?" | Not unless sent to **Cloud Logging** — container logs vanish with the pod, cluster events expire after an hour | Kubernetes itself does not persist logs long-term |
| Want PromQL dashboards/alerts on Kubernetes without operating Prometheus yourself | **Google Cloud Managed Service for Prometheus**, with **managed collectors** (recommended for all Kubernetes, including GKE) | Keeps your existing PromQL knowledge while Google handles scaling and HA |
| "Which error is most frequent / newest, and how many users does it affect?" | **Error Reporting** | Groups and deduplicates errors by stack trace automatically |
| "Where, along the path of a single request, did the time go?" | **Cloud Trace** | Trace = the whole request's duration; span = one sub-operation inside it |
| "Cloud Run traces requests automatically, so I have full visibility into my service" | False — automatic tracing only covers **inbound/outbound HTTP**, not internal calls | You need instrumentation (OpenTelemetry + Cloud Trace Exporter) to see internal spans |
| "Which function or code path is consuming CPU/memory in production, without slowing the app down?" | **Cloud Profiler** | Statistical sampling, low overhead — safe to leave on continuously in production |
| Cloud Trace vs Cloud Profiler, when both mention "performance" | Trace answers **"which request step was slow"** (time axis); Profiler answers **"which code line is expensive"** (resource axis) | Different axes entirely, despite both being performance tools |

---

## Frequently Confused Services (Cross-Module)

These pairs and groups don't sit side by side in any single deep dive, so the contrast between them is easy to miss unless you drill it deliberately.

| Confused group | One-line disambiguator |
| --- | --- |
| **Compute Engine vs GKE vs Cloud Run vs Cloud Run functions vs App Engine** | Compute Engine = VMs, full control. GKE = managed container orchestration, medium control. Cloud Run = fully managed serverless containers, minimal control. Cloud Run functions = single-purpose event-triggered code, deployed *as* a Cloud Run service. App Engine = legacy serverless PaaS; not recommended for new projects, Cloud Run replaced it. |
| **Cloud Monitoring vs Cloud Logging vs Error Reporting vs Cloud Trace vs Cloud Profiler** | Monitoring = "is something wrong?" (metrics/dashboards/alerts). Logging = "what exactly happened?" (raw event detail). Error Reporting = "which error, how often?" (grouped/deduplicated crashes). Trace = "which request step was slow?" (time axis). Profiler = "which code line eats resources?" (code axis). |
| **Artifact Analysis vs Binary Authorization** | Artifact Analysis scans and reports vulnerabilities — observation only. Binary Authorization enforces a policy and blocks non-compliant images from running — enforcement. |
| **Bigtable vs BigQuery** | Bigtable = operational NoSQL key-value store, sub-10ms lookups, for live applications. BigQuery = serverless analytics data warehouse, OLAP over petabytes. Same name prefix, opposite jobs. |
| **Cloud SQL vs AlloyDB vs Spanner** | Cloud SQL = general managed relational DB (MySQL/PostgreSQL/SQL Server), single-region OLTP. AlloyDB = PostgreSQL-only, high performance, HTAP. Spanner = globally distributed, horizontally scalable, strongly consistent, 99.999% SLA. |
| **Firestore vs Bigtable** | Firestore = document model, mobile/web apps, real-time sync + offline support. Bigtable = sparse wide-column store, billions of rows, sub-10ms high-throughput operational/analytical workloads. Both "NoSQL," entirely different use cases. |
| **Workload Identity vs Workload Identity Federation** | Workload Identity is for workloads **inside GKE** (Kubernetes service account impersonates an IAM service account). Workload Identity Federation is for workloads **outside Google Cloud** (external OIDC token exchanged for a Google Cloud access token). Neither needs a downloaded service account key. |
| **Identity Platform vs Firebase Authentication** | Same underlying auth foundation. Firebase Authentication targets mobile/web app developers (password, phone, social sign-in, drop-in UI). Identity Platform adds enterprise features: SAML/OIDC federation, MFA, IAP integration. |
| **Memorystore vs Cloud CDN** | Memorystore caches **application data** in memory (Redis/Memcached) for your backend. Cloud CDN caches **web/static content** at Google's edge, closer to end users. Different layer, different purpose. |
| **Continuous Delivery vs Continuous Deployment** | Delivery automates everything up to a production-ready, approved artifact — a **human** decides when it ships. Deployment goes one step further and ships automatically once tests pass, with **no human** gate. |
| **GKE Standard vs GKE Autopilot** | Standard: Google manages the control plane, you manage node pools and their configuration. Autopilot: Google manages the control plane *and* the nodes — you only think about workloads. |
| **API key vs User account (OAuth) vs Service account** | API key identifies a project, not an identity, and grants long-lived access if leaked — used rarely, for low-security read-only APIs. User account (OAuth) represents a person with a scoped, time-limited token. Service account represents an app/workload with role-scoped access. |
| **IAM role types: Basic vs Predefined vs Custom** | Basic = broad, project-wide, rarely appropriate in production. Predefined = Google-maintained, service-specific — the default choice. Custom = fully self-defined, for when predefined is still too permissive and least privilege is required. |
| **Cloud Build vs Cloud Deploy** | Cloud Build compiles code, runs tests, and produces a container image pushed to Artifact Registry — the **integration** stage. Cloud Deploy takes that image through sequential target environments with approval/rollback controls — the **delivery/deployment** stage. |
| **Kubernetes "Deployment" vs Google Cloud "Cloud Deploy"** | A Kubernetes **Deployment** is an object that keeps a set of stateless pod replicas running. **Cloud Deploy** is an entirely separate Google Cloud CI/CD service that automates promoting a build through environments. Same word, unrelated concepts — a classic exam-wording trap. |
| **Prometheus (self-managed) vs Google Cloud Managed Service for Prometheus vs Cloud Monitoring** | Prometheus is the open-source toolkit itself (PromQL, time-series data) — you'd operate and scale it yourself. Managed Service for Prometheus is Google's fully managed version of that same toolkit — same PromQL, no operational burden. Cloud Monitoring is the broader Google Cloud observability product, which can itself act as a PromQL query backend. |

---

> **Before the exam:** Work through each module table once, out loud if possible — say the scenario, then the answer, then the reason, without looking. Then do the same for the confused-services table, since those are the pairs most likely to appear as distractors in the same question. If a row doesn't make sense anymore, that's your signal to go back to the specific deep dive and rebuild the "why."
