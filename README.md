# InfraForge

<p align="center">
  <strong>Enterprise-Style Infrastructure Engineering & Observability Platform</strong>
</p>

<p align="center">
  Self-hosted infrastructure lab built with Docker, Grafana, Prometheus, Loki, PostgreSQL, Redis, Nginx, Portainer and modern observability tooling.
</p>

---

# Table of Contents

* [Overview](#overview)
* [Objectives](#objectives)
* [Core Technologies](#core-technologies)
* [Infrastructure Architecture](#infrastructure-architecture)
* [Infrastructure Stack](#infrastructure-stack)
* [Monitoring & Observability](#monitoring--observability)
* [Centralized Logging Pipeline](#centralized-logging-pipeline)
* [Alerting System](#alerting-system)
* [Project Structure](#project-structure)
* [Installation Guide](#installation-guide)
* [Service Configuration](#service-configuration)
* [Screenshots](#screenshots)
* [Operational Concepts Learned](#operational-concepts-learned)
* [Troubleshooting](#troubleshooting)
* [Security Considerations](#security-considerations)
* [Future Improvements](#future-improvements)
* [Learning Outcomes](#learning-outcomes)
* [License](#license)

---

# Overview

InfraForge is a production-inspired infrastructure engineering homelab designed to simulate modern operational environments using containerized infrastructure tooling.

The project combines:

* Infrastructure monitoring
* Metrics collection
* Centralized logging
* Alerting
* Reverse proxying
* Persistent databases
* Container orchestration
* Service observability
* Infrastructure troubleshooting

into a unified self-hosted platform.

The primary goal of the project is to gain practical hands-on experience with infrastructure engineering, DevOps practices, platform engineering concepts, and modern observability tooling.

---

# Objectives

This project was built to:

* Learn modern infrastructure tooling hands-on
* Understand observability pipelines
* Simulate production-style operational environments
* Practice debugging infrastructure systems
* Understand metrics, logs and alerting workflows
* Learn Docker networking and service isolation
* Build real operational monitoring workflows
* Understand stateful vs stateless infrastructure
* Gain practical experience with Linux-based operations

---

# Core Technologies

| Technology      | Purpose                     |
| --------------- | --------------------------- |
| Docker Compose  | Container orchestration     |
| Nginx           | Reverse proxy & routing     |
| Grafana         | Metrics & log visualization |
| Prometheus      | Metrics collection          |
| Loki            | Centralized log aggregation |
| Promtail        | Log shipping                |
| Node Exporter   | Host metrics                |
| cAdvisor        | Container metrics           |
| PostgreSQL      | Relational database         |
| Redis           | In-memory datastore/cache   |
| Uptime Kuma     | Uptime monitoring           |
| Portainer       | Docker management           |
| Telegram Alerts | Operational alerting        |
| Fedora Linux    | Host operating system       |

---

# Infrastructure Architecture

```text id="s84b1r"
                                ┌─────────────────────┐
                                │      Browser        │
                                └──────────┬──────────┘
                                           │
                                    Reverse Proxy
                                           │
                                   ┌───────▼───────┐
                                   │     Nginx     │
                                   └───────┬───────┘
                                           │

┌─────────────────────────────────────────────────────────────────────────────┐
│                          Infrastructure Layer                              │
└─────────────────────────────────────────────────────────────────────────────┘

      ┌──────────────┐  ┌──────────────┐  ┌──────────────┐
      │   Grafana    │  │  Portainer   │  │   Adminer    │
      └──────┬───────┘  └──────┬───────┘  └──────┬───────┘
             │                 │                 │
             └─────────────────┼─────────────────┘
                               │
                  ┌────────────▼────────────┐
                  │ Internal Docker Network │
                  └────────────┬────────────┘
                               │

        ┌─────────────┐ ┌─────────────┐ ┌─────────────┐
        │ Prometheus  │ │ PostgreSQL  │ │    Redis    │
        └──────┬──────┘ └─────────────┘ └─────────────┘
               │
        ┌──────▼──────┐
        │  cAdvisor   │
        └──────┬──────┘
               │
        ┌──────▼──────┐
        │NodeExporter │
        └─────────────┘

        ┌─────────────┐
        │    Loki     │
        └──────┬──────┘
               │
        ┌──────▼──────┐
        │  Promtail   │
        └─────────────┘
```

---

# Infrastructure Stack

## Reverse Proxy Layer

### Nginx

Nginx is used as the central reverse proxy responsible for:

* Domain-based routing
* Service abstraction
* Internal traffic management
* Local development ingress

### Example Local Domains

```text id="t73l6m"
grafana.local
prometheus.local
portainer.local
adminer.local
```

---

## Monitoring & Metrics Layer

### Prometheus

Prometheus collects and stores infrastructure metrics from:

* Node Exporter
* cAdvisor
* Infrastructure services

Metrics include:

* CPU usage
* Memory utilization
* Filesystem usage
* Container resource consumption
* Network statistics

---

### Node Exporter

Node Exporter exposes Linux host metrics including:

* CPU load
* RAM usage
* Disk I/O
* Filesystem utilization
* Network throughput

---

### cAdvisor

cAdvisor monitors Docker containers and exposes:

* Container CPU usage
* Memory consumption
* Network activity
* Container lifecycle data

---

# Monitoring & Observability

## Grafana

Grafana acts as the central observability dashboard.

### Features

* Metrics visualization
* Infrastructure dashboards
* Log exploration
* Alerting
* Data source management
* Real-time monitoring

### Integrated Data Sources

* Prometheus
* Loki

---

# Centralized Logging Pipeline

## Loki

Loki is used for centralized log aggregation.

### Purpose

* Store Docker container logs
* Enable searchable infrastructure logs
* Integrate logs directly into Grafana

---

## Promtail

Promtail collects logs from:

* Docker containers
* System logs

and forwards them to Loki.

---

## Logging Pipeline Architecture

```text id="hlb5cx"
Docker Containers
        │
        ▼
    Promtail
        │
        ▼
      Loki
        │
        ▼
     Grafana
```

---

# Alerting System

Grafana alerting is integrated with Telegram notifications.

### Alert Workflow

```text id="xn6ywl"
Metrics
   │
   ▼
Prometheus
   │
   ▼
Grafana Alert Rules
   │
   ▼
Telegram Contact Point
   │
   ▼
Mobile Notifications
```

---

## Example Alerts

### Infrastructure Down Alert

```promql id="fwq7ea"
up{job="cadvisor"} == 0
```

### High CPU Usage

```promql id="ijjlwm"
100 - (avg by(instance)(rate(node_cpu_seconds_total{mode="idle"}[5m])) * 100)
```

### Memory Usage Alert

```promql id="jlwmmx"
node_memory_MemAvailable_bytes
```

---

# Databases & Stateful Services

## PostgreSQL

PostgreSQL is used as the primary relational database.

### Purpose

* Persistent structured storage
* Backend application support
* Stateful infrastructure learning

---

## Redis

Redis is used as:

* Cache layer
* In-memory datastore
* Session storage
* Fast temporary storage

Redis persistence is enabled using:

* Append Only File (AOF)

---

# Container Management

## Portainer

Portainer provides:

* Docker environment management
* Container lifecycle control
* Volume inspection
* Stack management
* Network visibility

---

# Uptime Monitoring

## Uptime Kuma

Used for:

* Service uptime checks
* Infrastructure monitoring
* Availability visibility
* Status tracking

---

# Project Structure

```text id="jlwmmy"
infraforge/
│
├── docker-compose.yml
│
├── nginx/
│   └── default.conf
│
├── prometheus/
│   └── prometheus.yml
│
├── promtail/
│   └── config.yml
│
├── grafana/
│
├── images/
│
└── README.md
```

---

# Installation Guide

## 1. Clone Repository

```bash id="jz6k3x"
git clone <repository-url>
cd infraforge
```

---

## 2. Configure Hosts File

Add local domains:

```bash id="j9o6lv"
sudo nano /etc/hosts
```

Add:

```text id="5jlv91"
127.0.0.1 grafana.local
127.0.0.1 prometheus.local
127.0.0.1 portainer.local
127.0.0.1 adminer.local
```

---

## 3. Start Infrastructure Stack

```bash id="jlwmmz"
docker compose up -d
```

---

## 4. Verify Running Containers

```bash id="jlwmn0"
docker ps
```

---

# Service Configuration

| Service     | URL                     |
| ----------- | ----------------------- |
| Grafana     | http://grafana.local    |
| Prometheus  | http://prometheus.local |
| Portainer   | http://portainer.local  |
| Adminer     | http://adminer.local    |
| Uptime Kuma | http://localhost:3001   |

---

# Screenshots

# Grafana Dashboards

## Infrastructure Metrics

![Infrastructure Metrics](./images/graphana_metrix.png)

## Infrastructure Monitoring

![Infrastructure Monitoring](./images/infrastructure_monitoring.png)

---

# Prometheus

## Prometheus Targets & Metrics

![Prometheus Metrics](./images/prometheus_cadvisor.png)

---

# cAdvisor

## Container Metrics

![cAdvisor Metrics](./images/cadvisor_metrics.png)

## cAdvisor Dashboard

![cAdvisor Dashboard](./images/cadvisor.png)

## Memory Monitoring

![Memory Metrics](./images/memory_cadvisor.png)

## CPU Monitoring

![CPU Metrics](./images/cadvisor_cpu.png)

---

# Uptime Kuma

## Dashboard

![Uptime Kuma](./images/Uptime_Kuma.png)

## Monitoring View

![Uptime Monitoring](./images/Uptime_Kuma_Monitor.png)

---

# Nginx Reverse Proxy

## Nginx Configuration

![Nginx](./images/nginx.png)

## Local Domain Configuration

![Hosts Configuration](./images/add_hosts.png)

---

# Portainer

## Container Management

![Portainer](./images/portainer.png)

---

# Adminer & PostgreSQL

## Database Management

![Adminer](./images/adminer.png)

---

# Redis

## Redis Service

![Redis](./images/redis.png)

---

# Operational Concepts Learned

This project helped develop practical understanding of:

* Infrastructure engineering
* Observability systems
* Metrics collection
* Log aggregation
* Operational monitoring
* Alert engineering
* Docker networking
* Reverse proxying
* Persistent storage
* Stateful infrastructure
* Service isolation
* Linux troubleshooting
* Infrastructure debugging
* Container orchestration

---

# Troubleshooting

## Grafana Login Loop

Cause:
Incorrect root URL configuration.

Correct:

```yaml id="qk6x3s"
GF_SERVER_ROOT_URL=http://grafana.local/
```

---

## Promtail Permission Issues (Fedora/SELinux)

SELinux may block access to Docker logs.

Fix:

```yaml id="jlwmn1"
security_opt:
  - label=disable
```

---

## Portainer Docker Socket Permission Error

Fix permissions:

```bash id="jlwmn2"
sudo chmod 666 /var/run/docker.sock
```

---

## Loki Datasource Origin Issues

Ensure Grafana is accessed consistently using:

```text id="jlwmn3"
http://grafana.local
```

instead of localhost.

---

# Security Considerations

This project is designed for:

* learning
* development
* homelab experimentation

Some configurations prioritize operational simplicity over hardened production security.

Examples:

* local HTTP routing
* relaxed Docker socket permissions
* SELinux exceptions for observability tooling

Future production hardening may include:

* HTTPS/TLS
* RBAC
* authentication proxy
* secrets management
* VPN access
* hardened container policies

---

# Future Improvements

Planned additions include:

* CI/CD pipelines
* GitHub Actions automation
* Automatic deployments
* Go backend application deployment
* HTTPS/TLS automation
* Keycloak/Authelia authentication
* Backup automation
* Docker Swarm exploration
* Kubernetes migration
* Infrastructure as Code
* Distributed tracing
* Tempo integration
* SSL certificates
* Advanced alerting workflows

---

# Learning Outcomes

InfraForge was built to develop practical experience in:

* DevOps Engineering
* Infrastructure Engineering
* Platform Engineering
* Site Reliability Engineering (SRE)
* Linux Operations
* Observability Engineering

The project focuses heavily on:

* operational understanding
* debugging
* infrastructure reasoning
* systems thinking
* real-world tooling integration

rather than simply following tutorials.

---

# License

MIT License

---

# Final Notes

InfraForge represents a continuously evolving infrastructure engineering homelab focused on understanding how modern operational systems interact in real-world environments.

The project emphasizes:

* hands-on learning
* operational troubleshooting
* observability engineering
* monitoring pipelines
* infrastructure automation
* production-style architecture

through practical implementation and experimentation.
