# InfraForge Troubleshooting Guide

# Overview

This document contains common operational issues encountered while building and maintaining the InfraForge infrastructure stack.

The troubleshooting guide focuses on:

* Docker-related issues
* Grafana configuration problems
* Loki logging integration
* Portainer connectivity
* SELinux restrictions
* Reverse proxy misconfigurations
* Grafana alerting issues
* Infrastructure networking

The purpose of this document is to:

* simplify debugging
* document operational fixes
* provide recovery procedures
* improve infrastructure reliability

---

# Table of Contents

* Grafana Issues
* Loki Issues
* Promtail Issues
* Portainer Issues
* Docker Issues
* Nginx Issues
* SELinux Issues
* Prometheus Issues
* Alerting Issues
* Networking Issues
* Redis Issues
* PostgreSQL Issues
* General Debugging Commands

---

# Grafana Issues

# Grafana Login Redirect Loop

## Problem

After successful login, Grafana redirects back to the login page repeatedly.

---

## Cause

Incorrect `GF_SERVER_ROOT_URL` configuration.

Example of incorrect configuration:

```yaml id="jlwmnf"
GF_SERVER_ROOT_URL=http://grafana.local
```

Grafana requires a trailing slash.

---

## Solution

Update configuration:

```yaml id="jlwmng"
GF_SERVER_ROOT_URL=http://grafana.local/
```

---

## Apply Changes

```bash id="’winih"
docker compose down
docker compose up -d
```

---

# Grafana Datasource "Origin Not Allowed"

## Problem

Adding Loki datasource returns:

```text id="’winii"
403 Forbidden
origin not allowed
```

---

## Cause

Grafana accessed inconsistently using:

* localhost
* grafana.local

This causes origin mismatch validation failures.

---

## Solution

Always access Grafana consistently using:

```text id="uilxqj"
http://grafana.local
```

Avoid mixing:

```text id="jlwmni"
localhost:3000
grafana.local
127.0.0.1
```

---

# Loki Issues

# Loki Datasource Connection Failure

## Problem

Grafana cannot connect to Loki datasource.

---

## Cause

Incorrect datasource URL.

---

## Correct Loki URL

Inside Grafana datasource configuration:

```text id="jlwmnj"
http://loki:3100
```

NOT:

```text id="Ndzi3x"
localhost:3100
```

because Grafana communicates internally through Docker networking.

---

# Promtail Issues

# Promtail Cannot Access Docker Logs

## Problem

Promtail fails to read Docker container logs.

---

## Cause

SELinux restrictions blocking log access.

Common on:

* Fedora
* RHEL
* CentOS

---

## Solution

Add:

```yaml id="jlwmnk"
security_opt:
  - label=disable
```

to the Promtail container configuration.

---

## Restart Stack

```bash id="jlwmnl"
docker compose restart promtail
```

---

# Portainer Issues

# Portainer Cannot Connect to Docker Socket

## Problem

Error:

```text id="jlwmnm"
Permission denied while trying to connect to the Docker daemon socket
```

---

## Cause

Docker socket permissions insufficient.

---

## Solution 1 — Docker Group

Add user to Docker group:

```bash id="jlwmnn"
sudo usermod -aG docker $USER
```

Then logout/login.

---

## Solution 2 — Temporary Permission Fix

```bash id="’winio"
sudo chmod 666 /var/run/docker.sock
```

---

# Portainer Local Environment Not Visible

## Problem

Portainer shows:

```text id="񎓚p"
Environment: None selected
```

---

## Cause

Docker socket not mounted correctly.

---

## Correct Configuration

```yaml id="qmw9lc"
volumes:
  - /var/run/docker.sock:/var/run/docker.sock
```

---

# Docker Issues

# Docker Container Fails to Start

## Debugging Steps

Check running containers:

```bash id="jlwmno"
docker ps
```

Check all containers:

```bash id="jlwmnp"
docker ps -a
```

Inspect logs:

```bash id="jlwmnq"
docker logs <container_name>
```

---

# Docker Compose Service Restart Loop

## Problem

Container repeatedly restarts.

---

## Debugging

Inspect logs:

```bash id="jlwmnr"
docker compose logs -f <service>
```

Check container health:

```bash id="jlwmns"
docker inspect <container_name>
```

---

# Nginx Issues

# Nginx Reverse Proxy Not Routing

## Problem

Service domain inaccessible.

---

## Cause

* incorrect nginx config
* hosts file misconfiguration
* container not running

---

## Verify Hosts File

```bash id="’winit"
cat /etc/hosts
```

Ensure:

```text id="jlwmnt"
127.0.0.1 grafana.local
127.0.0.1 prometheus.local
127.0.0.1 portainer.local
```

