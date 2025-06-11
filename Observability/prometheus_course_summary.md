
# 📘 Getting Started with Prometheus – Summary Notes

## 🎯 What is Prometheus?
- A free, open-source monitoring and alerting toolkit.
- Tracks metrics at various levels: server, network, and application.
- Helps detect trends and issues before they become critical.

## 👨‍🏫 Course Intro (Pluralsight by Elton)
- Course goal: Introduce Prometheus and teach how to build a monitoring solution with it.
- Prometheus has become the **standard** for monitoring modern applications.

## ⚙️ Prometheus Overview
- Prometheus is a **server component**.
- Typically, one Prometheus instance per environment.
- Cross-platform: runs on **Windows and Linux**.

## 🧠 Core Components and Features

### ⏲️ Scheduler (Pull Model)
- Prometheus **pulls** (scrapes) metrics from monitored systems.
- Metrics are fetched every configurable interval (e.g., 30s, 1m).
- Monitored systems must **expose** metrics over HTTP.

### 📊 Time Series Database
- Prometheus stores all metrics with **timestamps** in its own database.
- Systems expose only current metrics; Prometheus adds timestamps and handles efficient storage.

### 📈 Metric Sources
- OS metrics (CPU, memory, disk) from Linux/Windows servers.
- Application metrics (e.g., active users, sales totals).
- Web server metrics (HTTP request/response stats).

### 🧪 Querying Metrics
- Prometheus has its own query language: **PromQL**.
- Includes a built-in **HTTP API** for querying.
- Metrics can be queried using **time ranges**.

### 🖥️ Web UI
- Simple admin interface for:
  - Viewing server configuration
  - Checking target reachability
  - Running queries and visualizing metrics
- Not a full dashboard solution.

### 📊 Dashboards (External)
- For full system dashboards, connect Prometheus to tools like **Grafana** via its API.

### 🚨 Alerting System
- Register **alerting rules** in Prometheus.
- Separate component (Alertmanager) handles triggered alerts:
  - Send emails
  - Page on-call staff
  - Create support tickets

## ✅ Why Prometheus?
- Unified, efficient solution: one server runs scraping, storage, query engine, and basic UI.
- Designed for **modern cloud-native and containerized** environments.
- Enables **proactive monitoring** through powerful alerting and historical data analysis.
