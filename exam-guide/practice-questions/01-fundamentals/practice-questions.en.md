# Module 1 — Fundamentals: Practice Questions

Scenario-based practice questions for the "Google Cloud Fundamentals: Core Infrastructure" module. These are drawn from the [deep dive](../../../deep-dive/01-fundamentals/fundamentals.md), weighted toward its exam-trap callouts and decision tables — the distinctions people actually miss on the real exam.

Answer all 15 questions first, then check the [Answer Key & Explanations](#answer-key--explanations) section below.

---

## Questions

**1.** An internal platform team builds a self-service portal: employees provision VMs instantly through a web UI (no ticket, no approval), reach the platform from anywhere over VPN, draw from a shared pool of servers, and can scale a workload up or down within minutes. Every team, however, pays a fixed quarterly fee regardless of how much they actually consume. Strictly applying the NIST definition, which essential cloud characteristic is missing?

A) Measured service (pay-as-you-go) — cost doesn't track actual usage
B) Broad network access — the platform requires a VPN
C) Resource pooling — the servers aren't described as multi-tenant
D) Elasticity — nothing confirms the platform can scale back down

**2.** A team is choosing between Compute Engine and App Engine for a new internal tool. One engineer claims: "It doesn't matter which we pick — either way we only pay for the compute we actually consume while our code runs." Is this correct?

A) Correct — IaaS and PaaS bill identically
B) Incorrect — App Engine bills for allocated capacity; Compute Engine bills only for actual usage
C) Incorrect — both only bill when a developer manually triggers a build
D) Incorrect — Compute Engine (IaaS) bills for the capacity you allocate, whether or not it's used; App Engine (PaaS) bills for resources actually consumed

**3.** A startup rebrands and wants its Google Cloud project to reflect the new brand in the console, while every CI/CD script, billing export, and IAM binding that references the project must keep working unchanged. What should they do?

A) Change the project ID to the new brand name — IDs are just labels and won't break anything
B) Change only the project name; the project ID is immutable once set, so scripts referencing it are unaffected
C) Change both the project ID and the project number; only the name is fixed
D) Change the project number to reflect the new brand

**4.** A batch pipeline calling the GKE API repeatedly gets throttled during bursts but recovers automatically a short time later. Separately, the same team tries to create a 21st VPC network in one project and is denied outright, no matter how long they wait. What quota type is each situation hitting?

A) Both are allocation quotas
B) Both are rate quotas
C) The API throttling is a rate quota (resets after a time window); the VPC network cap is an allocation quota (limits how many you can hold)
D) The API throttling is an allocation quota; the VPC network cap is a rate quota

**5.** An organization admin sets an IAM deny policy at the organization node, denying user X the permission to delete Cloud Storage buckets. Separately, a project owner — unaware of the deny policy — grants user X the Storage Admin role directly on one specific bucket, which includes delete permission. Can user X delete that bucket?

A) No — IAM always evaluates deny policies before allow policies, so the org-level deny wins regardless of where the allow was granted
B) Yes — the more specific, bucket-level allow binding overrides the broader organization-level deny
C) It depends on which policy was created first
D) Yes — deny policies only restrict predefined roles, not permissions granted directly

**6.** A security team wants to let a contractor only start and stop Compute Engine VMs across three projects grouped under one folder — nothing else. They're weighing: (a) a custom role defined at the folder, (b) the predefined Compute Instance Admin role applied at the folder, or (c) the basic Editor role at each project. Which approach actually works?

A) A custom role at the folder level, since it lets you define the exact two permissions needed
B) Basic Editor at each project, since basic roles are simplest for narrow scenarios
C) A custom role is always correct for least privilege, regardless of hierarchy level
D) The predefined role applied at the folder level — custom roles cannot be applied at the folder level, only at project or organization

**7.** A team wants to let Alice control who can impersonate the service account used by their CI pipeline, while Bob should only be able to view which VMs use that service account — without creating extra service accounts. Is this possible?

A) No — service accounts are pure identities and can't have IAM policies attached to them
B) Yes — a service account is also a resource, so Alice can get an editor-level role and Bob a viewer-level role directly on the service account itself
C) Only by creating two separate service accounts, one for Alice and one for Bob
D) No — only Organization Administrators can manage access to a service account

**8.** A company has one VPC network, `vpc1`, with a single subnet defined in the `asia-east1` region. They deploy VMs into two different zones within `asia-east1`, both attached to that same subnet. A new engineer worries this is misconfigured because "VMs in different zones can't share a subnet." Are they right?

