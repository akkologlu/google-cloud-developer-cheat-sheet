# Module 3 – Google Cloud Storage Options

> Learn how to choose the right Google Cloud storage service based on your application's data and workload.

---

# Learning Objectives

After completing this module, you should be able to:

- Understand the different storage services available in Google Cloud.
- Choose the right storage solution for different application scenarios.
- Understand the differences between relational and NoSQL databases.
- Differentiate between OLTP and OLAP workloads.
- Know when to use Cloud Storage, Firestore, Bigtable, Cloud SQL, AlloyDB, Spanner, BigQuery, and Memorystore.

---

# The Big Picture

One of the biggest mistakes new developers make is trying to store every type of data in a single database.

Real-world applications don't work that way.

Imagine you're building an application similar to Instagram.

Your application needs to store:

- User profile information
- Photos
- Videos
- Comments
- Likes
- Chat messages
- Analytics
- Cached data

These data types have completely different requirements.

Google Cloud provides different storage services because each one is optimized for a different use case.

**There is no single database that is best for everything.**

---

# Cloud Storage

## What is Cloud Storage?

Cloud Storage is Google Cloud's managed **Object Storage** service.

Unlike a database, Cloud Storage does not understand the content of a file.

To Cloud Storage, every file is simply a collection of bytes.

Whether you upload:

- image.jpg
- video.mp4
- report.pdf
- backup.zip

they are all treated exactly the same.

---

## Best Use Cases

Cloud Storage is ideal for storing:

- Images
- Videos
- Documents
- Backups
- Static website assets
- User uploads
- Log files

Objects can be as large as **5 TB**.

Cloud Storage is designed for:

- High durability
- High availability
- Massive scalability
- Global access

---

## When NOT to use Cloud Storage

Cloud Storage is **not** a database.

Do not use it when you need:

- SQL queries
- Transactions
- JOIN operations
- Relationships between records

---

# Firestore

## What is Firestore?

Firestore is a **serverless NoSQL document database**.

Instead of tables and rows, data is organised as:

```text
Collection
    └── Document
            └── Fields
```

Example:

```text
Users
    ├── Abdullah
    │      ├── name
    │      ├── age
    │      └── city
    │
    └── John
           ├── name
           └── country
```

Documents can also contain nested objects and subcollections.

---

## Advantages

Firestore provides:

- Automatic scaling
- Strong consistency
- Real-time updates
- Offline support
- Flexible schema

Unlike SQL databases, documents do not all need to have the same structure.

---

## Best Use Cases

Firestore is an excellent choice for:

- Mobile applications
- Web applications
- Chat applications
- Social media apps
- User profiles
- Applications with rapidly changing data structures

---

# Bigtable

## What is Bigtable?

Bigtable is a **high-performance NoSQL database** designed for enormous amounts of data.

It can store:

- Billions of rows
- Thousands of columns
- Terabytes to petabytes of data

Bigtable is optimised for extremely fast key-value lookups.

Typical latency is under **10 milliseconds**.

---

## Best Use Cases

Bigtable is ideal for:

- Time-series data
- IoT data
- Monitoring systems
- User behaviour tracking
- Event logging
- Large-scale operational workloads

Example:

Every time a YouTube user watches a video, an event is generated.

Millions of these events arrive every second.

Bigtable is designed for this kind of workload.

---

## When NOT to use Bigtable

Bigtable is not intended for:

- SQL queries
- Complex joins
- Relational data

---

# Cloud SQL

## What is Cloud SQL?

Cloud SQL is Google's managed relational database service.

It supports:

- MySQL
- PostgreSQL
- SQL Server

Google automatically manages:

- Backups
- Replication
- Failover
- Maintenance

You continue writing normal SQL.

---

## Best Use Cases

Cloud SQL is ideal for:

- Web applications
- E-commerce systems
- ERP systems
- CRM applications
- Traditional business applications

Any application that already uses MySQL or PostgreSQL can migrate with minimal changes.

---

# Primary Database vs Read Replica

Cloud SQL supports **Read Replicas**.

Understanding this concept is very important.

## Primary Database

The Primary database is the source of truth.

It handles:

- INSERT
- UPDATE
- DELETE

It can also process SELECT queries.

---

## Read Replica

A Read Replica is a copy of the Primary database.

It automatically receives updates from the Primary.

However, applications **cannot write** to a replica.

It is used only for read operations.

---

## Why use Read Replicas?

In most applications:

- Read operations greatly outnumber write operations.

Example:

An online shop might receive:

- 5,000 new orders (writes)
- 5 million product views (reads)

Without replicas:

```text
All requests
        │
        ▼
Primary Database
```

With replicas:

```text
              Primary
           (Read + Write)
                  │
      ┌───────────┴───────────┐
      ▼                       ▼
 Read Replica            Read Replica
   (Read)                  (Read)
```

This distributes the read workload and improves performance.

> **Remember:** A Read Replica is **not** a backup.
> It exists to improve scalability.

---

# AlloyDB

## What is AlloyDB?

AlloyDB is Google's next-generation PostgreSQL database.

Unlike traditional PostgreSQL, AlloyDB separates:

- Compute
- Storage

This architecture allows much better scalability.

Google claims:

- Up to 4× faster transactional performance
- Up to 100× faster analytical queries

