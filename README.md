Observability Monitoring VM (Ansible)

This repository provides a production-ready monitoring stack deployed via Ansible on a dedicated virtual machine.

It is designed to integrate seamlessly with my infrastructure project:
👉 devops_staging_prod_infra

The monitoring VM collects metrics, logs, and availability data from all production hosts using a modular and scalable approach based on Prometheus file-based service discovery (file_sd).

🚀 Overview

This project deploys a full observability stack:

Prometheus → metrics collection
Grafana → visualization & dashboards
Loki → log aggregation
Alertmanager → alert routing
Blackbox Exporter → HTTP/HTTPS probing

The stack runs entirely in Docker using docker-compose, managed by Ansible.

🧠 Architecture
🔹 High-level design
Each VM in the infrastructure runs:
node_exporter (metrics)
promtail (logs)
The monitoring VM:
scrapes metrics from all hosts
receives logs via Loki
probes endpoints using Blackbox

The key design principle is decoupling monitoring from application deployment.

🔹 Dynamic target discovery (file_sd)

Instead of hardcoding targets in Prometheus:

Targets are generated dynamically by the infrastructure repo:
prometheus_target → node targets
blackbox_target → HTTP checks
These are written as YAML files into:
/etc/prometheus/file_sd/node_targets/
/etc/prometheus/file_sd/blackbox_targets/

Prometheus automatically reloads them.

➡️ This enables:

zero-touch onboarding of new services
environment-aware monitoring (staging / production)
clean separation of concerns
📁 Project Structure
ansible-monitoring-vm/
├── inventory/
├── playbooks/
├── roles/
│   └── monitoring_stack/
│       ├── tasks/
│       ├── templates/
│       ├── defaults/
│       └── files/
└── README.md
⚙️ Deployment
1. Inventory
[monitoring]
vm-monitoring ansible_host=10.0.0.20

[monitoring:vars]
ansible_user=debian
2. Deploy
ansible-playbook -i inventory/production/hosts.ini playbooks/site.yml
3. Access services
Service	URL
Prometheus	http://<vm>:9090
Grafana	http://<vm>:3000
Loki API	http://<vm>:3100
Alertmanager	http://<vm>:9093
Blackbox	http://<vm>:9115
🔐 Grafana Credentials

Configured via Ansible variables:

monitoring_grafana_admin_user: admin
monitoring_grafana_admin_password: change-me
📊 Features
✅ Infrastructure-aware monitoring
Automatic discovery of all nodes
No manual Prometheus config updates
✅ Logs centralization (Loki)
Promtail agents push logs to Loki
Grafana provides unified log exploration
✅ HTTP / HTTPS monitoring
Blackbox exporter probes endpoints
Integrated with Prometheus alerting
✅ Modular & extensible
Add new scrape jobs via:
monitoring_extra_scrape_configs:
Add alerting receivers via:
monitoring_alertmanager_receivers:
🔄 Integration with Infrastructure

This repo is designed to work with:

👉 devops_staging_prod_infra

Responsibilities split
Component	Responsibility
Infra repo	Deploy apps + generate monitoring targets
Monitoring repo	Deploy observability stack
🧪 Example Flow
A new app is deployed via infra

prometheus_target creates a file:

- targets: ["10.0.0.10:9100"]
Prometheus automatically picks it up
Metrics appear in Grafana instantly
📦 Technologies Used
Ansible
Docker / Docker Compose
Prometheus
Grafana
Loki
Alertmanager
Blackbox Exporter
🎯 Design Goals
Separation of concerns
Scalability
Reproducibility
Production readiness
Observability-first architecture
🔮 Roadmap
 Add pre-configured Grafana dashboards
 Add alert rules (CPU, memory, HTTP failures)
 Add TLS support via reverse proxy (Caddy)
 Integrate with external notification systems (Slack, email)
 Add long-term storage (Thanos / Mimir)
👨‍💻 Author

DevOps / Linux / IAM Engineer
Focused on:

Infrastructure as Code
Observability
Secure and scalable architectures
📜 License

GNU GPL v3
