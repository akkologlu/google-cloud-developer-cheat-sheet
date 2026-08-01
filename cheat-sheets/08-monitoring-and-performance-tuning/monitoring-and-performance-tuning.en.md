# Module 8 – Monitoring and Performance Tuning

---

# Overview

Deploying an application is only half the job.

The other half is knowing whether it is healthy, why it is slow, and why it broke.

Google Cloud groups the tooling for this under one umbrella: **Google Cloud Observability**.

```text
Metrics

+

Logs

+

Metadata

↓

Google Cloud Observability
```

It works across Google Cloud, other clouds, and on-premises environments — not just Google Cloud resources.

The five tools covered in this module are:

- Cloud Monitoring
- Cloud Logging
- Error Reporting
- Cloud Trace
- Cloud Profiler

Prometheus and Google Cloud Managed Service for Prometheus are also covered as part of the Logging/Monitoring story.

---

# The Observability Chain

Think of the five tools as links in one chain, not five separate products.

```text
Cloud Monitoring

↓ (something is wrong)

Cloud Logging

↓ (what happened, in detail)

Error Reporting

↓ (which error, how often)

Cloud Trace

↓ (which request step is slow)

Cloud Profiler

↓ (which line of code is expensive)
```

Monitoring notices a problem. Logging gives context. Error Reporting summarizes and groups the failures. Trace finds the slow step. Profiler finds the expensive code.

---

# Cloud Monitoring

Cloud Monitoring is the foundation of application reliability.

It collects metrics, events, and metadata from Google Cloud services and applications, and lets you define alerting policies.

```text
Application

↓

Metrics / Events / Metadata

↓

Cloud Monitoring

↓

Dashboards + Alerts
```

## Three Ways Developers Use It

- **Dashboards** – track trends over time (database growth, daily active users, feature usage).
- **Alerting** – get notified before a metric crosses a dangerous threshold.
- **Retrospective analysis** – after an incident, correlate "what else happened at the same time".

---

## The Four Golden Signals

Every application dashboard should track these four signals.

| Signal | What it measures | Example metric | Watch out for |
| --- | --- | --- | --- |
| Latency | Time to serve a request | Response time in ms | Measure successful and failed requests **separately** |
| Traffic | Demand on the system | HTTP requests/sec, DB reads-writes/sec | The right unit is system-specific |
| Errors | Count of failed requests | HTTP 5xx, wrong-content 200, SLA violation | A "successful" HTTP 200 can still be an error |
| Saturation | How full the system is | CPU/memory/disk usage | Performance can degrade **before** reaching 100% |

Latency example:

```text
Request

↓

Backend connection lost

↓

Fails instantly (fast, but a 500 error)
```

A fast failure still counts as a failure. Mixing failed requests into a latency average hides real problems.

Errors are not only HTTP 5xx codes:

- Explicit error — HTTP 500
- Wrong content in a 200 response
- Policy error — response took longer than the promised SLA, even with no error code

---

# Cloud Logging

Cloud Logging is a real-time log management system with storage, search, analysis, and alerting.

It automatically collects logs from Google Cloud resources, and can also collect logs from your own applications.

```text
Application

↓

stdout / stderr

↓

Cloud Logging
```

## Logs Explorer vs Log Analytics

```text
Logs Explorer → individual log entries

Log Analytics → SQL over aggregated log data
```

- **Logs Explorer** – view individual log entries and related entries, useful for tracing one request.
- **Log Analytics** – run SQL queries over your logs to analyze performance in bulk.

## Log-based Alert vs Log-based Metric

```text
Pattern happens once

↓

Log-based alert

Pattern happens N times / trend

↓

Log-based metric
```

- **Log-based alert** – notify the moment a specific pattern appears in a log entry.
- **Log-based metric** – count occurrences over time, alert when a threshold is crossed, or watch a trend.

## Ops Agent

Ops Agent collects logs and metrics from third-party applications running on Compute Engine VMs.

