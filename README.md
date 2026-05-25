# InfraForge
Enterprise-style infrastructure homelab built using Docker, Nginx, monitoring tools, database and automation scripts

![Grafana Documentation](./images/graphana_metrix.png)

## Overview

InfraForge is a personal infrastructure engineering lab designed to simulate real-world operational environments.

The project focuses on:
- Monitoring
- Container orchestration
- Reverse proxying
- Service reliability
- Infrastructure automation
- Security hardening
- Backup management
- Operational troubleshooting

Built primarily on:
- Fedora Linux
- Docker
- Grafana
- Prometheus
- Nginx
- PostgreSQL
- Redis

## Features

- Dockerized infrastructure stack
- Reverse proxy with Nginx
- Real-time monitoring with Grafana + Prometheus
- Uptime monitoring with Uptime Kuma
- PostgreSQL database deployment
- Redis caching service
- Infrastructure health checks
- Automated backup scripts
- Log monitoring
- Secure internal networking
- Service isolation using Docker networks

## Infrastructure Architecture

Users
   ↓
Nginx Reverse Proxy
   ↓
------------------------------------------------
| Grafana | Uptime Kuma | Adminer | APIs |
------------------------------------------------
   ↓
------------------------------------------------
| PostgreSQL | Redis | Monitoring Services |
------------------------------------------------

## Planned Improvements

- CI/CD integration
- Centralized logging
- Alerting system
- Docker Swarm/Kubernetes exploration
- Infrastructure-as-Code
- SSL automation
- VPN access

## Grafana
Grafana is used for visualizing metrics from Prometheus.
![Grafana Documentation](./images/graphana_metrix.png)
![Grafana Documentation](./images/infrastructure_monitoring.png)

## Prometheus
Prometheus is used for collecting and storing metrics.
![Prometheus Documentation](./images/prometheus_cadvisor.png)

## cAdvisor
cAdvisor is used for monitoring container resources.
![cAdvisor Metrics](./images/cadvisor_metrics.png)
![cAdvisor Documentation](./images/cadvisor.png)
![cAdvisor Documentation](./images/memory_cadvisor.png)
![cAdvisor Documentation](./images/cadvisor_cpu.png)


## Uptime Kuma
Uptime Kuma is used for monitoring the uptime of the infrastructure.
![Uptime Kuma Documentation](./images/Uptime_Kuma.png)
![Uptime Kuma Monitoring](./images/Uptime_Kuma_Monitor.png)


## Nginx
Nginx is used as a reverse proxy for the infrastructure services.![Nginx Documentation](./images/nginx.png)
![Nginx Monitoring](./images/add_hosts.png)


# Portainer
Portainer is used for managing Docker containers.![Portainer Documentation](./images/portainer.png)

# Adminer
Adminer is used for managing PostgreSQL databases.![Adminer Documentation](./images/adminer.png)

# Postgres
Postgres is used for managing PostgreSQL databases.![Postgres Documentation](./images/adminer.png)

# Redis
Redis is used for caching and session management.![Redis Documentation](./images/redis.png)


