# Objective 3.1 — Observability

## SECTION 1 — Exam Objectives

- **Objective 3.1:** Given a scenario, configure appropriate resources to achieve observability — part of Domain 3.0 (Operations) of the CompTIA Cloud+ CV0-004 exam.
- **Logging** — the capture, centralization, and lifecycle of discrete event records. Sub-areas: Collection, Aggregation, Retention.
- **Alerting** — turning observations into actionable notifications. Sub-areas: Triage, Response.
- **Tracing** — following a single request as it flows across distributed services to find latency and failure points.
- **Monitoring** — the continuous collection of quantitative **metrics** that describe system health over time.
- **Why it matters** — you cannot fix what you cannot see. The exam asks which tool or configuration fits which goal: collect logs, aggregate them into a searchable store, retain them per policy, monitor metrics, trace requests, and alert the right people with a triage-and-response workflow. Mantra: **metrics tell you WHAT, logs tell you WHY, traces tell you WHERE, and alerts tell you WHEN to act.** Every cloud provider ships a native stack; the AWS mapping in Section 5 is explicitly testable.

---

## SECTION 2 — EXAM KNOWLEDGE

### 2.1 — Logging: Collection

- **Definition:** Capturing log data from every layer of the stack — operating systems (syslog, Windows Event Log), applications (stdout/stderr, language loggers), network devices, load balancers, databases, and cloud control-plane audit streams. Two key decisions: **agent vs. agentless** (an agent like Fluent Bit/CloudWatch collects, tags, and pre-processes at the source even on flaky networks; agentless pulls from a managed API on a schedule with less control), and **structured vs. unstructured** (JSON logs are machine-parseable and far easier to query at scale).
- **Why it matters:** Collection is step one; uncollected logs are useless. Avoid "log blindness" (missing critical sources) and "log noise" (capturing everything drowns signal). A common exam scenario gives an intermittently failing app and asks which resource to configure — the answer is usually a logging agent or log group capturing application events, not a metrics dashboard. Collection also touches compliance: you must capture authentication/authorization events for audit. Inventory every component and ensure each emits to a central pipeline.
- **Analogy:** Collection is installing sensors on every machine in a factory — skip one and that machine fails silently; wire too many noisy ones and the real alarm gets buried.
- **Example:** Install a Fluent Bit agent on a VM to tag and forward app logs reliably even when the network is flaky, redacting secrets at the source; agentless pulls logs from a managed service API on a schedule. Either way, structured (JSON) logs make later querying trivial.

### 2.2 — Logging: Aggregation

- **Definition:** Gathering logs from many heterogeneous sources into a single, queryable, centralized store so operators can correlate events across the fleet. The pipeline usually runs: agents ship logs → a log router (Fluentd, Logstash, or a cloud ingestion endpoint) parses and enriches with metadata (region, instance ID, service name) → a search backend (OpenSearch, Splunk, or a cloud log service). Key concepts: **shipping vs. streaming** (batch vs. near-real-time), **normalization** (one common schema), and **cardinality** (high-cardinality fields like per-user IDs make indexing expensive).
- **Why it matters:** Without aggregation, logs are scattered across hundreds of instances and you cannot answer "show me every error across all services in the last hour." Exam scenarios present a multi-region outage and ask how to correlate symptoms — the answer is centralized aggregation with consistent tags. Aggregation also enables log-based metrics and dashboards. Do not confuse it with retention: aggregation brings logs together; retention decides how long you keep them. A good aggregation tier reduces mean-time-to-detect via a single pane of glass.
- **Analogy:** Aggregation is consolidating every department's paper logs into one searchable database — instead of running to a hundred filing cabinets, you run one query.
- **Example:** Agents ship logs to Fluentd, which enriches each with region/instance-id/service-name and writes to OpenSearch; a single query now finds every error across all services. Keep high-cardinality fields (per-user IDs) out of indexed fields to control cost.

### 2.3 — Logging: Retention

