# Module 3 — Storage & Databases: Practice Questions

Scenario-based practice questions covering [deep-dive/03-storage](../../../deep-dive/03-storage/storage.md), weighted toward the exam traps and decision-table distinctions that trip people up most: Firestore vs Bigtable, Bigtable vs BigQuery, and Cloud SQL vs AlloyDB vs Spanner.

Read the deep dive first. Try the questions cold, then check your answers against the key below.

---

## Questions

1. PixShare, a photo- and video-sharing startup, lets users upload photos and short videos up to several gigabytes each. The app needs to serve these files over HTTP from anywhere in the world, and occasionally a mobile client needs to fetch only a small byte range of a large video (for example, to preview a thumbnail frame) without downloading the whole file. Where should PixShare store these uploads?
   A. Firestore, because it is a flexible NoSQL document database
   B. Cloud Storage, because it is designed for large, unstructured objects accessed over HTTP
   C. Bigtable, because it handles high-throughput writes
   D. Cloud SQL, storing each file as a BLOB column

2. A chat application needs to sync messages to mobile and web clients in real time, keep working when a user's device goes offline and sync automatically once reconnected, and store a flexible, nested structure for messages and threads that may evolve as the app grows. Which database best fits this application?
   A. Bigtable, because it handles massive write throughput
   B. BigQuery, because it can scale to any data size
   C. Firestore, because it offers real-time sync, offline support, and a flexible document model
   D. Cloud SQL, because chat data is inherently relational

3. An industrial IoT platform ingests sensor readings from millions of devices, tens of thousands of writes per second, and needs to look up the latest reading for a given device ID in under 10 milliseconds. The team also wants to reuse existing Apache HBase-based tooling with minimal changes. Which service fits?
   A. BigQuery, because it can query petabytes of sensor data
   B. Bigtable, because it offers sub-10ms key-value lookups at massive scale and is HBase API-compatible
   C. Firestore, because it is a fully managed NoSQL database
   D. Cloud SQL, using a heavily indexed table

4. A retail company's BI team wants to run ad-hoc SQL queries and build dashboards across three years of clickstream data — tens of petabytes — without provisioning or managing any servers or clusters. Which service should they use?
   A. Bigtable, since it is designed for huge datasets
   B. BigQuery, a serverless data warehouse that scans terabytes in seconds and petabytes in minutes
   C. AlloyDB, using its Columnar Engine
   D. Cloud SQL with read replicas for reporting

5. A small internal tool built on PostgreSQL handles a few hundred transactions per day for a single team in one region. There's no need for advanced analytics, no plan to go multi-region, and the team just wants to lift-and-shift the existing database with minimal changes. Which service is the best fit?
   A. Spanner, for its industry-leading SLA
   B. AlloyDB, for maximum PostgreSQL performance
   C. Cloud SQL, a general-purpose managed relational database compatible with PostgreSQL
   D. Bigtable, since it scales automatically

6. A bank's PostgreSQL-based ledger system needs to keep processing thousands of transactional writes per second (deposits, withdrawals) while simultaneously running real-time analytical reports on that same data, without exporting it to a separate warehouse or leaving the PostgreSQL ecosystem. Which service fits?
   A. Cloud SQL for PostgreSQL
   B. AlloyDB, whose Columnar Engine accelerates analytical queries while preserving transactional performance
   C. BigQuery, migrating the ledger data there
   D. Firestore, for its flexible schema

7. A global payments platform operates across North America, Europe, and Asia. It requires relational data with full ACID transactions, strong consistency worldwide (a write in one region must be immediately visible everywhere), horizontal scalability as transaction volume grows, and an SLA no lower than 99.999%. Which database should they choose?
   A. Cloud SQL with cross-region read replicas
   B. AlloyDB with multi-region clusters
   C. Spanner, built for global strong consistency and horizontal scalability
   D. Bigtable, for its massive scale