```text
Ops Agent

├── Fluent Bit          → log collection
└── OpenTelemetry Collector → metric collection
```

- Automatically collects logs from standard locations (`/var`, `/log`, `/syslog`).
- Supports flexible processing: parse text into structured logs, add/remove/rename fields, exclude by label or regex.
- Collects standard system metrics with zero configuration: CPU, disk, memory, network, processes.
- Collects curated third-party metrics: Apache Tomcat, Apache web server, NGINX.

## Preconfigured Logging

- **Cloud Run** services and functions have built-in logging — stdout/stderr go to Cloud Logging automatically.
- **GKE** needs the "observability for GKE" integration enabled on the cluster.

## GKE Log Persistence

Kubernetes logs are **not** persistent on their own.

```text
Pod deleted

↓

Container logs gone

System logs

↓

Cleared periodically

Cluster events

↓

Removed after 1 hour
```

Send logs to Cloud Logging if you need to keep them.

## Text Log vs Structured Log

| Feature | Text log | Structured (JSON) log |
| --- | --- | --- |
| Stored in | `textPayload` | `jsonPayload` |
| Log level | None | `severity` field |
| Searchability | Hard (plain text search) | Easy (field-based query) |
| Main display text | Whole string | `message` field |
| Recommended? | Simple/quick cases only | Yes, generally |

Structured logging is recommended: it gives you a queryable `severity` level and searchable fields instead of a plain string.

---

# Prometheus and Managed Service for Prometheus

Prometheus is an open-source systems monitoring and alerting toolkit.

```text
VMs / Kubernetes

↓

Prometheus

↓

Time Series Data

↓

PromQL
```

It's popular for monitoring Kubernetes workloads and clusters, but running Prometheus at scale is operationally heavy.

**Google Cloud Managed Service for Prometheus** removes that burden.

- Works multi-cloud and cross-project — single pane of glass via Cloud Monitoring.
- **Managed collectors** — recommended for all Kubernetes environments, including GKE; the Kubernetes operator runs Prometheus for you.
- **Self-deployed collectors** — a drop-in replacement for the standard Prometheus binary.
- Also supports OpenTelemetry collectors and Ops Agent as data sources.
- Any PromQL-compatible query tool works, including Cloud Monitoring and Grafana.
- Over 1,500 free metrics are queryable without ever sending data to the service.
- Provides a fully cloud-based alerting pipeline for both Cloud Monitoring and Prometheus metrics.

---

# Error Reporting

Error Reporting counts, analyzes, and aggregates crashes in running cloud services.

```text
Application Error

↓

Error Reporting

↓

Grouped + Deduplicated

↓

Error Details Dashboard
```

## How Errors Are Detected

- **Explicitly reported** via the Error Reporting API.
- **Inferred** from log entries — Error Reporting scans logs for common patterns like stack traces.

## Grouping

Error events are intelligently grouped and deduplicated by analyzing stack traces, so you see distinct errors instead of a flood of duplicates.

The console shows, for each error group:

- Occurrence count
- First and last seen time
- Number of affected users

You can opt in to email and mobile alerts when a new error appears.

## Enabling Error Reporting by Environment

| Environment | Setup required |
| --- | --- |
| Cloud Run | Automatic |
| GKE | Add `cloud-platform` access scope |
| Compute Engine | Grant VM service account the Error Reporting Writer role |

Supported languages: Go, Java, Node.js, PHP, Python, Ruby, .NET.

Reporting is asynchronous — your code does not block waiting for the error event to be delivered.

From the Error Details page you can click **View Logs** to jump straight into Logs Explorer for full context.

---

# Cloud Trace

Cloud Trace is a distributed tracing system that collects latency data from your applications and displays it in near-real-time.

```text
Request

↓

Service A (span)

↓

Service B (span)

↓

Database (span)

↓

Trace = A + B + Database
```

## Trace vs Span

