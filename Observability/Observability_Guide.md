# Observability Overview

## User’s Understanding

```
I want to learn about Observabilities:

I will give you my understanding, correct me if I am wrong, and give a detailed explanation.

First, there are 3 types of observability, and we use certain binaries to deal with them

1) Log - Loki and Serilog

Here we log all the things that are going on in our client or server when we get an error
We can see that in the log, but when we have multiple microservices login,g each will make the
log un-ordered and hard to trace back, to solve this, we introduced Traces

2) Traces - Jagger

Traces add a unique ID to the logs, so now we can select an error log and use its ID to
trace the path, but neither of these store the data; we want a real-time SQL DB to store
and draw insights from it, thus comes Prometheus.

3) Metrics - Prometheus :

The logs will have a time stamp. If we find an error log, we can get its timestamp and
Then query it in promQL to get the state of the machine at that time and
Find out what the issue is, and lastly, we can integrate all these in Grafana
to draw insights from them.
```

---

## Refined and Expanded Explanation

**Observability** refers to the ability to understand what's happening inside a system based on the data it produces—especially important in distributed systems like microservices. It primarily involves three pillars:

---

### 1. Logs

**What They Are:**  
Logs are **timestamped text records** that capture discrete events happening inside an application (e.g., exceptions, service starts, database queries).

**Tools:**  
- **Serilog**: Structured logging for .NET.  
- **Loki**: A log aggregation system by Grafana Labs that indexes logs using labels.

**Problem in Microservices:**  
Logs from many services get jumbled, making it hard to trace related events.

**Solution:**  
- Use structured logs with consistent fields like trace IDs.  
- Aggregate and search with **Loki**.


### Visual: Unordered Logs

```
Logs:

log1
log5
log121

Unordered and hard to trace when spread across microservices.
```
---

### 2. Traces

**What They Are:**  
Traces follow the **journey of a request** through various services. Each hop is a **span**, grouped under a **trace ID**.

**Tools:**  
- **Jaeger** (not Jagger): A distributed tracing platform.  
- **OpenTelemetry**: A standard to instrument logs, metrics, and traces.

**Use Case:**  
Find which service is slow or where a request failed by following the trace.

### Visual: Structured Tracing with a Trace ID

```
Traces:

Log 1 (ID 121)
      |
      |___ Log 212 (ID 121)
      |        |
      |        |___ Log 514 (ID 121)
```

This trace allows you to follow the full journey of a request (ID 121).

---

---

### 3. Metrics

**What They Are:**  
Metrics are **numerical measurements** collected over time (e.g., CPU usage, latency, memory).

**Tools:**  
- **Prometheus**: A time-series database that scrapes and stores metrics.  
- Uses **PromQL** to query the data.

**Use Case:**  
- Monitor trends.  
- Trigger alerts.  
- Investigate historical performance using timestamps.

### Visual: Time-correlated metrics from PromQL

```
Metrics:

[ Trace Timestamp: 2024-06-12T15:04:05Z ]

PromQL Query -> CPU Usage, Memory, Request Rate

Graph results used for diagnostics
```

---

---

### 4. Correlation Strategy

- From **logs**, extract the **trace ID**.  
- Use the trace ID to inspect the **request flow** in **Jaeger**.  
- Use the **timestamp** to query **Prometheus** metrics.  
- Visualize and correlate all in **Grafana**.

---

### 5. Grafana: The Unified UI

Grafana is the unified dashboarding tool that brings Logs (via Loki), Metrics (via Prometheus), and Traces (via Jaeger) together.

**Purpose:**  
Visualize all observability data:  
- Logs from **Loki**  
- Metrics from **Prometheus**  
- Traces from **Jaeger**

**Benefits:**  
- Unified view for analysis.  
- Drill-down capabilities.  
- Kiosk/dashboard modes.

---

## Summary Table

| Pillar     | Data Type           | Use Case                        | Tools                      |
|------------|---------------------|----------------------------------|----------------------------|
| Logs       | Textual events      | Debugging, audit trails          | Serilog, Loki              |
| Traces     | Request journeys    | Root cause analysis              | Jaeger, OpenTelemetry      |
| Metrics    | Numeric time-series | Monitoring, alerting, performance| Prometheus                 |
| Visualization | Unified dashboards | Insight & correlation         | Grafana                    |



By combining all three pillars of observability, teams can gain deep insight into system behavior, quickly resolve issues, and optimize performance.