A) Yes — subnets are zonal, so each zone needs its own subnet
B) No — VPC networks are zonal, but subnets are global
C) No — a subnet is regional and can span every zone within that region, so VMs in different zones of `asia-east1` can share it
D) Yes, unless Shared VPC is specifically enabled

**9.** An e-commerce app needs to route `/api` requests to one backend service and `/images` requests to a different backend, all over HTTPS with SSL/TLS termination at the edge. Which load balancer type fits?

A) Application Load Balancer, since content-based routing and SSL/TLS termination happen at Layer 7
B) Passthrough Network Load Balancer, since it preserves the source IP
C) Proxy Network Load Balancer, since it operates at Layer 4
D) Either works identically, since both operate at the same OSI layer

**10.** A gaming company runs a UDP-based multiplayer backend that requires direct server return and must see each client's real source IP address for anti-cheat geolocation. Which load balancer type should they use?

A) Application Load Balancer, since it can handle any protocol
B) Proxy Network Load Balancer, since it offers advanced traffic management
C) Cloud CDN, since it caches traffic at the network edge
D) Passthrough Network Load Balancer, since it doesn't terminate the connection and preserves the original client IP

**11.** A data science team runs a batch analytics job several times a week. Each run takes about four hours, doesn't need to start at a guaranteed time, and can resume from its last checkpoint if interrupted. Which Compute Engine pricing option minimizes cost here?

A) A committed-use discount, locking in a 1-year vCPU/memory commitment
B) A Spot VM, accepting preemption risk in exchange for up to 90% off the standard price
C) The sustained-use discount, since it applies automatically once usage crosses 25% of the month
D) A custom machine type, since tuning vCPU/memory always yields the largest discount

**12.** A finance team stores two kinds of objects in Cloud Storage: (1) monthly database backups they might need to restore roughly once a month, and (2) seven-year regulatory archives they expect to access less than once a year, if ever. Which storage classes fit each, respectively?

A) Standard for both, since Standard is safest for compliance-sensitive data
B) Coldline for the backups, Nearline for the regulatory records
C) Nearline for the backups, Archive for the regulatory records
D) Archive for the backups, Standard for the regulatory records

**13.** An analytics team ingests IoT sensor data at roughly 2 TB/day. They need sub-10ms lookups keyed by device ID and timestamp, but they don't need SQL joins or multi-row transactions. They're deciding between Spanner and Bigtable. What's the right choice, and what's the catch?

A) Bigtable, because it excels at massive, semi-structured, time-series-style data — but it does not support SQL joins or multi-row transactions
B) Spanner, because it offers strong consistency
C) Cloud SQL, because IoT telemetry is inherently relational
D) Firestore, because it supports offline sync for IoT devices

**14.** A small team wants to run containerized workloads on GKE. They have no dedicated infrastructure engineer and want Google to handle node provisioning, autoscaling, upgrades, and baseline security — they just want to declare what each workload needs. Which GKE mode fits, and what do they give up compared to the alternative?

A) GKE Standard, since it gives full node-level control
B) Neither — Cloud Run is the only serverless container option on Google Cloud
C) GKE Standard, because Autopilot isn't meant for production workloads
D) GKE Autopilot — Google manages node configuration, autoscaling, upgrades, and baseline security posture, at the cost of some fine-grained configuration control

**15.** An application has a long-running HTTP API that continuously serves user requests, plus a small piece of logic that should only run when a new file lands in a Cloud Storage bucket (generating a thumbnail). What's the appropriate service split?

A) Cloud Run for both — Cloud Run functions is deprecated
B) Cloud Run for the long-running HTTP API (a stateless container listening for requests); Cloud Run functions for the single-purpose thumbnail generator triggered by the Cloud Storage event
C) Cloud Run functions for both, since everything serverless should be a function
D) Cloud Run for the thumbnail generator since it's event-driven; Cloud Run functions for the API since it's serverless

---

## Answer Key & Explanations

**1. Answer: A.** The scenario explicitly states a fixed fee regardless of consumption, which directly contradicts the "pay only for what you use" characteristic. Option D is the tempting distractor — the text does say the workload "can scale up or down," so elasticity is actually satisfied; the trap is reading past the billing detail.

