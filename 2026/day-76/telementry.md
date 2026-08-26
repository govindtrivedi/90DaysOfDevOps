Day 76 completes the **three pillars of observability** and introduces **alerting**.

You now have:

```text
Day 73 → Metrics
Day 75 → Logs
Day 76 → Traces + Alerting
```

The most important concept today is that **OpenTelemetry is not another storage backend**. It is the framework/collection layer that receives telemetry and sends it to systems that store or visualize it.

---

# 1. The complete observability picture

Your stack now looks like this:

```text
                         ┌─────────────────┐
                         │     Grafana     │
                         │   :3000         │
                         └───────┬─────────┘
                                 │
             ┌───────────────────┼───────────────────┐
             │                   │                   │
             ▼                   ▼                   ▼
        Prometheus              Loki             Future
        Metrics DB             Logs DB          Trace Backend
             ▲                   ▲                   ▲
             │                   │                   │
       ┌─────┼─────┐             │             OTEL Collector
       │     │     │             │                   ▲
       │     │     │             │                   │
       ▼     ▼     ▼             │                  OTLP
     Node  cAdvisor  OTEL        │                   │
   Exporter          Collector   │                Your App
                         │       │
                         │       │
Docker Containers ───────┴───────┘
        │
        └── Promtail ───────────→ Loki
```

And now:

```text
Metrics → "What is wrong?"
Logs    → "Why did it happen?"
Traces  → "Where did the request spend its time?"
Alerts  → "Tell me when I need to act."
```

That last piece is important. Without alerting, you'd still need to constantly watch Grafana.

---

# 2. What is OpenTelemetry?

The assignment defines OpenTelemetry as a:

> vendor-neutral, open-source framework for generating, collecting, and exporting telemetry data.

That telemetry includes:

```text
Metrics
Logs
Traces
```

The key distinction:

```text
OpenTelemetry ≠ database
OpenTelemetry ≠ dashboard
OpenTelemetry ≠ trace storage system
```

Instead:

```text
Application
     │
     ▼
OpenTelemetry
     │
     ├──→ Prometheus
     ├──→ Loki
     ├──→ Jaeger
     ├──→ Tempo
     └──→ other backends
```

This vendor-neutral approach is one of the major reasons OpenTelemetry is important in modern observability architectures.

---

# 3. The OTEL Collector

The Collector is the service you'll deploy today.

Think of it as a telemetry pipeline:

```text
                 OTEL Collector
                       │
        ┌──────────────┼──────────────┐
        │              │              │
        ▼              ▼              ▼
    Receivers      Processors      Exporters
        │              │              │
      receive        modify          send
```

### Receivers

Receivers accept telemetry.

Your configuration uses:

```yaml
receivers:
  otlp:
    protocols:
      grpc:
        endpoint: 0.0.0.0:4317
      http:
        endpoint: 0.0.0.0:4318
```

Therefore:

```text
4317 → OTLP gRPC
4318 → OTLP HTTP
```

### Processors

You have:

```yaml
processors:
  batch:
```

The batch processor groups telemetry before exporting it.

Conceptually:

```text
100 individual telemetry items
          ↓
       batching
          ↓
fewer export operations
```

This reduces overhead.

### Exporters

Your configuration has:

```yaml
exporters:
  prometheus:
    endpoint: "0.0.0.0:8889"

  debug:
    verbosity: detailed
```

So:

```text
Metrics → Prometheus exporter
Traces  → Debug exporter
Logs    → Debug exporter
```

For this learning environment, traces aren't being stored yet. They're printed by the Collector.

---

# 4. Understand the three pipelines

Your configuration says:

```yaml
service:
  pipelines:
    metrics:
      receivers: [otlp]
      processors: [batch]
      exporters: [prometheus]

    traces:
      receivers: [otlp]
      processors: [batch]
      exporters: [debug]

    logs:
      receivers: [otlp]
      processors: [batch]
      exporters: [debug]
```

Read this as:

### Metrics

```text
OTLP
 ↓
batch
 ↓
Prometheus exporter
```

### Traces

```text
OTLP
 ↓
batch
 ↓
debug output
```

### Logs

```text
OTLP
 ↓
batch
 ↓
debug output
```

This is a very useful mental model for understanding Collector configuration.

---

# 5. OTLP

**OTLP = OpenTelemetry Protocol.**

Your Collector accepts OTLP through:

```text
4317 → gRPC
4318 → HTTP
```

The assignment uses HTTP because you can easily test it with `curl`.

In a real application, you generally won't manually construct these large JSON payloads.

Instead:

```text
Python / Java / Go / Node.js application
                ↓
          OTEL SDK
                ↓
              OTLP
                ↓
       OTEL Collector
```

The SDK handles serialization and telemetry generation for you.

---

# 6. Distributed tracing

A trace represents one request moving through a distributed system.

For example:

```text
User
 │
 ▼
API Gateway
 │ span 1
 ▼
Auth Service
 │ span 2
 ▼
Payment Service
 │ span 3
 ▼
Database
 │ span 4
```

The entire journey is the:

```text
TRACE
```

Each individual operation is a:

```text
SPAN
```

A span contains information such as:

```text
Trace ID
Span ID
Parent Span ID
Start time
Duration
Attributes
```

This allows you to reconstruct:

> Where did this request go, and where did it spend its time?

---

# 7. Send your first trace

Your supplied `curl` request sends:

```text
test-span
```

to:

```text
http://localhost:4318/v1/traces
```

The important journey is:

```text
curl
 │
 │ OTLP/HTTP
 ▼
OTEL Collector
 │
 │ traces pipeline
 ▼
batch processor
 │
 ▼
debug exporter
 │
 ▼
docker logs otel-collector
```

Then:

```bash
docker logs otel-collector 2>&1 | grep -A 10 "test-span"
```

should show the span information.

This is a great demonstration because you can actually see telemetry entering and moving through the Collector.

---

# 8. OTEL → Prometheus

The metrics exercise demonstrates another important architecture.

You send:

```text
test_requests_total
```

through OTLP.

The path is:

```text
curl
  ↓
OTLP HTTP
  ↓
OTEL Collector :4318
  ↓
OTLP receiver
  ↓
batch processor
  ↓
Prometheus exporter :8889
  ↓
Prometheus scrapes :8889
  ↓
Prometheus database
```

Then:

```promql
test_requests_total
```

retrieves the metric.

This is a particularly useful concept to remember:

> **The OTEL Collector doesn't replace Prometheus here. It bridges OTLP telemetry into a Prometheus-compatible metrics endpoint.**

---

# 9. Prometheus alerting

Now you move from:

```text
collect → store → visualize
```

to:

```text
collect → store → evaluate → alert
```

Your `alert-rules.yml` contains five conditions.

---

## High CPU

```yaml
- alert: HighCPUUsage
  expr: 100 - (avg(rate(node_cpu_seconds_total{mode="idle"}[5m])) * 100) > 80
  for: 2m
```

Meaning:

> Fire when average CPU utilization remains above 80% for two minutes.

---

## High memory

```yaml
expr: (1 - node_memory_MemAvailable_bytes / node_memory_MemTotal_bytes) * 100 > 85
```

Meaning:

> Fire when memory usage exceeds 85% for two minutes.

---

## Container down

```yaml
expr: absent(container_last_seen{name="notes-app"})
```

This is a particularly useful PromQL function.

`absent()` essentially asks:

> Does this time series exist?

If the expected series disappears, the alert can fire.

---

## Target down

```yaml
expr: up == 0
```

This uses the `up` metric you learned on Day 73.

Remember:

```text
up == 1 → scrape successful
up == 0 → scrape failed
```

So:

```promql
up == 0
```

is a simple way of detecting an unreachable scrape target.

---

## Disk usage

```yaml
expr: (1 - node_filesystem_avail_bytes{mountpoint="/"} / node_filesystem_size_bytes{mountpoint="/"}) * 100 > 90
```

Meaning:

> Alert when the root filesystem exceeds 90% utilization for five minutes.

---

# 10. What does `for:` mean?

This is one of the most important alerting concepts.

Consider:

```yaml
for: 2m
```

It doesn't mean:

> Alert two minutes after evaluating the expression.

It means:

> The alert condition must remain true continuously for two minutes before the alert fires.

The lifecycle is approximately:

```text
Condition false
      ↓
   INACTIVE
      │
      │ condition becomes true
      ▼
   PENDING
      │
      │ remains true for 2 minutes
      ▼
   FIRING
```

Why?

Imagine CPU does this:

```text
CPU
100% ┤       ╭╮
 80% ┤───────╯╰────
     └──────────────
```

A brief spike shouldn't necessarily wake someone up.

The `for` period helps prevent alert flapping caused by short-lived spikes.

---

# 11. Test the alert

The assignment has you stop the application:

```bash
docker compose stop notes-app
```

Then wait around 1–2 minutes.

Prometheus should detect that the target is no longer available.

Check:

```text
Prometheus
   ↓
Alerts
   ↓
TargetDown
```

You should see the transition:

```text
INACTIVE → PENDING → FIRING
```

Then restore it:

```bash
docker compose start notes-app
```

This is a very important practical exercise because you're deliberately creating an incident and observing the monitoring system detect it.

---

# 12. Prometheus alerts vs Grafana alerts

The assignment specifically asks you to understand this distinction.

### Prometheus alerting

Prometheus evaluates PromQL-based alert rules:

```text
Metrics
   ↓
Prometheus
   ↓
PromQL rule
   ↓
Alert
```

It's particularly natural when the alert is tightly coupled to Prometheus metrics.

Examples:

```text
High CPU
High memory
Target down
Disk usage
```

### Grafana alerting

Grafana can evaluate queries and manage alerting/notification workflows from the Grafana alerting system.

The assignment has you create:

```text
High Container Memory
```

based on:

```promql
container_memory_usage_bytes{name="notes-app"} / 1024 / 1024
```

with:

```text
threshold → 100 MB
evaluation → every 1m
for → 2m
```

So the conceptual difference is:

```text
Prometheus Alerting
       ↓
Prometheus-centric rules


Grafana Alerting
       ↓
Centralized visualization + alerting
       ↓
Contact points
       ↓
Notification policies
```