- A **trace** describes the time to complete a single operation.
- A **span** describes the time to complete a sub-operation within that trace.
- A trace contains one or more spans.

## Two Ways to Collect Trace Data

```text
Automatic tracing → Cloud Run inbound/outbound HTTP only

Instrumentation → full internal detail (OpenTelemetry + Cloud Trace Exporter)
```

- **Automatic tracing** – Cloud Run services and functions automatically get latency data for HTTP requests in and out. It does **not** capture latency inside the service.
- **Instrumentation** – use OpenTelemetry with the Cloud Trace Exporter (recommended where available), or the Cloud Trace API / client libraries, to see detail inside the service.

## Trace Explorer

- Scatter plot: one point per request, x = time, y = latency.
- Filter by attributes like method or status code.
- Click a point to open Trace Details and inspect spans.

Cloud Trace also auto-generates a daily report comparing yesterday's performance to the same day last week for top endpoints, plus custom analysis reports.

---

# Cloud Profiler

Understanding production performance is famously hard — a test environment rarely reproduces real production load.

Cloud Profiler is a statistical, low-overhead profiler that continuously gathers CPU usage and memory allocation information from production applications and attributes it to the source code that produced it.

```text
All Production Instances

↓

Sampling (statistical)

↓

CPU + Memory usage

↓

Attributed to source code
```

It uses statistical techniques and low-impact instrumentation across all production instances, so it can stay on continuously without slowing the application down.

Cloud Trace vs Cloud Profiler:

```text
Cloud Trace   → which request step is slow?   (time axis)

Cloud Profiler → which code function is expensive? (code axis)
```

---

# Five-Tool Comparison

| Tool | Collects / shows | Core question | When to use |
| --- | --- | --- | --- |
| Cloud Monitoring | Metrics, events, metadata; dashboards, alerts | Is the system healthy overall? | Continuous health tracking, trend watching, threshold alarms |
| Cloud Logging | Raw log entries (text/structured); SQL via Log Analytics | What happened, in detail? | Investigating the full context of an event |
| Error Reporting | Grouped, deduplicated errors; stack traces | Which error, how often, since when? | Prioritizing production errors |
| Cloud Trace | Per-request latency, trace/span timeline | Which step of the request is slow? | Answering "why did this request take so long?" |
| Cloud Profiler | CPU/memory usage attributed to source code | Which code line consumes resources? | Answering "why is my app burning CPU/memory?" |

---

# Module Summary

- Google Cloud Observability unifies Cloud Monitoring, Cloud Logging, Error Reporting, Cloud Trace, and Cloud Profiler across Google Cloud, other clouds, and on-premises.
- Cloud Monitoring is the foundation of reliability: dashboards for trends, alerting policies for early warning, and retrospective analysis after incidents.
- The four golden signals — latency, traffic, errors, saturation — are the minimum any dashboard should track, and each has a subtle gotcha worth remembering.
- Cloud Logging separates single-record inspection (Logs Explorer) from bulk SQL analysis (Log Analytics), and log-based alerts differ from log-based metrics.
- Structured (JSON) logging is preferred over plain text logging because it is searchable and carries a severity level.
- Ops Agent combines Fluent Bit (logs) and OpenTelemetry Collector (metrics) to instrument Compute Engine VMs.
- Kubernetes logs are not persistent by default — send them to Cloud Logging to keep them.
- Prometheus is powerful but operationally heavy to run yourself; Google Cloud Managed Service for Prometheus removes that burden while keeping PromQL.
- Error Reporting automatically groups and deduplicates crashes using stack trace analysis, and its setup effort varies by compute platform.
- Cloud Trace shows where in a request the time went, using traces made of one or more spans; automatic tracing on Cloud Run only covers inbound/outbound HTTP, not internal calls.
- Cloud Profiler continuously samples CPU and memory usage in production with low overhead, attributing cost to specific source code — a different axis from Cloud Trace.
