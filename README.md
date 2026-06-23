# Windows Observability Lab

A lightweight Windows monitoring stack built using Prometheus, Grafana, and Windows Exporter.

This project collects Windows system metrics and visualises them through custom Grafana dashboards. The stack is designed as a home-lab observability environment and demonstrates monitoring, metrics collection, dashboard creation, and basic infrastructure management using Docker.

---

## Overview

The monitoring stack consists of:

- Grafana
- Prometheus
- Windows Exporter
- Docker Desktop

## Architecture:

```text
Windows Host
│
├─ Windows Exporter (9182)
│
└─ Docker Desktop
    ├─ Prometheus (9090)
    └─ Grafana (3000)
```

## Components

### Grafana

Responsible for:

- Dashboard visualisation
- Querying Prometheus
- Displaying system health metrics
- Alerting (future enhancement)

Container:

```yaml
image: grafana/grafana:latest
port: 3000
```

### Prometheus

Responsible for:

- Scraping metrics
- Storing time-series data
- Providing metrics to Grafana

Container:

```yaml
image: prom/prometheus:latest
port: 9090
```

### Windows Exporter

Responsible for exposing Windows performance counters to Prometheus.

Exporter Port:

```text
9182
```

---

## Grafana Datasource

### Prometheus

| Setting | Value |
|----------|----------|
| Name | Prometheus |
| Type | Prometheus |
| URL | http://host.docker.internal:9090 |
| Access | Server |

---

## Prometheus Configuration

Scrape target:

```yaml
scrape_configs:
  - job_name: windows
    static_configs:
      - targets:
          - host.docker.internal:9182
```

---

## Dashboard Metrics

### System Overview

| Metric | Description |
|----------|----------|
| Uptime | System uptime |
| Processors | Logical processor count |
| RAM | Installed physical memory |
| CPU Usage | Overall CPU utilisation |
| Memory Usage | Physical memory utilisation |
| Disk Usage | Drive usage percentage |
| Bandwidth | Network throughput |

---

## Memory Metrics

### Total RAM

```promql
windows_memory_physical_total_bytes
```

### Available RAM

```promql
windows_memory_available_bytes
```

### Used RAM

```promql
windows_memory_physical_total_bytes - windows_memory_available_bytes
```

---

## CPU Metrics

### CPU Usage %

```promql
100 - (avg(rate(windows_cpu_time_total{mode="idle"}[5m])) * 100)
```

---

## Disk Metrics

### Drive Usage %

```promql
100 - (
  windows_logical_disk_free_bytes
  /
  windows_logical_disk_size_bytes
) * 100
```

### Drive Filter

Only display mounted drive letters:

```promql
volume=~"[A-Z]:"
```

---

## Network Metrics

### Received Traffic

```promql
rate(windows_net_bytes_received_total[5m])
```

### Sent Traffic

```promql
rate(windows_net_bytes_sent_total[5m])
```

---

## Disk I/O Metrics

### Read Throughput

```promql
rate(windows_logical_disk_read_bytes_total[5m])
```

### Write Throughput

```promql
rate(windows_logical_disk_write_bytes_total[5m])
```

### Disk Operations

```promql
rate(windows_logical_disk_reads_total[5m])
```

```promql
rate(windows_logical_disk_writes_total[5m])
```

---

## Pagefile Activity

### Swap/Pagefile Operations

```promql
rate(windows_memory_swap_page_operations_total[5m])
```

---

## Repository Structure

```text
windows-observability-lab/
├── grafana/
│   ├── docker-compose.yml
│   └── dashboards/
│       └── windows-dashboard.json
│
├── prometheus/
│   ├── docker-compose.yml
│   └── prometheus.yml
│
├── .gitignore
└── README.md
```

---

## Future Improvements

- Grafana Alerting
- Multi-host monitoring
- Linux Node Exporter support
- Docker container monitoring
- Alert notifications (Discord / Email)
- Long-term metric retention
- Infrastructure as Code deployment