---

## Verify Nginx Configuration

```bash id="ullugu"
docker exec -it nginx nginx -t
```

---

## Restart Nginx

```bash id="jlwmnu"
docker compose restart nginx
```

---

# SELinux Issues

# Docker Services Fail Due to SELinux

## Problem

Containers cannot:

* access mounted volumes
* access logs
* access Docker socket

---

## Cause

SELinux policy restrictions.

---

## Solution (Pragmatic Homelab Fix)

Disable SELinux labeling for affected services:

```yaml id="wlv3jz"
security_opt:
  - label=disable
```

---

## Verify SELinux Status

```bash id="jlwmnv"
getenforce
```

---

# Prometheus Issues

# Prometheus Targets Down

## Problem

Targets show:

```text id="jlwmnw"
DOWN
```

---

## Debugging

Open:

```text id="jlwmnx"
http://prometheus.local/targets
```

---

## Common Causes

* container not running
* wrong scrape target
* networking issue
* exporter unavailable

---

## Verify Container

```bash id="jlwmny"
docker ps
```

---

## Verify Networking

```bash id="Ndzi4a"
docker network inspect infraforge
```

---

# Alerting Issues

# Grafana Alert Returns "DatasourceNoData"

## Problem

Alert state:

```text id="Ndzi4b"
DatasourceNoData
```

---

## Cause

Alert query returned empty results.

---

## Solution

Verify query inside:

```text id="Ndzi4c"
Grafana → Explore
```

Test query:

```promql id="Ndzi4d"
up
```

---

# Alert Spam / Excessive Alerts

## Problem

Thousands of alerts generated.

---

## Cause

Broad PromQL queries.

Example problematic query:

```promql id="Ndzi4e"
container_last_seen
```

This matches:

* systemd slices
* host services
* containers
* scopes

---

## Better Query

```promql id="Ndzi4f"
up{job="cadvisor"} == 0
```

---

# Networking Issues

# Containers Cannot Communicate

## Debugging

Verify Docker network:

```bash id="Ndzi4g"
docker network ls
```

Inspect network:

```bash id="Ndzi4h"
docker network inspect infraforge
```

---

## Test Internal DNS Resolution

```bash id="Ndzi4i"
docker exec -it grafana ping prometheus
```

---

# Redis Issues

# Redis Persistence Not Working

## Verify Append Only Mode

Inside Redis container:

```bash id="Ndzi4j"
redis-cli CONFIG GET appendonly
```

Expected:

```text id="Ndzi4k"
appendonly
yes
```

---

# PostgreSQL Issues

# Cannot Connect to PostgreSQL

## Verify Container

```bash id="Ndzi4l"
docker ps
```

---

## Verify PostgreSQL Logs

```bash id="Ndzi4m"
docker logs postgres
```

---

## Test Connection

```bash id="Ndzi4n"
docker exec -it postgres psql -U postgres
```

---

# General Debugging Commands

# Docker Logs

```bash id="Ndzi4o"
docker logs <container>
```

---

# Follow Logs

```bash id="Ndzi4p"
docker logs -f <container>
```

---

# Docker Compose Logs

```bash id="Ndzi4q"
docker compose logs -f
```

---

# Restart Entire Stack

```bash id="Ndzi4r"
docker compose down
docker compose up -d
```

---

# Remove Stopped Containers

```bash id="Ndzi4s"
docker container prune
```

---

# Verify Running Containers

```bash id="Ndzi4t"
docker ps
```

---

# Verify Open Ports

```bash id="Ndzi4u"
ss -tulpn
```

---

# Infrastructure Recovery Workflow

When debugging infrastructure failures:

## Step 1

Verify containers are running.

```bash id="Ndzi4v"
docker ps
```

---

## Step 2

Inspect logs.

```bash id="Ndzi4w"
docker logs <container>
```

---

## Step 3

Verify networking.

```bash id="Ndzi4x"
docker network inspect infraforge
```

---

## Step 4

Verify reverse proxy.

```bash id="Ndzi4y"
docker exec nginx nginx -t
```

---

## Step 5

Verify observability systems.

Check:

* Grafana
* Prometheus
* Loki
* Alerting

---

# Final Notes

Troubleshooting infrastructure systems is a core part of:

* Infrastructure Engineering
* DevOps
* Platform Engineering
* Site Reliability Engineering

InfraForge intentionally embraces operational debugging as part of the learning process.

Most issues encountered during development were related to:

* networking
* reverse proxying
* observability pipelines
* permissions
* container communication
* SELinux restrictions

Understanding how to systematically debug these problems is one of the most valuable infrastructure engineering skills.
