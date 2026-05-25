# InfraForge Architecture

# Overview

InfraForge is a self-hosted infrastructure engineering and observability platform designed to simulate modern production-style operational environments using containerized infrastructure services.

The architecture focuses on:

* Service isolation
* Infrastructure observability
* Monitoring pipelines
* Centralized logging
* Reverse proxy management
* Stateful service deployment
* Internal networking
* Operational alerting
* Container orchestration

The stack is intentionally designed to mirror core concepts commonly found in:

* DevOps environments
* Platform engineering systems
* Site Reliability Engineering (SRE) workflows
* Modern cloud-native infrastructure

---

# High-Level Architecture

```text id="jlwmn7"
                                 ┌────────────────────┐
                                 │       Users        │
                                 └─────────┬──────────┘
                                           │
                                           ▼

                              ┌──────────────────────┐
                              │  Nginx Reverse Proxy │
                              └─────────┬────────────┘
                                        │
        ┌───────────────────────────────┼───────────────────────────────┐
        │                               │                               │
        ▼                               ▼                               ▼

 ┌──────────────┐              ┌──────────────┐               ┌──────────────┐
 │   Grafana    │              │ Uptime Kuma  │               │   Adminer    │
 └──────┬───────┘              └──────────────┘               └──────┬───────┘
        │                                                            │
        │                                                            │
        ▼                                                            ▼

 ┌──────────────┐                                            ┌──────────────┐
 │ Prometheus   │                                            │ PostgreSQL   │
 └──────┬───────┘                                            └──────────────┘
        │
        │
        ▼

 ┌──────────────┐
 │  cAdvisor    │
 └──────┬───────┘
        │
        ▼

 ┌──────────────┐
 │Node Exporter │
 └──────────────┘


 ┌──────────────┐
 │   Promtail   │
 └──────┬───────┘
        │
        ▼

 ┌──────────────┐
 │     Loki     │
 └──────┬───────┘
        │
        ▼

 ┌──────────────┐
 │   Grafana    │
 └──────────────┘
```

---

# Architectural Goals

The architecture was designed around the following principles:

## 1. Service Isolation

Each service runs inside its own container to provide:

* operational separation
* simplified management
* portability
* reproducibility
* failure isolation

---

## 2. Observability-First Design

The stack prioritizes infrastructure visibility through:

* metrics
* logs
* uptime monitoring
* dashboards
* alerting systems

This enables:

* operational debugging
* resource monitoring
* incident visibility
* infrastructure analysis

---

## 3. Internal Networking

All services communicate through a shared Docker bridge network.

This provides:

* service discovery
* internal hostname resolution
* isolated infrastructure communication
* reduced external exposure

Example:

```text id="jlwmn8"
grafana → prometheus
grafana → loki
promtail → loki
adminer → postgres
```

---

# Reverse Proxy Architecture

## Nginx

Nginx acts as the centralized ingress layer.

### Responsibilities

* Route traffic to internal services
* Manage local development domains
* Abstract container ports
* Simplify infrastructure access

---

## Domain Routing

Infrastructure services are exposed using local domains:

```text id="jlwmn9"
grafana.local
prometheus.local
portainer.local
adminer.local
```

---

## Traffic Flow

```text id="jlwmna"
Browser Request
      │
      ▼
Nginx Reverse Proxy
      │
      ▼
Target Internal Service
```

---

# Monitoring Architecture

# Prometheus Monitoring Pipeline

Prometheus acts as the metrics collection engine.

---

## Metrics Sources

### Node Exporter

Provides host-level metrics:

* CPU usage
* Memory usage
* Filesystem metrics
* Network statistics
* System load

---

### cAdvisor

Provides container-level metrics:

* Container CPU
* Container memory
* Network activity
* Container lifecycle metrics

---

## Metrics Flow

```text id="jlwmnb"
Infrastructure Services
        │
        ▼
 Exporters / cAdvisor
        │
        ▼
    Prometheus
        │
        ▼
     Grafana
```