8. An e-commerce backend needs to record thousands of small, fast transactions per second — creating orders, updating inventory counts, adjusting account balances — each needing immediate consistency. Which category of workload is this, and which service fits?
   A. OLAP workload, so BigQuery
   B. OLTP workload, so Cloud SQL
   C. OLAP workload, so Bigtable
   D. OLTP workload, so Cloud Storage

9. A team is deploying a Cloud Run service that must connect to a Cloud SQL instance. They want secure access without manually managing IP allowlists or SSL certificates for every environment. What should they use?
   A. A public IP with a broad firewall allowlist
   B. VPC Peering to another project
   C. The Cloud SQL Auth Proxy, which tunnels traffic securely without manual IP/SSL configuration
   D. A service account key embedded in application code

10. A mobile game already uses Redis in production for a real-time leaderboard built on sorted sets, and the team wants a fully managed drop-in replacement with no application code changes. Which Memorystore option should they choose?
    A. Memorystore for Memcached, since it's simpler
    B. Memorystore for Redis, which is fully protocol-compatible and supports richer data structures like sorted sets
    C. Firestore, for real-time updates
    D. Bigtable, for low-latency lookups

11. During a design review, an engineer proposes storing user shopping-cart balances only in Memorystore, with no other backing database, arguing it's fast enough and avoids extra components. What is the main risk of this design?
    A. There is no risk; Memorystore is durable enough for primary data
    B. Memorystore is an in-memory cache, not a durable source of truth — cart data could be lost and should live in a persistent database
    C. Memorystore cannot be secured with IAM or VPC
    D. Memorystore cannot handle the required throughput

12. An application needs to store user-uploaded profile photos, a relational product catalog with strict referential integrity, a fast session cache, and historical data for business-intelligence dashboards. What is the recommended architecture?
    A. Force all four data types into a single Cloud SQL instance for simplicity
    B. Use one purpose-fit service per use case — Cloud Storage, Cloud SQL, Memorystore, and BigQuery — combined in a polyglot persistence architecture
    C. Store everything in Firestore since it is flexible enough for any data shape
    D. Store everything in Bigtable since it scales to any size

13. A team's Cloud SQL instance is approaching its maximum storage capacity for a single instance, and data volume keeps growing. What is the recommended way to scale beyond a single instance's limit?
    A. It's impossible — they must migrate to a completely different storage paradigm
    B. Shard or partition the data across multiple database instances, since size limits apply per instance, not per architecture
    C. Compress all existing data to fit within one instance permanently
    D. Switch to Cloud Storage and store rows as individual objects

14. A video-streaming feature needs to let users seek to any point in a multi-gigabyte video file and start playback immediately, without downloading the entire file first. The videos are stored as objects in Cloud Storage. Which capability enables this?
    A. Cloud Storage does not support partial downloads; the whole object must be fetched
    B. A ranged GET request, which retrieves only the requested byte range of the object
    C. Splitting each video into thousands of small Firestore documents
    D. Storing the video as a BLOB column in Cloud SQL for random access

15. An analytics team currently runs Apache HBase on self-managed infrastructure and wants to migrate to a managed Google Cloud service with minimal code changes. They also need to resize their cluster during traffic spikes (like a seasonal sale) without any service downtime. Which service fits both requirements?
    A. Firestore, since it auto-scales
    B. Bigtable, which is HBase API-compatible and supports seamless cluster resizing with no downtime
    C. BigQuery, since it is serverless
    D. Cloud SQL, using vertical scaling

---

## Answer Key & Explanations

1. **B — Cloud Storage.** Cloud Storage is purpose-built object storage for large, unstructured blobs like photos and videos, addressed by object name and retrievable via ranged GET for partial byte fetches, up to 5 TB per object. Firestore (A) is the tempting distractor — it's also a flexible Google Cloud NoSQL service, but it's designed for structured, queryable documents, not multi-gigabyte binary blobs.