while remaining fully PostgreSQL compatible.

---

## Best Use Cases

Choose AlloyDB when you need:

- PostgreSQL compatibility
- High transactional performance
- Analytical queries on operational data
- Automatic scaling

---

# Spanner

## What is Spanner?

Spanner is Google's globally distributed relational database.

It combines:

- SQL
- Horizontal scalability
- Strong consistency

Unlike Cloud SQL, Spanner is designed for applications running across multiple regions.

It also provides an industry-leading **99.999% SLA**.

---

## Best Use Cases

Spanner is ideal for:

- Banking systems
- Financial applications
- Payment systems
- Airline reservation systems
- Global e-commerce platforms

Any application that requires global availability with consistent data is a good candidate for Spanner.

---

# BigQuery

## What is BigQuery?

BigQuery is **not** a transactional database.

It is a **serverless data warehouse** built for analytics.

BigQuery is designed to analyse massive datasets.

Google states that it can scan:

- Terabytes in seconds
- Petabytes in minutes

---

## Best Use Cases

BigQuery is ideal for:

- Business Intelligence
- Dashboards
- Reporting
- Data analytics
- Data exploration
- Machine learning datasets

Example:

Instead of asking:

> "Show customer #15."

BigQuery answers questions like:

> "Which city generated the highest sales over the last five years?"

---

# Bigtable vs BigQuery

Although their names are similar, these products solve completely different problems.

| Bigtable                | BigQuery                |
| ----------------------- | ----------------------- |
| NoSQL database          | Data warehouse          |
| Stores operational data | Analyses stored data    |
| Fast reads and writes   | Fast analytical queries |
| Key-value access        | SQL analytics           |
| Millisecond latency     | Scans huge datasets     |

An easy way to remember the difference:

> **Bigtable stores data.**

> **BigQuery asks questions about data.**

---

# Memorystore

## What is Memorystore?

Memorystore is Google's managed in-memory cache service.

It supports:

- Redis
- Memcached

Because data is stored in memory instead of on disk, access is extremely fast.

---

## Best Use Cases

Memorystore is commonly used for:

- Session storage
- Application caching
- Gaming
- Leaderboards
- Frequently accessed data

Instead of repeatedly querying the database, applications can retrieve data directly from cache.

---

# OLTP vs OLAP

Understanding these two workload types is essential.

---

## OLTP (Online Transaction Processing)

OLTP systems handle day-to-day business operations.

Examples:

- Creating an order
- Logging in
- Making a payment
- Sending a message
- Updating a customer profile

Characteristics:

- Small transactions
- Very fast response times
- Many concurrent users
- Frequent inserts and updates

Typical Google Cloud services:

- Cloud SQL
- AlloyDB
- Spanner
- Firestore
- Bigtable

---

## OLAP (Online Analytical Processing)

OLAP systems analyse existing data.

Instead of processing transactions, they answer business questions.

Examples:

- Which product sold the most last year?
- Which city generates the highest revenue?
- Monthly sales trends
- Customer behaviour analysis

Characteristics:

- Large scans
- Complex queries
- Aggregations
- Reporting
- Dashboards

Typical Google Cloud service:

- BigQuery

---

## Real-World Example

Imagine an online food delivery application.

### During the day

Customers:

- Place orders
- Make payments
- Track deliveries

These are OLTP operations.

---

### At night

Management wants to know:

- Which city placed the most orders?
- Which restaurant earned the highest revenue?
- Average delivery time by region

These are OLAP operations.

---

# Choosing the Right Storage Service

| Data Type                     | Recommended Service |
| ----------------------------- | ------------------- |
| Images                        | Cloud Storage       |
| Videos                        | Cloud Storage       |
| Documents                     | Cloud Storage       |
| Mobile App Data               | Firestore           |
| Chat Messages                 | Firestore           |
| Time-Series Data              | Bigtable            |
| User Behaviour                | Bigtable            |
| MySQL/PostgreSQL Applications | Cloud SQL           |
| High-Performance PostgreSQL   | AlloyDB             |
| Global Relational Database    | Spanner             |
| Analytics & Reporting         | BigQuery            |
| Cache                         | Memorystore         |

---

# Module Summary

Choosing the right storage service is about understanding your data.

Ask yourself:

- Is it a file?
- Is it structured or unstructured?
- Is it transactional?
- Is it analytical?
- Does it require global consistency?
- Does it need extremely fast reads?
- Is it temporary cache?

Google Cloud offers different storage services because each service is optimised for a different workload.

The best applications often use multiple storage services together.

Example:

- Cloud Storage → Product images
- Cloud SQL → Orders and customers
- Memorystore → Product cache
- BigQuery → Sales reports

---

# Certification Tips

When solving exam questions, first identify the type of data.

- 📁 File → Cloud Storage
- 📄 Flexible document → Firestore
- 📊 Massive key-value data → Bigtable
- 🗄️ Relational database → Cloud SQL
- ⚡ High-performance PostgreSQL → AlloyDB
- 🌍 Global relational database → Spanner
- 📈 Analytics → BigQuery
- 🚀 Cache → Memorystore

A simple decision process:

> **What kind of data am I storing, and what am I trying to do with it?**

Answering those two questions usually leads you to the correct Google Cloud storage service.