- **Definition:** How long log data is stored before deletion or archival, driven by a triangle of cost, compliance, and debugging need. Organizations tier: **hot** (fast, expensive, recent data for active troubleshooting), **warm** (cheaper, slower), and **cold/archive** (object storage like S3 or Glacier for long-term compliance at cents per GB). Compliance frameworks (PCI-DSS, HIPAA, SOC 2, GDPR) mandate minimum retention windows — often 1 year for audit logs — and some require **immutable** (write-once, delete-protected) retention and legal hold to prevent tampering.
- **Why it matters:** Storing every log forever is prohibitively expensive, so tier and right-size the window. Recognize the exam pattern: "we must keep security audit logs for 7 years and prove they were not altered" points to immutable/archive storage with legal hold, not a 30-day hot bucket. Retention is configured per log group/index and should be automated; plan log rotation on the source to avoid filling local disk. Retention applies to all observability data (logs, metrics, traces), but the strictest requirement usually governs security/audit logs. A missed forensic investigation caused by short retention is fixed by a longer, policy-driven plan.
- **Analogy:** Retention is deciding how long to keep bank records — recent ones in the desk drawer (hot), older ones in the office safe (warm), and 7-year tax records in the vault (cold/immutable).
- **Example:** A lifecycle policy moves logs hot → cold → deleted; security audit logs kept 1 year immutable in Glacier with legal hold; a scenario where short retention caused a missed forensic investigation is fixed by a longer, policy-driven retention plan.

### 2.4 — Alerting: Triage

- **Definition:** The disciplined process of evaluating an incoming alert to decide its validity, severity, and ownership before anyone spends effort. Begins with **classification** — page-worthy (wakes someone at 3 a.m.) vs. ticket-worthy (business hours). Uses severity levels (info/warning/critical) and routing rules to the correct on-call team via PagerDuty/Slack/email. Key distinction: an **alert** is actionable (something broke or will), while an **alarm** is a metric that crossed a threshold and may be benign. Triage also does dedup/correlation (five alerts from one root cause collapse into one incident) and uses runbooks to speed acknowledgment.
- **Why it matters:** Triage suppresses noise and surfaces signal — directly combating **alert fatigue**. Exam scenarios give five identical pages to five engineers and ask what is missing — the answer is triage (dedup/correlation). CompTIA lists triage as distinct from response: triage decides what the problem is and who owns it; response fixes it. Good triage metadata (service, region, runbook link) speeds resolution; triage is also where you decide whether to escalate.
- **Analogy:** Triage is the ER nurse who sorts arrivals by severity — the heart attack goes first, the paper cut waits — so doctors are not paged for every sniffle.
- **Example:** An alert classified ticket-worthy routes to the backend team's Slack during business hours instead of paging at 3 a.m.; five alerts from one root cause dedup into one incident; a runbook tells the responder what to check first, cutting mean-time-to-acknowledge.

### 2.5 — Alerting: Response

- **Definition:** The operational action taken after triage — acknowledge, investigate, mitigate, restore service, then document the post-incident review. Patterns include runbook execution, automated remediation (auto-rollback of a bad deploy, auto-scaling to relieve load, restart of a failed container), and escalation. A crucial distinction is **manual vs. automated** response: automation reduces mean-time-to-recover but must be guarded against false positives. Response is measured by **MTTR** (mean time to recover) and requires clear communication (status pages, incident bridges).
- **Why it matters:** Response turns an observation into a business outcome — reduced downtime. Exam scenarios pair "alerts fire but nobody acts" with a missing response process (no on-call, no runbook, no automation). After resolution, a blameless post-mortem asks why the alert fired and whether the threshold or runbook needs tuning. Response consumes the triage output: a well-triaged alert reaches the right responder with context, so response is fast. Both triage and response are part of alerting, separate from monitoring, which merely detects.
- **Analogy:** Response is the firefighter who actually puts out the fire after dispatch (triage) decided it was real and routed it — monitoring just installed the smoke alarm.
- **Example:** A self-healing Auto Scaling Group replaces a bad instance automatically; a Lambda clears a full disk; after resolution, a blameless post-mortem asks why the alert fired and whether to tune the threshold. Chat-ops integration logs the entire conversation for later audit.