---

# Logging Architecture

# Loki Logging Pipeline

InfraForge uses Loki for centralized logging.

---

## Promtail

Promtail is responsible for:

* collecting Docker logs
* tailing container logs
* forwarding logs to Loki

---

## Loki

Loki stores and indexes logs efficiently.

Unlike traditional logging systems:

* Loki indexes labels
* not full log contents

This reduces:

* storage overhead
* infrastructure complexity

---

## Logging Flow

```text id="jlwmnc"
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

# Observability Stack

InfraForge implements the three pillars of observability:

| Pillar        | Tool       |
| ------------- | ---------- |
| Metrics       | Prometheus |
| Logs          | Loki       |
| Visualization | Grafana    |

---

# Alerting Architecture

Grafana alerting is integrated with Telegram notifications.

---

## Alert Workflow

```text id="jlwmnd"
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

## Alert Types

Current alerts include:

* infrastructure downtime
* exporter failures
* monitoring interruptions
* service availability alerts

---

# Stateful Services

# PostgreSQL

PostgreSQL acts as the persistent relational database layer.

### Purpose

* structured data storage
* persistent application data
* backend service support

---

# Redis

Redis provides:

* caching
* temporary storage
* session storage
* fast in-memory operations

Persistence is enabled using:

* Append Only File (AOF)

---

# Container Management Layer

# Portainer

Portainer provides operational visibility into:

* Docker containers
* networks
* volumes
* stacks
* environments

This simplifies:

* infrastructure management
* debugging
* operational workflows

---

# Uptime Monitoring Layer

# Uptime Kuma

Uptime Kuma continuously monitors:

* service availability
* uptime status
* operational accessibility

This provides:

* availability visibility
* operational monitoring
* service health tracking

---

# Docker Networking

All services communicate through a shared Docker network.

---

## Benefits

* automatic service discovery
* isolated internal communication
* simplified container interaction
* reduced external exposure

---

## Example Internal Resolution

```text id="jlwmne"
grafana → http://prometheus:9090
grafana → http://loki:3100
adminer → postgres:5432
```

---

# Persistence Architecture

Persistent Docker volumes are used for:

* PostgreSQL data
* Grafana dashboards
* Redis persistence
* Loki data storage

---

# Security Considerations

InfraForge is primarily designed for:

* learning
* experimentation
* homelab infrastructure

Some configurations intentionally prioritize:

* simplicity
* operational learning
* easier debugging

over hardened production security.

---

## Current Security Limitations

* local HTTP routing
* relaxed Docker socket permissions
* SELinux exceptions
* no centralized authentication
* no TLS certificates

---

## Planned Security Improvements

Future enhancements include:

* HTTPS/TLS
* authentication layer
* RBAC
* VPN-secured access
* secret management
* hardened container policies

---

# Scalability Considerations

The current architecture is optimized for:

* single-node deployment
* local infrastructure learning
* operational experimentation

Future scaling paths may include:

* Docker Swarm
* Kubernetes
* distributed monitoring
* external databases
* high availability setups

---

# Future Architecture Improvements

Planned additions include:

* CI/CD pipelines
* GitHub Actions
* automated deployments
* distributed tracing
* Tempo integration
* authentication systems
* backup automation
* infrastructure-as-code
* container orchestration evolution

---

# Architectural Philosophy

InfraForge was built around the idea that:

> understanding infrastructure comes from operating it.

The project emphasizes:

* observability
* operational workflows
* debugging
* infrastructure reasoning
* system interaction
* production-style architecture

through practical implementation rather than purely theoretical learning.

---

# Final Notes

InfraForge continues evolving as a practical infrastructure engineering laboratory focused on:

* modern observability systems
* infrastructure automation
* operational engineering
* platform engineering workflows
* real-world tooling integration

with an emphasis on hands-on operational understanding.
