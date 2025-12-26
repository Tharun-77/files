# n8n Docker Deployment – Architecture Documentation

## Overview
This document explains the **system architecture** for deploying **n8n** on a **Linux VPS** using **Docker and Docker Compose**, designed to operate reliably alongside **cPanel-managed hosting environments**.

The architecture focuses on:
- High availability and automatic recovery
- Data persistence and safety
- Secure service exposure
- Minimal interference with existing cPanel services
- Ease of maintenance and future scalability

---

## High-Level Architecture
User / Browser
|
HTTPS / HTTP
|
Reverse Proxy (Optional)
|
Docker Engine
|
n8n Container
|
Persistent Docker Volume

yaml
Copy code

---

## Component Breakdown

### 1. Client / Browser
- End users access the n8n UI via browser
- Communication occurs over HTTP or HTTPS
- Webhooks from third-party services also target this endpoint

---

### 2. Reverse Proxy (Optional)
**Examples:** Apache or Nginx

**Responsibilities:**
- SSL/TLS termination
- Domain and subdomain routing
- Request forwarding to the n8n container
- Security headers and access control

**Why optional?**
- n8n can be exposed directly via a port
- Reverse proxy is recommended for production-grade security and SSL

---

### 3. Docker Engine
- Runs independently of cPanel
- Manages container lifecycle
- Ensures isolation between n8n and hosted web services
- Handles restart policies and resource isolation

---

### 4. n8n Container
- Runs the official `n8nio/n8n` Docker image
- Exposes the n8n service on a configurable port
- Reads configuration via environment variables
- Uses Basic Authentication for UI protection

**Key characteristics:**
- Stateless container
- All state is stored in persistent volumes
- Can be recreated or updated without data loss

---

### 5. Persistent Docker Volume
- Stores workflows, credentials, execution history
- Mounted at `/home/node/.n8n`
- Survives container restarts and image updates
- Can be backed up independently

---

## Data Flow

### UI Access
Browser → Reverse Proxy → n8n Container → Docker Volume

shell
Copy code

### Webhook Execution
External Service → Reverse Proxy → n8n Container → Workflow Execution

yaml
Copy code

---

## Restart & Recovery Strategy
- Docker uses `restart: unless-stopped`
- Containers automatically restart on:
  - VPS reboot
  - Docker daemon restart
  - Container crash
- Manual stops are respected

This ensures **high availability without manual intervention**.

---

## Security Architecture
- No secrets stored in source control
- Environment variables used for sensitive configuration
- Basic authentication enabled for n8n UI
- SSL/TLS strongly recommended
- Firewall rules restrict direct port access

---

## cPanel Coexistence Strategy
- Docker services do not interfere with cPanel
- Ports are carefully selected to avoid conflicts
- Reverse proxy can integrate with existing Apache/Nginx
- No impact on hosted websites, email, or DNS

---

## Backup & Disaster Recovery
**Recommended strategy:**
- Regular Docker volume backups
- VPS snapshot backups
- Workflow exports from n8n UI
- Ability to restore container from Docker Compose file

---

## Scalability & Future Enhancements
This architecture can be extended to support:
- Automated SSL renewal
- Monitoring and alerting
- CI/CD-based deployments
- Horizontal scaling using Kubernetes or AWS ECS
- External databases for higher workloads

---

## Design Principles
- Simplicity over complexity
- Stateless containers, stateful volumes
- Infrastructure as Code mindset
- Production-first design
- Easy recovery and maintainability

---

## Author
**Tarun Tej**  
DevOps & Cloud Engineer  
Docker | VPS | System Design | Cloud Infrastructure