### 2.6 — Tracing

- **Definition:** Distributed tracing follows a single user request as it travels through microservices, queues, and databases, painting a timeline ("trace") of every hop with its latency and status. A trace is composed of **spans** (one unit of work — an HTTP call, a DB query) nested in a tree under a single trace ID. The industry standard is OpenTelemetry/W3C Trace Context using `traceparent` headers so spans link across boundaries automatically. **Sampling** (head- or tail-based) keeps representative data cheap. Instrumentation can be manual (code changes) or automatic (agent/sidecar).
- **Why it matters:** Tracing answers "WHERE is the latency or error?" while logs answer "WHY." A request touching ten services cannot be pinpointed with metrics or logs alone. Exam scenarios contrast tracing with logging/monitoring and ask which to enable for a slow checkout flow across microservices — the answer is distributed tracing. Tracing also reveals service dependencies (a service map) and cascading failures. Pitfall: if a service drops the trace header, the trace breaks. Tracing integrates with metrics and logs via the trace ID, letting you jump from a slow span to the exact log lines.
- **Analogy:** Tracing is a GPS breadcrumb trail for one customer's request — you watch the exact path it took and see which single stop ate 80% of the time, instead of guessing which of twelve rooms is slow.
- **Example:** A customer's slow dashboard load is traced to 80% of the time in one downstream reporting service's DB query; OpenTelemetry propagates `traceparent` so spans link across services; tail-based sampling keeps only representative traces to control cost.

### 2.7 — Monitoring: Metrics

- **Definition:** The continuous, automated collection and visualization of **metrics** — numeric time-series measurements that describe system behavior (CPU %, memory, request count, error rate, latency percentiles). The four golden signals are **latency, traffic, errors, and saturation**. A metric has a name, labels/dimensions (e.g., region=us-east-1), a value, and a timestamp. Types: **counter** (only increases), **gauge** (up/down), **histogram** (distribution → percentiles), **summary**. Concepts include baselines, static thresholds, and anomaly detection (dynamic baselines).
- **Why it matters:** Metrics are cheap to store and fast to query — the first line of observability, answering "is the system healthy right now?" at a glance (unlike logs, which are event records). Exam tests knowing when metrics suffice (capacity planning, SLA dashboards) versus when you need logs/traces (root cause). Metrics feed capacity planning and auto-scaling. A dashboard is the presentation layer — avoid vanity dashboards that show no actionable signal. Effective monitoring is proactive: it shows trends before they become incidents.
- **Analogy:** Monitoring metrics are the car's dashboard gauges — speed, fuel, temperature at a glance tell you something is wrong before the engine dies; they tell you WHAT, not WHY.
- **Example:** A dashboard shows a memory-leak trend via anomaly detection against the weekly baseline; a histogram yields p95/p99 latency; a counter tracks requests served; a gauge shows current CPU%; CloudWatch target tracking scales capacity on a metric before users notice.

---

## SECTION 3 — REAL WORLD CLOUD EXAMPLES

- **Scenario 1 — E-commerce Black Friday surge (Logging + Alerting):** A retail site ships application and access logs from every web and API server to a centralized log service. A spike in 5xx errors triggers a critical alert that pages the on-call engineer, who uses aggregated logs to find a failing payment microservice and rolls back the last deploy within minutes. Retention keeps 14 months of audit logs in cold storage for PCI-DSS compliance.
- **Scenario 2 — Multi-region SaaS latency investigation (Tracing):** A customer reports slow dashboard loads. The team opens a distributed trace for one slow request and sees 80% of the time spent in a single downstream reporting service's database query, not the frontend. They fix one query instead of guessing across twelve services.
- **Scenario 3 — Kubernetes cluster health (Monitoring):** A platform team monitors CPU saturation, pod restart counts, and request latency per namespace. A dashboard shows a memory leak trending toward the limit; they scale and patch before users notice. Anomaly detection flags the deviation from the weekly baseline.
- **Scenario 4 — Security forensics after a breach (Retention + Aggregation):** An attacker accessed an account; because all auth events were aggregated with immutable 1-year retention, the incident team reconstructs the exact timeline and proves which logs were untouched, satisfying the auditor.
- **Scenario 5 — Serverless API (Metrics + Triage):** A Lambda-based API monitors invocation count, duration, and error rate. A throttle alert is triaged as ticket-worthy (no user impact yet) and routed to the backend team's Slack, avoiding unnecessary 3 a.m. pages and reducing alert fatigue.

