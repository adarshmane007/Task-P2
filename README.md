# 📊 Full Monitoring Stack: Prometheus + Grafana for MySQL, PostgreSQL, and System Metrics

## 🎯 Objective

The goal of this project is to build a complete monitoring stack on an Ubuntu EC2 instance using **Prometheus** for metrics collection and **Grafana** for visualization. The stack monitors:

- **System metrics** using Node Exporter
- **MySQL database metrics** using mysqld_exporter
- **PostgreSQL database metrics** using postgres_exporter

This setup ensures real-time observability of infrastructure and database health, with all metrics visualized in Grafana dashboards.

---

## 🧱 Stack Components

| Component         | Version     | Purpose                            |
|------------------|-------------|------------------------------------|
| Prometheus        | 2.49.1      | Metrics collection and scraping    |
| Grafana           | OSS (latest)| Dashboard visualization            |
| Node Exporter     | 1.7.0       | System metrics exporter            |
| MySQL Exporter    | 0.15.0      | MySQL metrics exporter             |
| PostgreSQL Exporter| 0.15.0     | PostgreSQL metrics exporter        |
| OS                | Ubuntu 20.04| EC2 instance base image            |

---

## ⚙️ Setup Overview

### 🔹 1. Prometheus Installation & Configuration

- Downloaded and extracted Prometheus.
- Created a custom `prometheus.yml` with three scrape jobs:

```yaml
scrape_configs:
  - job_name: 'node'
    static_configs:
      - targets: ['localhost:9100']

  - job_name: 'mysql'
    static_configs:
      - targets: ['localhost:9104']

  - job_name: 'postgres'
    static_configs:
      - targets: ['localhost:9187']
```

- Started Prometheus using:

```bash
nohup ./prometheus --config.file=prometheus.yml > prometheus.log 2>&1 &
```

---

### 🔹 2. Node Exporter Setup

- Extracted Node Exporter and launched it:

```bash
nohup ./node_exporter > node_exporter.log 2>&1 &
```

- Verified metrics at `http://localhost:9100/metrics`.

---

### 🔹 3. MySQL Exporter Setup

- Created a MySQL user with monitoring privileges.
- Configured `.my.cnf` with credentials.
- Started exporter:

```bash
nohup ./mysqld_exporter > mysqld_exporter.log 2>&1 &
```

- Verified metrics at `http://localhost:9104/metrics`.

---

### 🔹 4. PostgreSQL Exporter Setup

- Provisioned PostgreSQL using Ansible with:
  - Database: `internship_pg`
  - User: `intern_user` with full privileges
  - Sample data in `users` table

- Set environment variable:

```bash
export DATA_SOURCE_NAME="postgresql://intern_user:intern_pass@localhost:5432/internship_pg?sslmode=disable"
```

- Started exporter:

```bash
nohup ./postgres_exporter > postgres_exporter.log 2>&1 &
```

- Verified metrics at `http://localhost:9187/metrics`.

---

### 🔹 5. Grafana Installation & Dashboard Setup

- Installed Grafana via APT and started the service:

```bash
sudo apt install -y grafana
sudo systemctl start grafana-server
sudo systemctl enable grafana-server
```

- Accessed Grafana at `http://<EC2-IP>:3000`
- Added Prometheus as a data source (`http://localhost:9090`)
- Imported dashboards using the following IDs:
  - **Node Exporter Full**: `1860`
  - **MySQL Overview**: `7362`
  - **PostgreSQL Overview**: `9628`

---

## 📁 Repository Structure

```
## 📁 Folder Structure

```
~/ (home directory)
├── prometheus.yml
├── prometheus-2.49.1.linux-amd64/
├── node_exporter-1.7.0.linux-amd64/
├── mysqld_exporter-0.15.0.linux-amd64/
├── postgres_exporter-0.15.0.linux-amd64/
└── screenshots/
    ├── prometheus-target.jpg
    ├── mysql-metrics.jpg
    ├── node-exporter-metrics.jpg
    └── postgresql-metrics.jpg
```
```

---

## 🖼️ Screenshots

### 🖼️ Screenshot 1: Prometheus Targets (All UP)
![Prometheus Targets](screenshots/prometheus-target.jpg)

### 🖼️ Screenshot 2: MySQL Metrics Dashboard
![MySQL Metrics](screenshots/mysql-metrics.jpg)

### 🖼️ Screenshot 3: Node Exporter (System Metrics)
![Node Exporter Metrics](screenshots/node-exporter-metrics.jpg)

### 🖼️ Screenshot 4: PostgreSQL Metrics Dashboard
![PostgreSQL Metrics](screenshots/postgresql-metrics.jpg)

---

## ✅ Outcome

- All exporters are running and visible in Prometheus.
- Grafana dashboards are fully functional and reflect real-time metrics.
- Exporters are launched using `nohup` for simplicity (no systemd used).
- Screenshots are organized and included for verification.

---

## 🧠 Notes

- Exporters were run manually using `nohup` as `systemd` was not required.
- Screenshots were transferred from Windows to EC2 using `scp` via WSL.
- The setup is reproducible and documented for future deployments.

---

## 👨‍💻 Author

**Adarsh Mane**  
DevOps Intern @ Pearl Thought  
Focused on cloud-native monitoring, automation, and reproducible infrastructure