2. **C — Firestore.** Firestore's document/collection model, strong consistency, and built-in real-time sync plus offline support are exactly designed for mobile/web apps like chat. Bigtable (A) is the classic trap — both are NoSQL, but Bigtable targets billions of rows with sub-10ms single-key lookups, not hierarchical documents with real-time offline sync.

3. **B — Bigtable.** A sparse, massive table with sub-10ms lookups and HBase API compatibility is Bigtable's exact profile. BigQuery (A) is the name-similarity trap — it's an analytical data warehouse, not built for millisecond operational lookups.

4. **B — BigQuery.** Serverless OLAP with SQL over petabytes is BigQuery's core purpose. Bigtable (A) is again the name trap — it's an operational NoSQL key-value store, not a serverless SQL data warehouse, and it isn't designed for ad-hoc analytical SQL.

5. **C — Cloud SQL.** Simple, low-traffic, single-region, minimal-refactor migration is Cloud SQL's exact sweet spot. AlloyDB (B) is the trap — it's also PostgreSQL-compatible, but its extra performance and HTAP capabilities are unnecessary complexity for a workload this light.

6. **B — AlloyDB.** AlloyDB's compute/storage separation and Columnar Engine deliver up to 100x faster analytics with no penalty to transactional throughput — the definition of HTAP. Cloud SQL (A) is the trap since it's also managed PostgreSQL, but it doesn't offer AlloyDB's analytical acceleration for a mixed transactional-plus-analytical workload.

7. **C — Spanner.** Strong consistency, horizontal scale, relational data, global reach, and a 99.999% SLA together point unambiguously to Spanner — no other service combines all of these. Cloud SQL's cross-region read replicas (A) only offer near-real-time, not truly strongly-consistent, global reads.

8. **B — OLTP workload, Cloud SQL.** Many small, fast, immediately-consistent transactions is the textbook definition of OLTP, which Cloud SQL (along with AlloyDB and Spanner) is built to serve. BigQuery (A) is the trap: it's built for OLAP — large-scale analytical scans and reporting — not sub-second transactional writes.

9. **C — Cloud SQL Auth Proxy.** The proxy runs a local client that speaks the standard database protocol to the app and builds a secure tunnel to the server side, removing the need to manage allowlists or certificates by hand. Option A is the trap — it's the manual, error-prone approach the proxy exists to replace.

10. **B — Memorystore for Redis.** Memorystore for Redis is fully protocol-compatible with open-source Redis, including sorted sets, so the existing leaderboard code runs unmodified. Memcached (A) is the trap — Memorystore supports it too, but Memcached is a simpler key-value cache without Redis's richer data structures like sorted sets.

11. **B — Memorystore is a cache, not a durable source of truth.** Memorystore is designed as a fast, transient cache layer in front of a persistent database (Cloud SQL, Firestore, Spanner, etc.), not as the primary store for data that must never be lost. Option A is the trap for anyone who remembers "Memorystore is fast" but forgets it isn't durable.

12. **B — Polyglot persistence.** The module's core lesson is that you are not limited to one database — pick the best-fit service per workload (object storage, relational, cache, warehouse) and combine them. Option A is the trap that violates the "no one size fits all" principle the module opens with.

13. **B — Shard or partition across multiple instances.** Storage limits are per database instance; splitting data across multiple instances is the standard way to grow beyond a single instance's ceiling without abandoning the service. Option A overstates the limit as an architectural dead end, which the module explicitly says it is not.

14. **B — Ranged GET request.** Cloud Storage supports ranged GET requests, letting a client request a specific byte range of an object — exactly what's needed to seek into a large video without downloading it all. Option A is the trap for anyone who assumes object storage only supports whole-object downloads.

15. **B — Bigtable.** Bigtable's HBase API compatibility eases the migration, and its seamless scaling model applies cluster configuration changes instantly with no downtime during resize. BigQuery (C) is the recurring name trap — it's serverless, but it's an analytical warehouse, not an HBase-compatible operational store.