---

## SECTION 4 — COMPARISON TABLES

| Pillar | Answers the question | Data type | Best for | Cost to store |
|---|---|---|---|---|
| Monitoring (Metrics) | WHAT is the system doing? | Numeric time series | Health, capacity, SLA | Low |
| Logging | WHY did it happen? | Event records (text/JSON) | Root cause, audit | Medium |
| Tracing | WHERE is the bottleneck? | Spans / request tree | Distributed latency | Medium-High |
| Alerting | WHEN must a human act? | Notifications | Incident response | Low (process) |

| Logging sub-area | Purpose | Configuration knob | Exam keyword |
|---|---|---|---|
| Collection | Capture from sources | Agent vs agentless, structured logs | stdout, syslog, Event Log |
| Aggregation | Centralize & correlate | Pipeline, schema, indexing | single pane of glass |
| Retention | How long to keep | Hot/warm/cold tiers, legal hold | compliance, immutable |

| Alerting stage | Goal | Output | Failure mode |
|---|---|---|---|
| Triage | Validate, classify, route | Severity, owner, dedup | Alert fatigue |
| Response | Mitigate & recover | Runbook, automation, MTTR | Unacted alerts |

| Metric type | Behavior | Example | Use |
|---|---|---|---|
| Counter | Only increases | Requests served | Rate, totals |
| Gauge | Up or down | CPU %, memory | Current state |
| Histogram | Distribution | Request latency | Percentiles (p95/p99) |

| Retention tier | Speed | Cost | Typical use |
|---|---|---|---|
| Hot | Fast | High | Active troubleshooting (<30d) |
| Warm | Medium | Medium | Recent analysis |
| Cold/Archive | Slow | Low | Compliance (years) |

---

## SECTION 5 — AWS PROVIDER MAPPING

AWS-ONLY mapping for Objective 3.1 (observability):

- **Amazon CloudWatch** — the unified monitoring and logging service. CloudWatch **Metrics** collects numeric time-series data from EC2, Lambda, RDS, and custom sources; CloudWatch **Logs** (log groups/streams) handles log collection, aggregation, and retention (configurable per log group, with log-class analytics and export to S3 for cold archival); CloudWatch **Alarms** provide alerting with triage (severity via alarm state) and response (SNS notification, auto-scaling action, or Lambda). CloudWatch **Dashboards** present metrics. This single service covers the Monitoring, Logging, and Alerting pillars.
- **AWS CloudTrail** — the audit logging service. It records every API call and account activity (who did what, from where, when) as management-event logs. CloudTrail is the AWS answer for compliance-grade, immutable audit log **collection and retention** (with organization trails and S3 + Glacier archival, plus log file integrity validation). It maps directly to the Logging/Retention sub-objectives for security audit.
- **AWS X-Ray** — the distributed **tracing** service. X-Ray collects traces and spans from applications (via the X-Ray SDK or ADOT/OpenTelemetry sidecar), builds a service map, and shows latency per segment so you can pinpoint where a request is slow or failing. It maps to the Tracing sub-objective and complements CloudWatch metrics/logs by answering "where."

Together: Monitor with CloudWatch Metrics, log with CloudWatch Logs + CloudTrail, trace with X-Ray, and alert with CloudWatch Alarms → SNS.

---

## SECTION 6 — PRACTICE QUESTIONS

1. Which observability pillar answers "WHERE is the latency in a multi-service request?"
    A. Monitoring
    B. Logging
    C. Tracing
    D. Alerting

