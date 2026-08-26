Day 74 builds directly on Day 73. You now move from **collecting metrics with Prometheus** to **monitoring the actual host and Docker containers and visualizing those metrics in Grafana**. The expected result is Node Exporter + cAdvisor + Grafana, with a custom dashboard. 

## Day 74 mental model

```text
                    ┌──────────────┐
                    │   Grafana    │
                    │    :3000     │
                    └──────┬───────┘
                           │
                           │ PromQL
                           ▼
                    ┌──────────────┐
                    │  Prometheus  │
                    │    :9090     │
                    └──────┬───────┘
                           │
              ┌────────────┼────────────┐
              │            │            │
              ▼            ▼            ▼
        Node Exporter   cAdvisor    notes-app
           :9100          :8080        :8000
              │            │
              ▼            ▼
            HOST        CONTAINERS
```

The key distinction is:

```text
Node Exporter → "How is my server doing?"
cAdvisor      → "How are my containers doing?"
Prometheus    → "Collect and query the metrics"
Grafana       → "Visualize the metrics"
```

---

# 1. Node Exporter

Node Exporter exposes Linux host-level metrics such as:

* CPU
* Memory
* Disk/filesystem
* Network

The assignment mounts `/proc`, `/sys`, and `/` read-only so Node Exporter can inspect the host without modifying it. 

Your Prometheus configuration adds:

```yaml
- job_name: "node-exporter"
  static_configs:
    - targets: ["node-exporter:9100"]
```

After:

```bash
docker compose up -d
```

verify:

```bash
curl http://localhost:9100/metrics | head -20
```

and check **Prometheus → Status → Targets** for:

```text
node-exporter    UP
```

The assignment then has you query metrics such as CPU idle time, total/available memory, filesystem usage, and network traffic. 

### Important PromQL example

Memory percentage:

```promql
(1 - node_memory_MemAvailable_bytes / node_memory_MemTotal_bytes) * 100
```

This follows the basic calculation:

```text
Used memory
------------
Total memory
```

---

# 2. cAdvisor

This is where the monitoring scope changes.

**Node Exporter monitors the host.**

**cAdvisor monitors containers.**

cAdvisor uses Docker's socket, `/sys`, and `/var/lib/docker` to discover containers and collect container resource statistics. 

Prometheus target:

```yaml
- job_name: "cadvisor"
  static_configs:
    - targets: ["cadvisor:8080"]
```

Useful queries include:

```promql
rate(container_cpu_usage_seconds_total{name!=""}[5m])
```

for container CPU,

```promql
container_memory_usage_bytes{name!=""}
```

for container memory,

and:

```promql
topk(3, container_memory_usage_bytes{name!=""})
```

to identify the three highest-memory containers.

The `{name!=""}` filter is important in the assignment because it removes aggregated/system-level entries and focuses on named containers. 

### Interview answer

If someone asks:

**"What's the difference between Node Exporter and cAdvisor?"**

A strong answer is:

> Node Exporter exposes metrics about the underlying Linux host, such as CPU, memory, disk, and network. cAdvisor focuses on container-level resource usage and performance, such as CPU, memory, and network usage of individual Docker containers. I would use Node Exporter to understand host health and cAdvisor to understand which containers are consuming those host resources.

---

# 3. Grafana

Prometheus is excellent for **storing and querying metrics**, but Grafana gives you the visual operational dashboard.

The assignment runs Grafana on:

```text
http://localhost:3000
```

and configures Prometheus as its datasource using:

```text
http://prometheus:9090
```

Notice the important distinction:

```text
Browser → localhost:3000

Grafana container → prometheus:9090
```

Inside the Docker network, `prometheus` is the service/container name. The assignment explicitly warns not to use `localhost` for the Grafana-to-Prometheus connection. 

---

# 4. Your first Grafana dashboard

The assignment has you create five panels.

### CPU

```promql
100 - (avg(rate(node_cpu_seconds_total{mode="idle"}[5m])) * 100)
```

This effectively calculates:

```text
100% - average CPU idle %
```

→ CPU utilization.

### Memory

```promql
(1 - node_memory_MemAvailable_bytes / node_memory_MemTotal_bytes) * 100
```

