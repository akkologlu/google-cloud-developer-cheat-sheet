# Module 8 — Monitoring and Performance Tuning: Practice Questions

This set covers Cloud Monitoring, Cloud Logging, Google Cloud Managed Service for Prometheus, Error Reporting, Cloud Trace, and Cloud Profiler.

The questions are weighted toward the distinctions that actually trip people up on the real exam: the four golden signals, log-based alerts vs. log-based metrics, structured vs. text logging, automatic vs. instrumented tracing, and Cloud Trace vs. Cloud Profiler.

Try to answer all questions first, then check your answers against the [Answer Key & Explanations](#answer-key--explanations) section below.

---

## Questions

**1.** During an incident, your team's dashboard shows average request latency dropping sharply. Investigation reveals a downstream database connection was being refused outright, so a large share of requests failed instantly with an HTTP 500 response. A teammate concludes the application actually got faster during the incident. What is the real problem here?

A. Nothing is wrong — a lower average latency during an incident is exactly what you'd expect.
B. The dashboard is blending successful and failed request latency into a single average, which hides the real problem. Latency should be measured separately for successful and failed requests.
C. The team should be graphing saturation instead of latency during incidents.
D. The team should be graphing traffic instead of latency during incidents.

**2.** A payment API returns HTTP 200 for every request, but a bug causes 0.1% of "successful" responses to report the wrong currency total. Separately, the service has an SLA promising sub-1-second responses, and 2% of requests take 1.5 seconds. A teammate says "we have zero errors because there are no 5xx status codes." Is this correct?

A. Correct — errors only exist when the server returns an explicit HTTP error status code.
B. Incorrect — errors include explicit error codes, successful-looking responses with incorrect content, and violations of a promised policy such as a latency SLA.
C. Incorrect — only the wrong-content responses count as errors; SLA violations are a separate concern with no bearing on the errors signal.
D. Correct, but only because the currency bug belongs in Error Reporting rather than in the errors golden signal.

**3.** Your on-call rotation sets a saturation alert to fire only when CPU utilization reaches 100%. Users start reporting degraded response times while CPU utilization is still around 70%. What is the correct interpretation?

A. This indicates a bug in Cloud Monitoring — CPU metrics are under-reporting utilization.
B. Systems can begin degrading in performance well before reaching 100% utilization, so utilization targets should be set conservatively based on how the system actually behaves, not at the 100% ceiling.
C. Saturation should only ever be measured using memory utilization, never CPU.
D. This means traffic, not saturation, is the golden signal that should be alerted on instead.

**4.** Your web frontend dashboard measures traffic as HTTP requests per second. A teammate wants to reuse that exact same metric definition for a NoSQL database dashboard so that "traffic" means the same thing everywhere. What should you tell them?

A. Reuse the HTTP requests/second metric project-wide for consistency across all dashboards.
B. Traffic is measured with a system-specific metric — for a NoSQL database, that's reads or writes per second, not HTTP requests per second.
C. Use saturation instead of traffic, since traffic doesn't meaningfully apply to databases.
D. Use CPU utilization as a stand-in for traffic on the database dashboard.

**5.** You want to be notified the instant a specific critical error string (for example, `OutOfMemoryError: heap space exhausted`) shows up anywhere in your logs. Which Cloud Logging feature should you configure?

A. A log-based metric with a count threshold of 1.
B. A log-based alert, since it triggers a notification the moment a matching log pattern occurs.
C. The Error Reporting Writer IAM role.
D. A Cloud Monitoring uptime check.

**6.** You want to track how often a particular warning message appears over time, get notified only once the count crosses a threshold within a given hour, and also observe the longer-term trend over several weeks. Which Cloud Logging feature fits this need?

A. A log-based alert.
B. A log-based metric, since it's designed for counting occurrences over time, observing trends, and alerting once a threshold is crossed.
C. A Cloud Trace custom analysis report.
D. Error Reporting's grouped error view.

**7.** Your production service currently writes plain text lines to stdout. The team wants to filter logs to show only `ERROR`-severity entries and reliably search by a custom `component` field. What should you change?

A. Nothing — text logging already supports severity filtering through the `textPayload` field.
B. Switch to structured (JSON) logging: emit a single-line serialized JSON object with a `severity` field and a custom `component` field. These land in `jsonPayload` and become queryable.
C. Turn on Log Analytics — it will automatically infer severity levels from unstructured text lines without any format change.
D. Route the logs through Error Reporting instead, since it manages severity levels for you.

**8.** You install the Ops Agent on a Compute Engine VM running NGINX, and you need it to collect both application logs and curated NGINX metrics. Which underlying components are actually responsible for each job?

A. A single unified binary called Fluent Bit handles both logs and metrics.
B. Fluent Bit collects logs, and the OpenTelemetry Collector collects metrics.
C. Fluent Bit collects metrics, and the OpenTelemetry Collector collects logs.
D. Ops Agent has no internal sub-components — it is one monolithic collector.

**9.** A GKE pod crash-looped and was automatically recreated by the cluster. An hour later, an SRE wants to check the cluster events related to that pod to understand what happened, but the events are already gone. Why did this happen, and what should have been done to prevent it?

A. Cluster events are retained indefinitely by GKE; the SRE simply needs to look in a different console page.
B. GKE does not persist this data indefinitely: container logs disappear once the pod is removed, system logs are periodically cleared, and cluster events are removed after one hour. Sending logs to Cloud Logging preserves them for as long as needed.
C. This only happens when Cloud Trace is not enabled on the cluster.
D. Cluster events are stored in Cloud Profiler rather than Cloud Logging, so the SRE was looking in the wrong tool.

**10.** Your team is moving Kubernetes workloads to GKE. They already know PromQL and want dashboards and alerts built on it, but they explicitly do not want to operate and scale a Prometheus deployment themselves. What should they use?

A. Self-deployed collectors, since they are a drop-in replacement for the standard Prometheus binary.
B. Managed collectors, which are recommended for all Kubernetes environments (including GKE) because the Kubernetes operator handles running Prometheus for you.
C. The Ops Agent, since it fully replaces the need for Prometheus.
D. Cloud Trace, since it also exposes a PromQL-compatible query interface.

**11.** A legacy service was never integrated with the Error Reporting API, but it does log full stack traces to stdout whenever an unhandled exception occurs. Will Error Reporting still surface these errors?

A. No — Error Reporting only detects errors that are explicitly reported through its API.
B. Yes — Error Reporting can also infer errors by scanning log entries for common text patterns, such as stack traces, without requiring explicit API calls.
C. No — stack traces must first be sent to Cloud Trace before Error Reporting can use them.
D. Yes, but only if the service also has Cloud Profiler enabled.

**12.** Your team runs three services — one on Cloud Run, one on GKE, and one on Compute Engine — and wants Error Reporting active on all three. What's different about enabling it on each?

A. All three require the exact same manual IAM role grant.
B. Cloud Run has it enabled automatically; GKE requires adding the `cloud-platform` access scope to the cluster; Compute Engine requires granting the VM's service account the Error Reporting Writer role.
C. Error Reporting only works on Cloud Run — GKE and Compute Engine must rely on Cloud Trace instead.
D. All three are automatically enabled with zero configuration required.

**13.** A Cloud Run service shows trace data for its inbound HTTP requests, but the latency of an internal database query performed while handling those requests is completely invisible on the trace timeline. Why, and what should the team do about it?

A. Cloud Trace automatically captures all internal calls already; the query span is just slow to appear in the console.
B. Automatic tracing on Cloud Run only captures the timing of inbound and outbound HTTP requests, not latency inside the service. Instrumenting the application — for example with OpenTelemetry and the Cloud Trace exporter — is required to see internal spans like the database query.
C. Cloud Profiler must be enabled instead; it is the only tool that can show database query latency.
D. Switch to Cloud Logging log-based metrics to measure the database call's latency.

**14.** An incoming request to your service triggers an RPC call to a downstream service and then a database query before responding. Using Cloud Trace terminology, how should this be described?

A. Each RPC call and each database query is its own separate trace; the overall request has no name of its own.
B. The request from start to finish is the trace; each suboperation, such as the RPC call or the database query, is a span. A trace consists of one or more spans.
C. The span is the total request duration, and the trace is a single suboperation within it.
D. Trace and span are two names for the same concept, just shown at different zoom levels in Trace Explorer.

**15.** An engineer wants to know exactly which function in the codebase is consuming the most CPU in production, continuously, without meaningfully slowing the application down. Which tool fits, and why?

A. Cloud Trace, since it provides near-real-time performance insights.
B. Cloud Profiler, since it continuously attributes CPU and memory usage to source code across all production instances using low-overhead statistical sampling rather than tracking every single operation.
C. Log Analytics, by writing a SQL query against CPU-related log entries.
D. Error Reporting, since CPU spikes are surfaced there as errors.

---

## Answer Key & Explanations

**1. Correct answer: B.**
The scenario is the exact exam trap the module calls out: a connection refusal fails fast, so lumping that failed request's latency in with successful requests artificially drags the average down and hides how "healthy-slow" the service actually is. The fix is always to measure latency for successful and failed requests separately, not to swap latency out for a different signal (A, C, D).

**2. Correct answer: B.**
The errors golden signal is broader than HTTP status codes: it includes explicit errors (5xx), successful-looking responses with wrong content (the currency bug), and policy/SLA violations (the 1.5-second responses breaking the sub-1-second promise). The tempting distractor is A/D, which assumes "no 5xx" means "no errors" — that's the mistake the module explicitly warns against.

**3. Correct answer: B.**
Saturation is about how "full" a system is, and the module stresses that systems commonly start degrading well before hitting 100% utilization — for example, queuing delay can spike once CPU crosses a much lower threshold. The lesson is to calibrate utilization targets to real observed behavior, not to assume 100% is the meaningful cutoff (which is what makes B correct and A/C/D wrong).

**4. Correct answer: B.**
Traffic is explicitly a system-specific metric: HTTP requests/second makes sense for a web server, but a NoSQL database's traffic is naturally measured in reads or writes per second. Forcing one universal traffic definition onto every system (A) is the trap; C and D swap in the wrong golden signal entirely.

**5. Correct answer: B.**
"Notify me the moment this pattern appears" is the defining use case for a log-based alert — it's an immediate, event-driven notification tied to a specific log pattern. A log-based metric (A) is built for counting/trending over time, not instant single-event notification, which is why it's the tempting-but-wrong choice here.

**6. Correct answer: B.**
Counting occurrences, alerting once a threshold is crossed, and observing longer-term trends are exactly what log-based metrics are designed for. A log-based alert (A) is the tempting distractor because it also involves "getting notified," but it's built for immediate pattern matches, not counting/trend analysis.

**7. Correct answer: B.**
Text logs have no `severity` field and are hard to search reliably; structured (JSON) logs place data in `jsonPayload`, let you set `severity` explicitly, and make custom fields like `component` queryable. Log Analytics (C) is a tempting distractor because it does let you run SQL over logs, but it can't invent severity levels or structured fields out of raw unstructured text — the format has to change at the source.

**8. Correct answer: B.**
The Ops Agent is not one monolithic tool — it's built from two specialized open-source components: Fluent Bit handles high-throughput log collection, and the OpenTelemetry Collector handles metric collection. Mixing these up (C) or assuming a single binary does everything (A, D) is the exact trap the module flags.

**9. Correct answer: B.**
GKE does not keep logs and events around indefinitely: container logs vanish when the pod is removed, system logs are cleared periodically, and cluster events are specifically removed after one hour. Sending everything to Cloud Logging is what preserves the evidence you'd need for exactly this kind of post-incident investigation — the module frames this as the direct reason GKE observability integration with Cloud Logging matters.

**10. Correct answer: B.**
For all Kubernetes environments, including GKE, managed collectors are the recommended choice because the Kubernetes operator handles running Prometheus for you — no manual scaling or operating a Prometheus deployment. Self-deployed collectors (A) are a valid drop-in replacement for the Prometheus binary, but they're more relevant outside Kubernetes or when you need tighter manual control — which is exactly why they're the tempting-but-wrong pick here.

**11. Correct answer: B.**
Error Reporting detects errors through two paths: explicit reporting via its API, and inference from log entries that match common text patterns like stack traces. A service doesn't need any special code changes for the second path to work — just normal stack-trace logging to stdout, which makes A the tempting-but-wrong "you must integrate the API" trap.

**12. Correct answer: B.**
Enabling Error Reporting scales with how much operational control the compute option gives you: Cloud Run is automatic, GKE needs the `cloud-platform` access scope added at cluster creation, and Compute Engine needs the VM's service account granted the Error Reporting Writer role. Assuming one uniform setup step across all three (A, D) is the trap.

**13. Correct answer: B.**
Automatic tracing on Cloud Run only covers the inbound and outbound HTTP request boundary — it does not see what happens inside the service, such as a database call. To capture that internal latency, the application needs to be instrumented, with OpenTelemetry plus the Cloud Trace exporter being the recommended approach. Assuming "Cloud Run means fully automatic, complete tracing" (A) is precisely the exam trap the module warns about.

**14. Correct answer: B.**
A trace describes the completion of a single overall operation, and a span describes the completion of a suboperation within that trace — a trace always contains one or more spans. Swapping the definitions (C) or treating every suboperation as its own unrelated trace (A) are the common mix-ups.

**15. Correct answer: B.**
Cloud Profiler is purpose-built for exactly this: it continuously samples CPU and memory usage across all production instances and attributes it back to source code, using statistical, low-overhead techniques so it can run in production without noticeably slowing the app down. Cloud Trace (A) is the tempting distractor because it also deals with "performance," but it answers a different question — which request step is slow — not which function is consuming resources.