> [!note]- Reveal Answer
> **Correct: C**
> **Why correct:** Distributed tracing follows a single request across services, painting a timeline of spans to pinpoint the slow/failing hop.
> **Why A is wrong:** Monitoring metrics show WHAT is wrong system-wide, not WHERE inside a request path.
> **Why B is wrong:** Logging tells you WHY an event happened, but not the latency path across services.
> **Why D is wrong:** Alerting tells you WHEN a human must act, not where latency lives.

2. A company must keep security audit logs for 7 years and prove they were unaltered. Which approach fits best?
    A. 30-day hot log bucket
    B. Immutable cold storage with legal hold
    C. Real-time streaming only
    D. Local syslog on each host

> [!note]- Reveal Answer
> **Correct: B**
> **Why correct:** Immutable/archive (WORM) retention with legal hold satisfies long-term, tamper-proof compliance mandates like 7-year audit windows.
> **Why A is wrong:** A 30-day hot bucket is far too short and is mutable, so it neither meets retention nor proves non-alteration.
> **Why C is wrong:** Streaming is a delivery mechanism, not a retention/integrity control.
> **Why D is wrong:** Local syslog is scattered, mutable, and offers no centralized compliance retention or proof of integrity.

3. What is the primary purpose of log aggregation?
    A. Reduce the number of log sources
    B. Centralize logs for cross-system correlation
    C. Delete old logs automatically
    D. Encrypt logs at rest

> [!note]- Reveal Answer
> **Correct: B**
> **Why correct:** Aggregation brings scattered logs from many sources into one queryable store so you can correlate events across the fleet.
> **Why A is wrong:** Aggregation does not reduce sources; it collects from all of them into a single pipeline.
> **Why C is wrong:** Deleting old logs is a retention/lifecycle function, not aggregation.
> **Why D is wrong:** Encryption is a protection control, separate from centralizing and correlating logs.

4. The four golden signals of monitoring are latency, traffic, errors, and:
    A. Availability
    B. Saturation
    C. Tracing
    D. Logging

> [!note]- Reveal Answer
> **Correct: B**
> **Why correct:** Saturation (how "full" a resource is) is the fourth golden signal, completing latency/traffic/errors/saturation.
> **Why A is wrong:** Availability is a derived SLA outcome, not one of the four golden signals.
> **Why C is wrong:** Tracing is a separate observability pillar, not a monitoring golden signal.
> **Why D is wrong:** Logging is a separate pillar (WHY), not a golden-signal metric.

5. An alert fires but five identical pages go to five engineers. Which process is missing?
    A. Collection
    B. Retention
    C. Triage (dedup/correlation)
    D. Tracing

> [!note]- Reveal Answer
> **Correct: C**
> **Why correct:** Triage dedups/correlates related alerts so one root cause collapses into a single routed incident, preventing page storms.
> **Why A is wrong:** Collection captures logs/metrics; it does nothing to consolidate duplicate alerts.
> **Why B is wrong:** Retention governs how long data is kept, not alert routing or dedup.
> **Why D is wrong:** Tracing locates latency across services; it does not manage alert pages.

6. Which metric type only increases and is used for totals/rates?
    A. Gauge
    B. Histogram
    C. Counter
    D. Summary

> [!note]- Reveal Answer
> **Correct: C**
> **Why correct:** A counter monotonically increases (e.g., requests served), making it ideal for totals and rates.
> **Why A is wrong:** A gauge goes up and down and reflects current state, not cumulative totals.
> **Why B is wrong:** A histogram captures a distribution to derive percentiles, not a single increasing total.
> **Why D is wrong:** A summary computes quantiles in-stream; it is not a monotonically increasing counter.

7. In AWS, which service provides distributed tracing?
    A. CloudWatch
    B. CloudTrail
    C. X-Ray
    D. SNS