### Container CPU

```promql
rate(container_cpu_usage_seconds_total{name!=""}[5m]) * 100
```

### Container memory

```promql
container_memory_usage_bytes{name!=""} / 1024 / 1024
```

This converts bytes → MB.

### Disk

```promql
(1 - node_filesystem_avail_bytes{mountpoint="/"} / node_filesystem_size_bytes{mountpoint="/"}) * 100
```

The assignment's final dashboard is named **"DevOps Observability Overview"**. 

---

# 5. Provisioning — important DevOps concept

This is probably one of the most valuable concepts in Day 74.

You *can* configure Grafana manually through the UI.

But that's not ideal for production.

Imagine you destroy the Grafana server and recreate it.

If you configured everything manually:

```text
New Grafana
   ↓
Click around
   ↓
Add datasource
   ↓
Configure dashboards
   ↓
Repeat everything
```

Instead, use configuration-as-code:

```text
datasources.yml
       ↓
Grafana starts
       ↓
Prometheus datasource automatically configured
```

The assignment creates:

```text
grafana/
└── provisioning/
    ├── datasources/
    │   └── datasources.yml
    └── dashboards/
```

and defines Prometheus as the default datasource in YAML. 

### Interview answer

> Grafana datasource provisioning makes the monitoring environment reproducible. Instead of manually configuring datasources through the UI, I define them as configuration files that can be version-controlled and automatically applied when Grafana starts.

That is essentially the same **Infrastructure as Code / Configuration as Code mindset** you've been practicing with Terraform and Ansible.

---

# 6. Community dashboards

The assignment also introduces Grafana's community dashboards.

You are asked to import:

```text
1860 → Node Exporter Full
193  → Docker monitoring via cAdvisor
```

Dashboard 1860 gives you a much more comprehensive view of the Node Exporter metrics you've already been querying manually. 

So you're learning two approaches:

```text
Build dashboard yourself
        ↓
Understand exactly what each metric/query does

Import community dashboard
        ↓
Use production-style dashboards quickly
```

Both are useful.

---

# 7. Final Day 74 stack

By the end, your Compose project should have:

```text
prometheus
node-exporter
cadvisor
grafana
notes-app
```

The assignment explicitly expects all five services to be running. 

You can verify with:

```bash
docker compose ps
```

And Prometheus should have at least:

```text
prometheus      UP
notes-app       UP
node-exporter   UP
cadvisor        UP
```

---

# 8. Day 73 vs Day 74

This progression is worth remembering:

| Day    | Focus                                     |
| ------ | ----------------------------------------- |
| **73** | Prometheus fundamentals                   |
|        | Scraping, targets, metrics, PromQL        |
| **74** | Infrastructure monitoring + visualization |
|        | Node Exporter, cAdvisor, Grafana          |

So:

```text
Day 73

Application
    ↓
Prometheus
    ↓
PromQL


Day 74

Host ───────→ Node Exporter ──┐
                              │
Containers → cAdvisor ────────┤
                              ▼
Application ───────────────→ Prometheus
                              │
                              ▼
                           Grafana
```

---

## The 5 things I'd make sure you can explain

**1. What does Node Exporter do?**

> Exposes Linux host-level metrics for Prometheus.

**2. What does cAdvisor do?**

> Exposes container resource and performance metrics.

**3. Why do we need Grafana if Prometheus already has a UI?**

> Prometheus is primarily focused on metrics collection and querying; Grafana provides richer dashboards and visualization for operational monitoring.

**4. Why use `prometheus:9090` instead of `localhost:9090` in Grafana?**

> Because Grafana and Prometheus are separate containers. `prometheus` resolves to the Prometheus service on the Docker network, while `localhost` inside Grafana refers to the Grafana container itself.

**5. Why provision Grafana with YAML?**

> To make datasource configuration repeatable, version-controlled, and suitable for automated deployments rather than depending on manual UI configuration.

The Day 74 documentation should capture the complete Compose/Prometheus configuration, Node Exporter vs cAdvisor, target health, your custom dashboard, the community dashboard, PromQL examples, and YAML datasource provisioning. 
