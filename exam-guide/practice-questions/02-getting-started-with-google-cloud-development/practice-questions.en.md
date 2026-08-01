# Practice Questions — Module 02: Getting Started with Google Cloud Development

Scenario-based practice questions for the **Developing Applications with Google Cloud: Foundations** module covered in [`deep-dive/02-getting-started-with-google-cloud-development`](../../../deep-dive/02-getting-started-with-google-cloud-development/getting-started-with-google-cloud-development.md).

These questions are weighted toward the exam traps and decision tables from that module — the distinctions people actually get wrong on the real Professional Cloud Developer exam. Try to answer every question before checking the [Answer Key & Explanations](#answer-key--explanations) section.

---

## Questions

**1.** Your team is deploying the same application build to development, staging, and production. QA keeps flagging that "the code that passed tests isn't the code running in prod" because a developer hardcoded the staging database URL as a constant before the last release, then changed it by hand for the production build. What should the team do to prevent this class of bug going forward?

A. Store the database URL as a constant in the source code, and add a comment documenting which environment it applies to
B. Store the database URL as an environment variable and inject the correct value per environment, keeping the code identical across environments
C. Keep two separate branches of the codebase — one for staging values, one for production values
D. Store the database URL in the version control repository as a separate config file per environment, committed alongside the code

**2.** A team is debating whether to break their five-year-old monolithic order-processing application into microservices. One engineer argues "microservices are the modern standard, so we should always move to them." A tech lead pushes back. What is the correct exam-aligned reasoning?

A. Microservices should always be preferred over monoliths because they scale independently
B. Monoliths should always be preferred because they are simpler to deploy
C. The decision should weigh the cost of refactoring (time, effort, operational complexity) against the benefit (independent scaling and deployment) — it is not an automatic choice
D. The decision should be based solely on which architecture the engineering team has more experience with

**3.** An order service and an inventory service need to be loosely coupled at runtime, so a spike in orders doesn't stall the order service while inventory catches up, and so either service can be updated independently. You want to implement this using Google Cloud primitives that fill the role of an "event queue" and a "message queue" respectively. Which pairing is correct?

A. A Pub/Sub topic as the event queue, and an Eventarc trigger as the message queue
B. An Eventarc trigger as the event queue, and a Pub/Sub topic as the message queue
C. Cloud Tasks as the event queue, and Cloud Scheduler as the message queue
D. Cloud CDN as the event queue, and Memorystore as the message queue

**4.** A customer service's API returns a payload with `name`, `age`, and `email` fields. An email-sending service consumes this payload but only needs `name` and `email`. Six months later, the customer team wants to add a `phone_number` field to the payload to support a new feature. Which design choice in the email service today avoids breaking it when that field is added?

A. The email service deserializes the payload into a strict schema that requires every field present in the payload today, and rejects unknown fields
B. The email service reads only the `name` and `email` fields it needs and ignores any other fields in the payload
C. The customer service should never change its payload once the email service starts consuming it
D. The email service should call the customer service twice — once for name/email, once to check the total field count matches

**5.** A Cloud Run service processes checkout requests under highly variable traffic. During code review, someone suggests caching the user's shopping cart in the service instance's local memory between requests to speed things up. Why does this go against the best practices in this module, and what should be done instead?

A. It's fine — Cloud Run instances never scale down, so in-memory state is always safe
B. In-memory state ties a user to a specific instance, making it hard to scale out or safely terminate instances; cart state should be persisted in a separate database like Firestore instead
C. Local memory is acceptable as long as the instance uses a larger machine type
D. Cart state should be written to the container's local disk instead of memory

**6.** Your application calls a downstream payment API. Two different failure patterns show up in production: (1) occasional network blips where the call fails but the API is healthy again within a second or two, and (2) an extended outage where the payment API is completely down for 20 minutes. What is the correct resilience strategy for each case, respectively?

A. Case 1: open a circuit breaker and stop calling. Case 2: retry with exponential backoff until it succeeds
B. Case 1: retry with exponential backoff. Case 2: open a circuit breaker and stop sending traffic until the service recovers
C. Both cases: retry immediately and repeatedly with no backoff, since retries are cheap
D. Both cases: open a circuit breaker immediately and never retry

**7.** Your application has two distinct caching needs: (1) personalized pricing data that's expensive to recompute and read by backend logic on every request, and (2) static JavaScript, CSS, and image assets that need to load fast for users around the world. Which Google Cloud services correctly match these two needs?

A. Cloud CDN for the personalized pricing data, Memorystore for the static assets
B. Memorystore for the personalized pricing data, Cloud CDN for the static assets
C. Cloud Storage for both needs, with no dedicated caching layer
D. Firestore for both needs, since it has built-in in-memory caching

**8.** A company has a 15-year-old legacy claims-processing system that cannot be rewritten or moved to the cloud in the near term. They want mobile apps and partner integrations to consume its data through a clean, modern REST API, with rate limiting and centralized security, instead of every consumer having to implement the legacy system's protocol directly. What should they put in front of the legacy system?

A. A Cloud CDN endpoint to cache legacy responses at the edge
B. Apigee as an API gateway/facade that exposes modern APIs backed by the legacy system
C. A direct network path so each consumer implements the legacy protocol itself
D. A Compute Engine load balancer with no proxy layer

**9.** A startup wants to let users sign up and log in with email/password, SAML, and multi-factor authentication, and wants this working in days, not months, without building and securing their own credential store. What is the recommended approach from this module?

A. Build a custom authentication service with a self-managed user database and password hashing
B. Delegate identity management to Identity Platform / Firebase Authentication
C. Have every user authenticate using a shared IAM service account
D. Store user passwords encrypted in a Cloud Storage bucket and validate them in application code

**10.** Your team is building a service on Cloud Run and wants to build log-based metrics and trace requests across services, without managing log files or rotation inside the container. What should the application do?

A. Open a log file on the container's local disk and rotate it on a schedule
B. Write logs to standard output (stdout) and let the platform collect and centralize them
C. Write log entries directly into a Cloud Storage bucket from application code
D. Buffer all log lines in memory and flush them to BigQuery only at shutdown

**11.** A team wants every commit to a shared branch to automatically trigger a build and run unit/integration tests, and wants the verified build automatically stored and marked ready for release — but they still want a person to click "deploy" before anything reaches production. Which combination of practices does this describe?

A. Continuous Integration + Continuous Deployment
B. Continuous Integration + Continuous Delivery
C. Continuous Delivery only, with no automated testing
D. Continuous Deployment only, with manual testing

**12.** A team is migrating a 15-year-old monolithic claims-processing application to microservices. A "big bang" rewrite was rejected as too risky. Instead, they want to replace small pieces of the legacy application incrementally, with a routing layer that sends each request to either the legacy app or the new service depending on what has been migrated so far. Which pattern are they describing?

A. Blue/green deployment
B. Canary release
C. Strangler pattern
D. Circuit breaker pattern

**13.** A team wants to release a new version of a service to just 5% of production traffic first, watch error rates and latency, and only ramp up to 100% if the new version looks healthy. Which deployment strategy is this?

A. Blue/green deployment
B. Canary release
C. Strangler pattern
D. Continuous Delivery

**14.** A developer is writing a new script in 2026 to copy build artifacts into a Cloud Storage bucket as part of a deployment pipeline, and wants the best performance along with a command style consistent with the rest of their `gcloud` scripts. Which command should they use?

A. `gsutil cp`
B. `gcloud storage cp`
C. `bq load`
D. `gcloud compute scp`

**15.** A platform team wants to give every engineer on a 200-person team a consistent, secure, persistent, and reproducible cloud development environment that lives inside the company's VPC, is reachable from a browser, SSH, or a local IDE, and doesn't require each engineer to install tooling locally. Which product fits, and why is the most obvious alternative wrong for this job?

A. Cloud Shell — it's free and browser-based, so it's good enough for a whole team's daily development environment
B. Cloud Workstations — provides managed, reproducible, persistent, VPC-contained dev environments; Cloud Shell is an ephemeral, per-session admin shell that terminates after an hour of inactivity and isn't meant to be a team's primary dev environment
C. Cloud Code — it's only an IDE plugin, not a hosted environment
D. A shared Compute Engine VM with SSH credentials distributed to the whole team

---

## Answer Key & Explanations

**1. Correct answer: B**
Configuration values must live in environment variables, not as constants in source code, so the exact same build can move between dev, staging, and production with only the environment changing. Option D is the tempting distractor — it moves the value out of the code, but committing per-environment config files to the repo still couples config changes to code changes and defeats the "same build, different env vars" guarantee.

**2. Correct answer: C**
The module is explicit that microservices are not automatically "better" — refactoring a monolith is costly, so the decision must weigh that cost against the benefit of independent scaling and deployment. Option A is the classic exam trap: assuming microservices are always the right modern choice regardless of context.

**3. Correct answer: B**
The module maps an Eventarc trigger to the "event queue" role and a Pub/Sub topic to the "message queue" role. Option A is the tempting distractor because it swaps the two — Pub/Sub feels intuitively more like a generic "queue," but the module's specific mapping is Eventarc for events, Pub/Sub for messages.

**4. Correct answer: B**
Loose coupling on an HTTP payload means a consumer should read only the fields it needs, which lets the publisher add new fields later without breaking anyone. Option A is the tempting distractor because strict schema validation looks like good engineering discipline, but it tightly couples the consumer to the full shape of the payload and breaks the moment a new field is added.

**5. Correct answer: B**
Stateless components should not store or share state internally; shared state is a common scalability bottleneck, and instances should be interchangeable so any of them can handle any request. State belongs in a separate persistent store like Firestore. Option A is the tempting distractor because it's true that Cloud Run can hold instances alive briefly, but that's not a guarantee, and relying on it violates the whole point of designing for elastic, interchangeable instances.

**6. Correct answer: B**
Transient failures call for retry with exponential backoff (so you don't hammer an already-struggling backend), while long-lasting, persistent failures call for a circuit breaker that stops sending traffic entirely until the service recovers. Option A is the tempting distractor because it applies the exact right techniques but swaps which failure type gets which one — a mistake the module explicitly warns against.

**7. Correct answer: B**
Memorystore (Redis/Memcached, fully managed, in-memory) is for application data that's expensive to recompute; Cloud CDN (Google's global edge network) is for web/static content served close to users. Option A is the tempting distractor because it swaps the two layers — both are "caching," but they operate at different layers of the stack.

**8. Correct answer: B**
Apigee is the module's named solution for putting a modern API facade in front of legacy systems that can't be rewritten or moved, adding security, rate limiting, quotas, and analytics along the way. Option A is a plausible-sounding distractor because Cloud CDN also sits "in front" of something, but it caches static content — it doesn't provide an API facade, security, or rate limiting for a legacy backend.

**9. Correct answer: B**
Delegating identity management to Identity Platform or Firebase Authentication avoids building and securing a custom credential store, and both natively support email/password, SAML, OpenID Connect, and MFA. Option C is the tempting distractor because IAM service accounts are a real Google Cloud identity mechanism, but they're for workload/application identity, not end-user sign-up and login.

**10. Correct answer: B**
Applications should treat logs as an event stream and write to stdout, letting the underlying platform (Google Cloud Observability) collect, centralize, and build metrics/traces from them — this is especially natural for serverless compute like Cloud Run, which has no persistent disk to manage. Option A is the tempting distractor because managing a local log file feels like the "traditional" server approach, but it directly contradicts the module's stated best practice and doesn't fit serverless environments.

**11. Correct answer: B**
Continuous Integration covers the automatic build-and-test-on-commit step; Continuous Delivery covers automatically storing the verified build as release-ready, but leaves the final production push to a human. Option A is the tempting distractor because Continuous Deployment sounds like the "more complete" or "more automated" answer, but it specifically removes the human approval step this scenario says the team wants to keep.

**12. Correct answer: C**
The strangler pattern is exactly this: incrementally replacing pieces of a legacy application behind a facade that routes each request to the old or new implementation, until the legacy system is fully "strangled." Option A is a tempting distractor because blue/green also involves two things existing side by side, but blue/green is an instant full traffic cutover between two complete environments, not an incremental, piece-by-piece replacement.

**13. Correct answer: B**
Canary release means rolling a new version out to a small slice of traffic first, monitoring it, and then ramping up gradually — like a canary detecting danger before it spreads. Option A is the tempting distractor because blue/green is also a "safe rollout" strategy, but it switches all traffic from the old environment to the new one at once rather than a small percentage first.

**14. Correct answer: B**
`gcloud storage` is now the preferred command-line tool for managing Cloud Storage — it performs better than `gsutil` and matches the style of other `gcloud` commands. Option A is the tempting distractor because `gsutil` still works and is what many older tutorials show, but the module is explicit that `gcloud storage` is now preferred.

**15. Correct answer: B**
Cloud Workstations is built for exactly this: managed, reproducible, secure, persistent team development environments that run inside the customer's VPC. Option A is the tempting distractor because Cloud Shell is real, free, and browser-based, but it's an ephemeral per-session admin shell (5 GB persistent disk, terminates after an hour of inactivity) meant for quick management tasks — not a standardized environment for an entire engineering team's daily development work.
