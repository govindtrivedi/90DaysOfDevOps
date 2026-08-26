Day 73 is the start of the **Observability block**. Unlike Days 68–72, where the focus was on building and deploying infrastructure, today’s focus is answering: **“How do I know the system is healthy, and why did it fail?”**

The assignment expects you to understand observability, run Prometheus in Docker, configure scrape targets, practice PromQL, and document the setup. The final project will eventually expand into metrics, logs, and traces over Days 73–77.

### 1. Core concept: Monitoring vs Observability

The simplest way to remember it:

| Monitoring                            | Observability                               |
| ------------------------------------- | ------------------------------------------- |
| Tells you **when** something is wrong | Helps determine **why** it is wrong         |
| Alerts and thresholds                 | Exploration, querying, correlation          |
| “CPU is above 90%”                    | “Why is CPU above 90%, and what caused it?” |

The three pillars are:

* **Metrics** → numerical measurements over time.
* **Logs** → timestamped records of events.
* **Traces** → the journey of an individual request across services.

The assignment's mental model is:

```text
Metrics  → What is broken?
Logs     → Why did it break?
Traces   → Where did it break?
```

For example:

```text
Metric:
HTTP 500 errors increased
        ↓
Log:
Database connection timeout
        ↓
Trace:
API → User Service → Database
                     ↑
                  12 seconds
```

That distinction is the most important theoretical takeaway from Day 73.

---

## 2. Architecture you're starting to build

The assignment gives you this longer-term architecture:

```text
                         ┌───────────────┐
                         │    Grafana    │
                         └───────┬───────┘
                                 │
              ┌──────────────────┼──────────────────┐
              │                  │                  │
              ▼                  ▼                  ▼
         Prometheus             Loki          OTEL Collector
              ▲                  ▲                  ▲
              │                  │                  │
          Metrics              Logs              Traces
              ▲                  ▲                  ▲
              │                  │                  │
          Your App           Promtail            Your App
              │
              │
       ┌──────┴──────┐
       │             │
       ▼             ▼
 Node Exporter     cAdvisor
       │             │
       └──────┬──────┘
              ▼
          Prometheus
```

Day 73 primarily establishes **Prometheus + metrics**. The assignment explicitly describes this as the observability stack you'll continue building over the next five days.

---

# 3. Build the Prometheus environment

Create:

```bash
mkdir observability-stack
cd observability-stack
```

Your initial structure can be:

```text
observability-stack/
├── prometheus.yml
└── docker-compose.yml
```

Your Prometheus configuration is:

```yaml
global:
  scrape_interval: 15s
  evaluation_interval: 15s

scrape_configs:
  - job_name: "prometheus"
    static_configs:
      - targets: ["localhost:9090"]
```

The important concept is **scraping**.

Prometheus doesn't normally wait for applications to push metrics to it. Instead:

```text
Prometheus
    │
    │ GET /metrics
    ▼
Target
```

Every 15 seconds, Prometheus asks the configured target for its metrics.

---

# 4. Run Prometheus with Docker

The Compose setup from the assignment is:

```yaml
services:
  prometheus:
    image: prom/prometheus:latest
    container_name: prometheus
    ports:
      - "9090:9090"
    volumes:
      - ./prometheus.yml:/etc/prometheus/prometheus.yml
      - prometheus_data:/prometheus
    command:
      - '--config.file=/etc/prometheus/prometheus.yml'
    restart: unless-stopped

volumes:
  prometheus_data:
```

Start it:

```bash
docker compose up -d
```

Then open:

```text
http://localhost:9090
```

Go to:

```text
Status → Targets
```

You should see:

```text
prometheus    UP
```

### Important Docker networking detail

When you later add another container, don't blindly use `localhost` for that container.

Inside the Prometheus container:

```text
localhost = Prometheus container itself
```

So the sample application's target is:

```yaml
targets: ["notes-app:8000"]
```

because Docker Compose provides service-name-based networking.

---

# 5. Prometheus metric types

You need to understand four types.

### Counter

A counter represents something that generally **only increases**.

Example:

```text
total HTTP requests
total errors
total processed jobs
```

Conceptually:

```text
100 → 150 → 250 → 400
```

If the process restarts, the counter can reset.

### Gauge

A gauge represents a value that can **increase or decrease**.

Examples:

```text
CPU temperature
memory currently used
active connections
```

Conceptually:

```text
40 → 65 → 51 → 72 → 43
```

### Histogram

A histogram measures the distribution of observations into buckets.

For example:

```text
Request duration

< 100ms     500 requests
< 500ms     900 requests
< 1s        980 requests
< 5s        995 requests
```

This is useful for understanding request latency distributions.

### Summary

A summary is also used for distributions, but the assignment emphasizes that summaries calculate **percentiles on the client side**, whereas histograms use buckets.

---

# 6. Labels and time series

Consider:

```text
http_requests_total{method="GET",status="200"}
```

The labels:

```text
method="GET"
status="200"
```

give additional dimensions to the metric.

A **time series** is essentially a unique combination of:

```text
metric name + label values
```

So these are different time series:

```text
http_requests_total{method="GET",status="200"}

http_requests_total{method="GET",status="500"}

http_requests_total{method="POST",status="200"}
```