**2. Answer: D.** IaaS (Compute Engine) charges for the capacity you allocate whether it's idle or busy; PaaS (App Engine) charges for resources actually consumed. Option A is the tempting-but-wrong answer because it's easy to assume "cloud = pay only for use" applies uniformly — but that's specifically a PaaS/serverless property, not an IaaS one.

**3. Answer: B.** Project ID is immutable once the project is created; project name is the only one of the three identifiers that can be freely changed. Option A is the classic trap — it treats project ID as a cosmetic label rather than the permanent handle every script and integration depends on.

**4. Answer: C.** Rate quotas reset after a fixed time window (e.g., GKE's default of 3,000 calls per 100 seconds); allocation quotas cap how many of a resource you can hold at once (e.g., 15 VPC networks per project by default). Both quota types are enforced at the project level, which makes B and D tempting if you conflate "resets over time" with "hard cap."

**5. Answer: A.** IAM always checks deny policies before allow policies, and both inherit down the resource hierarchy — so an organization-level deny overrides a more specific, later-granted bucket-level allow. Option B is the tempting distractor because "more specific wins" is a common pattern in other permission systems, but it does not apply to IAM's deny-first evaluation.

**6. Answer: D.** Predefined roles can be scoped to a project, a folder, or an organization, so applying the predefined Compute Instance Admin role at the folder is exactly what's needed. Option A is the trap: a custom role sounds like the most precise tool, but custom roles can only be applied at the project or organization level — never the folder level.

**7. Answer: B.** A service account has a dual nature: it acts as an identity (for the CI pipeline) and is itself a resource that can carry its own IAM bindings — so Alice can get an editor-type role and Bob a viewer-type role directly on it. Option A is tempting because service accounts are commonly thought of purely as "things that authenticate," missing their resource side.

**8. Answer: C.** Subnets are regional and can span every zone within that region, so VMs in different zones of the same region attached to the same subnet are perfectly normal and are treated as being on the same network segment. Option A is the single most common misconception new Google Cloud users have about VPC networking.

**9. Answer: A.** Content-based routing (`/api` vs `/images`) and SSL/TLS termination are Layer 7 behaviors, which is exactly what the Application Load Balancer provides. Option C is tempting because "proxy" sounds advanced, but a Proxy Network Load Balancer operates at Layer 4 and doesn't do HTTP path-based routing.

**10. Answer: D.** Passthrough Network Load Balancer doesn't terminate or modify the connection, so it preserves the client's original source IP and supports direct server return — exactly what the anti-cheat and UDP requirements call for. Option B is the trap: Proxy Network Load Balancer also handles TCP/UDP-family traffic, but because it terminates the client connection and opens a new one to the backend, the original source IP is not preserved.

**11. Answer: B.** A batch job that tolerates interruption and doesn't need a guaranteed start time is the textbook Spot VM use case, offering up to 90% savings. Option A is tempting because committed-use discounts also save significant money, but they're meant for stable, predictable, long-running workloads — not an interruption-tolerant job with no fixed schedule.

**12. Answer: C.** Nearline targets data accessed about once a month or less (fits the backups), and Archive targets data accessed less than once a year with a 365-day minimum storage duration (fits the multi-year regulatory records). Option B is tempting because Coldline (~90-day access pattern) sits between the two, but it doesn't match either described access pattern as precisely as Nearline and Archive do.

**13. Answer: A.** Bigtable is built exactly for high-throughput, low-latency, key-based access on massive semi-structured datasets like time-series IoT data — but it's explicitly called out as not supporting SQL joins or multi-row transactions. Option B is tempting because Spanner also scales horizontally, but Spanner is the heavier, relational choice meant for when you need joins, secondary indexes, and strong global consistency — not a simple high-throughput key-value pattern.

**14. Answer: D.** GKE Autopilot manages node configuration, autoscaling, upgrades, and baseline security for you, which is exactly what a team without dedicated infrastructure staff needs — the tradeoff is reduced node-level configuration control compared to Standard. Option A inverts the tradeoff: Standard gives more control but requires the team to manage nodes themselves, which contradicts what the scenario asks for.

**15. Answer: B.** Cloud Run runs a full, stateless, long-running container that listens for requests (the HTTP API); Cloud Run functions is built for small, single-purpose logic triggered by an event like a new object landing in Cloud Storage (the thumbnail generator). Option D is the trap answer — it swaps the two services based on a surface-level reading of "event-driven" and "serverless" without matching each workload to what each service is actually designed for.