For this exercise, Grafana is also the easier place to demonstrate notifications.

---

# 13. Contact points and notification policies

These are two different concepts.

### Contact point

A contact point answers:

> **Who/where should the notification go?**

Examples:

```text
Email
Slack
PagerDuty
```

The assignment creates:

```text
DevOps Team
```

### Notification policy

A notification policy answers:

> **Which alerts should go to which contact point?**

For example:

```text
severity=critical
        ↓
Critical Team

severity=warning
        ↓
DevOps Team
```

This becomes very powerful as your environment grows.

---

# 14. Full architecture

Your final Day 76 architecture can be documented like this:

```text
                         ┌────────────────────┐
                         │      Grafana       │
                         │      :3000         │
                         └─────────┬──────────┘
                                   │
             ┌─────────────────────┼─────────────────────┐
             │                     │                     │
             ▼                     ▼                     ▼
        Prometheus               Loki              Alerting
        :9090                    :3100                  │
             ▲                     ▲                     │
             │                     │                     ▼
       ┌─────┼─────┐               │              Notifications
       │     │     │               │
       │     │     │               │
       ▼     ▼     ▼               │
      Node cAdvisor OTEL            │
    Exporter         Collector      │
                    :8889           │
                      ▲             │
                      │             │
                 OTLP :4317/4318    │
                      ▲             │
                      │             │
                    App/OTLP        │
                                    │
Docker Containers ──→ Promtail ──→ Loki
```

Or separated by pillar:

```text
METRICS
────────
Node Exporter ──┐
cAdvisor ──────┼──→ Prometheus ──→ Grafana
OTEL Collector ┘                    │
                                    └→ Alerts


LOGS
────
Docker → Promtail → Loki → Grafana


TRACES
──────
Application
    ↓
OTLP
    ↓
OTEL Collector
    ↓
Debug / future Jaeger or Tempo
```

---

# 15. Your final service list

By the end of Day 76, your Compose stack should contain:

| Service        |             Port | Purpose                              |
| -------------- | ---------------: | ------------------------------------ |
| Prometheus     |           `9090` | Metrics storage/querying/alert rules |
| Node Exporter  |           `9100` | Host metrics                         |
| cAdvisor       |           `8080` | Container metrics                    |
| Grafana        |           `3000` | Dashboards + alerting                |
| Loki           |           `3100` | Log storage                          |
| Promtail       |           `9080` | Log collection                       |
| OTEL Collector | `4317/4318/8889` | Telemetry collection/export          |
| Notes App      |           `8000` | Sample application                   |

Verify:

```bash
docker compose ps
```

Your goal is to see all eight services running.

---

# 16. The most important interview answers

### What is OpenTelemetry?

> OpenTelemetry is a vendor-neutral, open-source framework for generating, collecting, and exporting telemetry such as metrics, logs, and traces. It isn't a backend itself; it sends telemetry to systems such as Prometheus, Loki, Jaeger, or other observability platforms.

### What is the OTEL Collector?

> The OpenTelemetry Collector is a standalone telemetry pipeline that receives data through receivers, processes it using processors, and sends it to backends through exporters.

### What are receivers, processors, and exporters?

> Receivers accept telemetry, processors transform or batch it, and exporters send the processed telemetry to a backend.

```text
Receiver → Processor → Exporter
```

### What is OTLP?

> OTLP is the OpenTelemetry Protocol used to transport telemetry between applications, collectors, and other components. It supports gRPC on port 4317 and HTTP on port 4318.

### What is a trace?

> A trace represents the complete journey of a request through a distributed system. Each individual operation is represented by a span, and spans are connected through parent-child relationships.

### Why use `for: 2m` in an alert?

> It requires the alert condition to remain continuously true for two minutes before firing, which prevents transient spikes from generating unnecessary alerts.

### Prometheus alerting vs Grafana alerting?

> Prometheus alerting is tightly integrated with PromQL and Prometheus metrics. Grafana alerting provides a centralized alerting layer integrated with dashboards, queries, contact points, and notification policies.

---

## The big picture after Day 76

You have now progressed from simply running infrastructure to building an actual observability platform:

```text
                  ┌──────────────┐
                  │   Grafana    │
                  └──────┬───────┘
                         │
             ┌───────────┼───────────┐
             │           │           │
             ▼           ▼           ▼
          Metrics       Logs       Traces
             │           │           │
         Prometheus     Loki      OTEL Collector
             │           │           │
             └───────────┴───────────┘
                         │
                    Alerting
                         │
                         ▼
                   Notification
```

The progression across Days 73–76 is therefore:

```text
Day 73
Collect metrics
       ↓
Day 74
Monitor infrastructure + visualize
       ↓
Day 75
Collect and investigate logs
       ↓
Day 76
Trace requests + automatically detect problems
```

**The real DevOps lesson:** dashboards are useful, but a production observability system should ultimately answer four questions:

> **What is broken? → Why is it broken? → Where is it broken? → Who needs to know?**

Metrics, logs, traces, and alerting give you those four capabilities.