> [!note]- Reveal Answer
> **Correct: C**
> **Why correct:** AWS X-Ray is the distributed tracing service — it collects traces/spans and builds a service map with per-segment latency.
> **Why A is wrong:** CloudWatch handles metrics, logs, and alarms, not distributed request tracing.
> **Why B is wrong:** CloudTrail records API/audit activity, not request latency paths.
> **Why D is wrong:** SNS is a notification/pub-sub service used by alarms, not a tracing tool.

8. Which AWS service records API calls for audit/compliance?
    A. CloudWatch Metrics
    B. CloudTrail
    C. X-Ray
    D. Lambda

> [!note]- Reveal Answer
> **Correct: B**
> **Why correct:** CloudTrail logs every API call (who did what, from where, when) as immutable management-event audit records.
> **Why A is wrong:** CloudWatch Metrics stores numeric time series, not API audit logs.
> **Why C is wrong:** X-Ray traces requests; it does not record account-level API activity.
> **Why D is wrong:** Lambda is a compute service, not an audit-logging service.

9. "Alert fatigue" is best mitigated by:
    A. Collecting more logs
    B. Tuning thresholds and adding suppression
    C. Disabling all alarms
    D. Increasing retention

> [!note]- Reveal Answer
> **Correct: B**
> **Why correct:** Tuning thresholds and adding suppression/dedup keeps only actionable pages, directly reducing alert fatigue.
> **Why A is wrong:** More logs add signal to search, but do not reduce the volume of noisy alerts.
> **Why C is wrong:** Disabling all alarms removes protection and hides real incidents.
> **Why D is wrong:** Retention length has no effect on alert volume or fatigue.

10. Structured logging is preferred because it is:
    A. Smaller in size only
    B. Machine-parseable and queryable
    C. Required by all clouds
    D. Always encrypted

> [!note]- Reveal Answer
> **Correct: B**
> **Why correct:** Consistent fields (e.g., JSON) let agents and backends query, filter, and correlate logs at scale.
> **Why A is wrong:** Structured logs are not necessarily smaller; readability/queryability is the point, not size.
> **Why C is wrong:** No cloud mandates structured logs; it is a best practice, not a requirement.
> **Why D is wrong:** Encryption is an independent control applied to logs at rest/in transit, not inherent to being structured.

11. A gauge metric is best for reporting:
    A. Total requests ever served
    B. Current CPU utilization
    C. Request latency distribution
    D. Number of errors per hour

> [!note]- Reveal Answer
> **Correct: B**
> **Why correct:** A gauge rises and falls and represents current state, so it fits instantaneous values like CPU%.
> **Why A is wrong:** Total requests ever served is cumulative and only increases → a counter.
> **Why C is wrong:** A latency distribution is a histogram's job, not a gauge.
> **Why D is wrong:** Errors per hour is a rate derived from a counter, not a point-in-time gauge.

12. Hot/warm/cold storage tiers relate to which sub-objective?
    A. Tracing
    B. Retention
    C. Triage
    D. Monitoring

> [!note]- Reveal Answer
> **Correct: B**
> **Why correct:** Retention tiers (hot/warm/cold/archive) balance cost vs. access speed vs. compliance over the data's life.
> **Why A is wrong:** Tracing has no storage-tier concept; it is about request spans.
> **Why C is wrong:** Triage is an alerting stage, unrelated to log storage tiers.
> **Why D is wrong:** Monitoring is about live metrics, not how long logs are kept.

13. After triage decides an alert is critical and routes it, the next step is:
    A. Collection
    B. Aggregation
    C. Response (mitigate/recover)
    D. Retention

> [!note]- Reveal Answer
> **Correct: C**
> **Why correct:** Response (acknowledge, mitigate, restore, document) is the operational action taken after a triaged/route alert.
> **Why A is wrong:** Collection happens upstream, feeding the metrics/logs that triggered the alert.
> **Why B is wrong:** Aggregation centralizes data; it is not the action on a routed alert.
> **Why D is wrong:** Retention governs data lifespan, not incident action.

14. OpenTelemetry trace context headers enable:
    A. Log rotation
    B. Span linkage across services
    C. Metric aggregation
    D. Retention policies

