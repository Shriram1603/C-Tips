
# Prometheus Labels & Relabeling

## Overview

This document summarizes how Prometheus handles labels and how you can filter, enrich, and normalize metric data using relabeling and metric relabeling.

---

## 📌 Adding Extra Labels

You can enrich metrics with additional labels at the job or target level in the `prometheus.yml` file.

```yaml
static_configs:
  - targets: ['localhost:9100']
    labels:
      os: linux
      runtime: vm
```

### 🔍 Visibility

In Prometheus UI under **Status > Targets**, you'll see:

- Default labels: `job`, `instance`
- Custom labels: `os`, `runtime`

---

## ⚠️ Label Inconsistencies

### Example Problem

- `runtime=container` for batch jobs
- `runtime=docker` for web app on Windows

This inconsistency makes aggregation and filtering difficult.

---

## 🔁 Relabeling (target-level)

### Purpose

To normalize label values before metrics are ingested.

### Example: Normalize `runtime` to `docker`

```yaml
relabel_configs:
  - source_labels: [__meta_custom_runtime]
    regex: container
    target_label: runtime
    replacement: docker
    action: replace
```

### Other Actions

- `replace`: Change or copy values
- `drop`: Ignore a target completely
- `keep`: Only keep targets matching a condition
- `labeldrop`: Remove a specific label
- `labelkeep`: Keep only specific labels

---

## 📂 File-based Service Discovery

You can offload static configs to `.json` files:

```json
[
  {
    "targets": ["localhost:9100"],
    "labels": {
      "os": "linux",
      "runtime": "container"
    }
  }
]
```

Prometheus `prometheus.yml`:

```yaml
file_sd_configs:
  - files:
    - 'batch.json'
```

---

## 🎯 metric_relabel_configs

### Purpose

Manipulate metric labels after target discovery but before storage.

### Usage Scenarios

1. **Drop unwanted metrics**
   ```yaml
   metric_relabel_configs:
     - source_labels: [__name__]
       regex: go_.*
       action: drop
   ```

2. **Drop unnecessary labels**
   ```yaml
   metric_relabel_configs:
     - source_labels: [mountpoint]
       regex: .*
       replacement: ""
       target_label: mountpoint
       action: replace
   ```

3. **Rename labels**
   ```yaml
   metric_relabel_configs:
     - source_labels: [volume]
       regex: (.*)
       target_label: device
       replacement: "$1"
       action: replace
   ```

---

## ⚠️ Risks of Changing Labels

- Changing label names/values mid-stream can:
  - Break dashboards and alerts
  - Cause double counting
  - Produce multiple time series for the same metric

---

## ✅ Best Practices

![image](https://github.com/user-attachments/assets/941778f3-2e66-49ca-bbd5-3f3eed9db9c0)


- Normalize label values early using `relabel_configs`
- Use `metric_relabel_configs` to drop noisy/unneeded metrics
- Ensure label consistency across platforms (e.g., Windows vs Linux)
- Avoid label churn: don't change label values frequently
- Be cautious when evolving schemas — keep backward compatibility where possible

---

## 🔚 Summary

| Feature               | When It Runs          | Access to Temp Labels | Main Use                  |
|----------------------|-----------------------|------------------------|---------------------------|
| `relabel_configs`    | Before scraping       | ✅ Yes                 | Normalize/filter targets  |
| `metric_relabel_configs` | After scraping but before storage | ❌ No | Clean/normalize metrics     |

Prometheus provides powerful tools to manipulate labels, enabling flexible and consistent metric collection — but changes should be made thoughtfully to avoid downstream issues.