This is one of the fundamental ideas you'll need for PromQL.

---

# 7. PromQL — the important part of Day 73

Start with:

```promql
up
```

This gives the health status of scrape targets:

```text
1 = UP
0 = DOWN
```

Then:

```promql
prometheus_http_requests_total
```

shows the Prometheus HTTP request counter.

For a particular handler:

```promql
prometheus_http_requests_total{handler="/api/v1/query"}
```

### Range vector

```promql
prometheus_http_requests_total[5m]
```

This asks for the metric's values over the previous five minutes.

### Rate

```promql
rate(prometheus_http_requests_total[5m])
```

This converts a counter into an approximate **per-second rate**.

The assignment specifically emphasizes that `rate()` is commonly used with counters.

### Aggregation

```promql
sum(rate(prometheus_http_requests_total[5m]))
```

Conceptually:

```text
individual time series
        ↓
      rate()
        ↓
      sum()
        ↓
overall request rate
```

### Label filtering

```promql
prometheus_http_requests_total{code="200"}
```

Only HTTP 200 responses.

And:

```promql
prometheus_http_requests_total{code!="200"}
```

Anything other than 200.

### Arithmetic

```promql
process_resident_memory_bytes / 1024 / 1024
```

converts bytes into MB.

### Top-K

```promql
topk(5, prometheus_http_requests_total)
```

returns the five highest matching series.

---

# 8. The exercise query

The assignment asks:

> Show the per-second rate of non-200 HTTP requests to Prometheus over the last five minutes.

Following the assignment's hint, the query is:

```promql
rate(prometheus_http_requests_total{code!="200"}[5m])
```

This combines:

```text
label filtering
      +
range vector [5m]
      +
rate()
```

That's a good example of how PromQL expressions are built from smaller pieces.

---

# 9. Add the sample application

Your Compose file will then contain two services:

```text
Prometheus :9090
      │
      │ scrapes
      ▼
notes-app :8000
```

The Prometheus target becomes:

```yaml
scrape_configs:
  - job_name: "prometheus"
    static_configs:
      - targets: ["localhost:9090"]

  - job_name: "notes-app"
    static_configs:
      - targets: ["notes-app:8000"]
```

After:

```bash
docker compose up -d
```

check:

```text
Status → Targets
```

You should have:

```text
prometheus    UP
notes-app     UP
```

Then generate traffic:

```bash
curl http://localhost:8000
curl http://localhost:8000
curl http://localhost:8000
```

The purpose isn't merely to make the application respond. You're generating activity that can subsequently appear in its metrics.

---

# 10. Prometheus storage

Prometheus stores metrics in its local **TSDB — Time Series Database**.

The assignment asks you to inspect it with:

```bash
docker exec prometheus du -sh /prometheus
```

The Compose file includes:

```yaml
volumes:
  - prometheus_data:/prometheus
```

This is important because the Prometheus container's writable filesystem is not where you want to depend on persistent monitoring data.

The named volume provides persistent storage outside the container lifecycle.

The assignment also introduces retention configuration:

```yaml
command:
  - '--config.file=/etc/prometheus/prometheus.yml'
  - '--storage.tsdb.retention.time=30d'
  - '--storage.tsdb.retention.size=1GB'
```

So retention can be controlled by **time** and **storage size**.

---

# 11. What I would focus on for your Day 73 interview preparation

Don't try to memorize every PromQL function today. Make sure you can confidently explain these:

### What is observability?

> Observability is the ability to understand the internal state and behavior of a system from the data it produces, primarily through metrics, logs, and traces.

### Monitoring vs observability?

> Monitoring tells me that something is wrong, while observability gives me the ability to investigate why it is wrong.

### What is Prometheus?

> Prometheus is a metrics collection and monitoring system that uses a pull-based model to scrape metrics from configured targets and stores them as time series that can be queried using PromQL.

### What is a scrape target?

> A scrape target is an endpoint from which Prometheus periodically collects metrics.

### Counter vs Gauge?

> A counter represents a value that increases over time, such as total HTTP requests. A gauge represents a value that can increase or decrease, such as current memory usage.

### Why `rate()`?

> Counters continuously accumulate values, so `rate()` converts their increase over a time window into a useful per-second rate.

### What does `up` mean?

> `up` is a Prometheus-generated metric indicating whether a scrape target was successfully scraped: `1` means up and `0` means down.

### Why Docker service names?

> Containers on the same Docker Compose network can communicate using service names, so Prometheus can reach `notes-app:8000` rather than using the host's localhost address.

---

## Day 73 → Day 77 mental model

Your previous days built the **platform**:

```text
Terraform
   ↓
Infrastructure
   ↓
Ansible
   ↓
Configured servers
   ↓
Docker
   ↓
Running application
```

Now observability sits on top:

```text
Running application
       │
       ├── Metrics ──→ Prometheus
       │
       ├── Logs ─────→ Promtail → Loki
       │
       └── Traces ───→ OTEL Collector
                              │
                              ▼
                           Grafana
```

Day 73 therefore isn't just “install Prometheus.” It is the point where you're moving from **deploying systems** to **being able to understand and operate those systems in production**.