> [!note]- Reveal Answer
> **Correct: B**
> **Why correct:** Propagating trace context (e.g., traceparent) links child spans across service boundaries into one trace.
> **Why A is wrong:** Log rotation is a retention/cleanup task, unrelated to trace headers.
> **Why C is wrong:** Metric aggregation is a monitoring concern, not driven by trace context.
> **Why D is wrong:** Retention policies are lifecycle rules, not set by trace headers.

15. Which is NOT a logging sub-area in this objective?
    A. Collection
    B. Aggregation
    C. Retention
    D. Saturation

> [!note]- Reveal Answer
> **Correct: D**
> **Why correct:** Saturation is one of the four golden monitoring signals, not a logging sub-area (collection/aggregation/retention).
> **Why A is wrong:** Collection is the first logging sub-area (capturing from sources).
> **Why B is wrong:** Aggregation is the logging sub-area that centralizes logs.
> **Why C is wrong:** Retention is the logging sub-area governing how long logs are kept.

16. CloudWatch Alarms primarily support which pillar?
    A. Tracing
    B. Alerting
    C. Collection
    D. Tracing

> [!note]- Reveal Answer
> **Correct: B**
> **Why correct:** CloudWatch Alarms evaluate metrics and fire notifications/actions (SNS, auto-scale, Lambda) — that is alerting.
> **Why A is wrong:** Tracing is provided by X-Ray, not by CloudWatch Alarms.
> **Why C is wrong:** Collection is about gathering logs/metrics, not evaluating thresholds to notify.
> **Why D is wrong:** Tracing is provided by X-Ray, not CloudWatch Alarms (and this option duplicates A).

17. A histogram metric is most useful for calculating:
    A. Current memory
    B. p95/p99 latency percentiles
    C. Total uptime
    D. Error counts

> [!note]- Reveal Answer
> **Correct: B**
> **Why correct:** A histogram records a distribution of values, enabling percentile calculations like p95/p99 latency.
> **Why A is wrong:** Current memory is a point-in-time gauge value.
> **Why B is wrong:** Total uptime is a duration/derived metric, not a histogram output.
> **Why D is wrong:** Error counts are cumulative and better modeled as a counter.

18. Immutable retention helps with:
    A. Faster queries
    B. Proving logs were not tampered
    C. Lower cost than hot storage always
    D. Real-time alerting

> [!note]- Reveal Answer
> **Correct: B**
> **Why correct:** Write-once/read-many (WORM) retention prevents deletion/edits, supporting forensic integrity and audit proof.
> **Why A is wrong:** Immutable cold storage is typically slower to query, not faster.
> **Why C is wrong:** Cold/immutable storage is usually cheaper than hot, but "always" is too strong and cost is not its purpose.
> **Why D is wrong:** Alerting is driven by live metrics/alarms, not by retention mode.

19. Triage classifies an alert as "ticket-worthy" rather than "page-worthy" to:
    A. Delete the alert
    B. Route non-urgent issues to business-hours handling
    C. Disable monitoring
    D. Increase retention

> [!note]- Reveal Answer
> **Correct: B**
> **Why correct:** Severity routing sends non-urgent (ticket-worthy) alerts to business-hours queues, avoiding unnecessary 3 a.m. pages.
> **Why A is wrong:** Classification routes the alert; it does not delete it.
> **Why C is wrong:** Monitoring stays on; only the routing/urgency changes.
> **Why D is wrong:** Retention length is unrelated to alert severity classification.

20. The simplest observability mantra: metrics tell you WHAT, logs tell you WHY, traces tell you WHERE, and alerts tell you:
    A. WHO
    B. WHEN to act
    C. HOW much it costs
    D. WHICH region

> [!note]- Reveal Answer
> **Correct: B**
> **Why correct:** Alerts tell you WHEN a human must act — the trigger for triage and response.
> **Why A is wrong:** Ownership/routing is part of triage, but the core alert purpose is timing of action.
> **Why C is wrong:** Cost is a capacity/finance concern, not the alert's role.
> **Why D is wrong:** Region is a metric/log dimension, not what alerts communicate.
