# Windows Observability Lab

A Docker-based observability lab for monitoring Windows systems using Prometheus, Windows Exporter, and Grafana.

The project demonstrates infrastructure monitoring, configuration-as-code, Grafana provisioning, Prometheus configuration management, and reproducible deployments through Git.

Designed as a learning and portfolio project for DevOps, Platform Engineering, Infrastructure, and Cloud Engineering roles.

---

## Architecture

```text
Windows Host
│
├─ Windows Exporter (9182)
│
└─ Docker Desktop
   │
   ├─ Prometheus (9090)
   │    └─ Scrapes Windows Exporter
   │
   └─ Grafana (3000)
        └─ Uses Prometheus datasource
```

---

## Project Structure

```text
windows-observability-lab/
│
├── grafana/
│   ├── dashboards/
│   │   └── windows-dashboard.json
│   │
│   ├── provisioning/
│   │   ├── dashboards/
│   │   │   └── dashboards.yml
│   │   │
│   │   └── datasources/
│   │       └── grafana-datasources.yml
│   │
│   ├── data/                     # Ignored by Git
│   └── docker-compose.yml
│
├── prometheus/
│   ├── prometheus.yml
│   ├── data/                     # Ignored by Git
│   └── docker-compose.yml
│
├── .gitignore
└── README.md
```

---

## Components

### Prometheus

Prometheus is responsible for collecting and storing metrics.

Configured scrape targets:

```yaml
- prometheus
- windows
```

Additional target labels:

```yaml
host: windows-host
lab: desktoplab
os: windows
role: desktop
```

### Windows Exporter

Windows Exporter provides operating system metrics to Prometheus, including:

- CPU utilisation
- Memory usage
- Disk utilisation
- Network activity
- Running services
- Process information
- Operating system statistics

### Grafana

Grafana is used to visualise Prometheus metrics through dashboards.

Both the datasource and dashboard are automatically provisioned during container startup.

---

## Grafana Provisioning

### Datasource Provisioning

Location:

```text
grafana/provisioning/datasources/grafana-datasource.yml
```

Automatically creates the Prometheus datasource when Grafana starts.

### Dashboard Provisioning

Location:

```text
grafana/provisioning/dashboards/dashboards.yml
```

Automatically imports:

```text
windows-dashboard.json
```

when Grafana starts.

### Benefits

- No manual datasource creation
- No manual dashboard imports
- Consistent deployments
- Fully reproducible environment

---

## Deployment

### Prerequisites

- Docker Desktop
- Windows Exporter

### Clone Repository

```bash
git clone https://github.com/Xeals-Senpai/windows-observability-lab.git
cd windows-observability-lab
```

### Start Prometheus

```bash
cd prometheus
docker compose up -d
```

### Start Grafana

```bash
cd ../grafana
docker compose up -d
```

---

## Verification

### Prometheus

Open:

```text
http://localhost:9090
```

Targets page:

```text
http://localhost:9090/targets
```

Expected result:

```text
prometheus   UP
windows      UP
```

### Grafana

Open:

```text
http://localhost:3000
```

Expected result:

- Prometheus datasource automatically configured
- Windows dashboard automatically imported
- Metrics visible without manual configuration

---

## Reproducibility

The repository stores:

- Prometheus configuration
- Grafana datasource provisioning
- Grafana dashboard provisioning
- Dashboard JSON definitions

Runtime data is excluded through `.gitignore`:

```text
grafana/data/
prometheus/data/
```

This allows the monitoring stack to be recreated on a new machine using only:

```bash
git clone https://github.com/Xeals-Senpai/windows-observability-lab.git

cd windows-observability-lab/prometheus
docker compose up -d

cd ../grafana
docker compose up -d
```

---

## Skills Demonstrated

- Docker
- Docker Compose
- Prometheus
- Windows Exporter
- Grafana
- Infrastructure as Code (IaC)
- Monitoring and Observability
- Configuration Management
- Git and GitHub
- Linux and Windows Administration
- Reproducible Deployments

---

## Future Improvements

Potential future enhancements include:

- Linux server monitoring using Node Exporter
- Additional Grafana dashboards
- Alertmanager integration
- Multi-host monitoring
- Centralised log collection
- Container monitoring
- Infrastructure automation with Terraform or Ansible
