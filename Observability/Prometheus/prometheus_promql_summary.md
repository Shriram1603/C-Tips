
# Prometheus & PromQL Summary Guide

## Demo 1: Querying Summaries and Histograms

- **Summaries**:
  - Summaries contain two key metrics:
    - `_count`: number of samples recorded.
    - `_sum`: total value of all samples.
  - Useful for calculating **averages** over time:
    ```promql
    rate(http_request_duration_seconds_sum[1m]) / rate(http_request_duration_seconds_count[1m])
    ```

- **Histograms**:
  - Metrics contain labeled **buckets**, with counts of values ≤ the bucket limit.
  - Use `rate()` on buckets for per-second rate:
    ```promql
    rate(http_request_duration_seconds_bucket{le="1"}[1m])
    ```
  - Use `histogram_quantile()` for percentiles (e.g., 90th percentile):
    ```promql
    histogram_quantile(0.9, sum(rate(http_request_duration_seconds_bucket[1m])) by (le, instance))
    ```

## Demo 2: Evaluating Expressions Outside Prometheus UI

- Use `rate()` to track trends over time for counters (like `_sum` and `_count` in summaries).
- Example: Calculate and graph average delay from summary data:
  ```promql
  rate(app_delay_seconds_sum[1m]) / rate(app_delay_seconds_count[1m])
  ```

- **Histogram Percentiles**:
  - Filter on labels (e.g., `status="200"`)
  - Calculate 90th percentile response time:
    ```promql
    histogram_quantile(0.9, sum(rate(http_request_duration_seconds_bucket{status="200"}[1m])) by (le, instance))
    ```

- Use in dashboards:
  - Track spikes → performance issues.
  - Compare instance behavior for load balancing or regressions.
  - Trigger alerts when thresholds are exceeded.

## Demo 3: Prometheus API and Grafana

### Prometheus API
- Query endpoint: Get current values
  ```http
  http://localhost:9090/api/v1/query?query=metric_name
  ```

- Range queries (for time-series data):
  ```http
  http://localhost:9090/api/v1/query_range?query=metric_name&start=...&end=...&step=...
  ```

### Grafana Integration
- Add Prometheus as a data source in Grafana.
- Use PromQL expressions in panels:
  - **Active Jobs**:
    ```promql
    sum(active_jobs)
    ```
  - **Processed Jobs** (counter):
    ```promql
    sum(rate(jobs_processed_total[5m])) by (instance)
    ```
  - **Memory and CPU usage**:
    ```promql
    container_memory_usage_bytes
    container_cpu_usage_seconds_total
    ```

### Dashboard Insights
- Panels update based on PromQL results.
- Refresh dashboards in sync with Prometheus scrape interval.
- Info panels can show app version from labels in `*_info` metrics.

### Extra Features
- Dashboards in **kiosk mode**.
- Automatically rotating displays for NOC/monitoring views.
- Correlate performance graphs with version rollouts using `*_info` metrics.

---

This guide condenses the core PromQL patterns for querying summary and histogram metrics, working with the API, and building Grafana dashboards.
