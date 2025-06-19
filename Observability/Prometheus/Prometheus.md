
# 🛠️ Prometheus Crash Course Cheat Sheet

A quick reference guide to understanding Prometheus concepts so you can breeze through courses like the one on Pluralsight.

---

## 📦 What is Prometheus?

Prometheus is an **open-source monitoring and alerting system** designed to collect, store, and query **metrics** (numeric data about your system/application performance).

---

## 🧩 Core Concepts

### 🔢 1. Metrics

Metrics are numeric measurements of system behavior or performance.

**Types of metrics:**
| Type       | Description                                          | Example                     |
|------------|------------------------------------------------------|-----------------------------|
| Counter    | Always increases                                     | `http_requests_total`       |
| Gauge      | Can go up and down                                   | `memory_usage_bytes`        |
| Histogram  | Tracks distribution (e.g., response time buckets)    | `http_response_duration`    |
| Summary    | Similar to histogram, includes quantiles/percentiles | `rpc_duration_seconds`      |

---

### 🌐 2. Exposing Metrics

Applications expose metrics by serving an HTTP endpoint, typically at `/metrics`, that returns metrics in **Prometheus format**.

**Example response:**
```
http_requests_total{method="GET", status="200"} 42
```

Prometheus **scrapes** (pulls) this data at regular intervals.

➡️ If your application doesn’t expose metrics, use an **exporter** (e.g., `node_exporter`, `blackbox_exporter`).

---

### ⏰ 3. Scheduler (Scraping)

Prometheus uses a **pull model**: it scrapes targets on a schedule defined in `prometheus.yml`.

```yaml
scrape_configs:
  - job_name: "my-app"
    static_configs:
      - targets: ["localhost:8080"]
```

It will regularly pull from `http://localhost:8080/metrics` and store the results.

---

## 🔄 Overall Flow

```text
[Your App with /metrics endpoint]
        ↑      (expose metrics)
        |
   Prometheus (scrapes metrics every X seconds)
        ↓
   Stores in time-series DB
        ↓
   Query with PromQL or visualize in Grafana
```

---

## ✅ TL;DR Quick Reference

| Term             | Meaning                                                                 |
|------------------|-------------------------------------------------------------------------|
| **Metric**       | A numeric value representing system performance                         |
| **Expose**       | Your app provides a `/metrics` endpoint with metric data                |
| **Scheduler**    | Prometheus scrapes metrics on a defined interval                        |
| **Exporter**     | A tool that provides metrics for systems that can’t expose them natively|
| **Scraping**     | The process of Prometheus pulling metrics regularly                     |

---

## 📌 Related Tools

- **Grafana** – Visualization tool often used with Prometheus
- **Alertmanager** – Sends alerts based on Prometheus rules
- **Exporters** – Bridge systems (e.g., Linux stats, databases) with Prometheus

---

> Tip: All Prometheus metrics can be queried with **PromQL**, a powerful query language for aggregating and analyzing time series data.
