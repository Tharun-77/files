# n8n Production Deployment on VPS using Docker (cPanel Compatible)

## Overview
This document describes a **production-ready deployment of n8n** using **Docker and Docker Compose** on a **Linux VPS**, designed to work safely alongside **cPanel-managed hosting environments** (e.g., A2 Hosting Managed VPS).

This setup focuses on:
- Reliability and automatic restarts after server reboots
- Data persistence across container restarts
- Secure configuration using environment variables
- Compatibility with managed VPS and cPanel environments
- Clean, maintainable, and well-documented infrastructure

---

## Tech Stack
- n8n (Workflow Automation)
- Docker
- Docker Compose
- Linux VPS
- cPanel (co-existing, non-intrusive setup)
- Optional: Apache / Nginx Reverse Proxy, SSL/TLS

---

## Architecture
User / Browser
|
HTTPS / HTTP
|
Reverse Proxy (optional)
|
Docker Container (n8n)
|
Persistent Docker Volume

yaml
Copy code

### Architecture Notes
- n8n runs inside an isolated Docker container
- Workflow data and credentials are stored in Docker volumes
- Docker restart policies ensure service availability after crashes or reboots
- Ports are managed carefully to avoid conflicts with cPanel services

---

## Prerequisites
- VPS with root or sudo access
- Docker installed
- Docker Compose installed
- Open port for n8n (or reverse proxy configured)
- Optional: Domain or subdomain for SSL

---

## Docker Compose Configuration
Below is a **production-ready Docker Compose configuration** for n8n.

```yaml
version: "3.8"

services:
  n8n:
    image: n8nio/n8n:latest
    container_name: n8n
    restart: unless-stopped
    ports:
      - "5678:5678"
    environment:
      - N8N_HOST=${N8N_HOST}
      - N8N_PORT=${N8N_PORT}
      - N8N_PROTOCOL=${N8N_PROTOCOL}
      - WEBHOOK_URL=${WEBHOOK_URL}
      - N8N_BASIC_AUTH_ACTIVE=${N8N_BASIC_AUTH_ACTIVE}
      - N8N_BASIC_AUTH_USER=${N8N_BASIC_AUTH_USER}
      - N8N_BASIC_AUTH_PASSWORD=${N8N_BASIC_AUTH_PASSWORD}
    volumes:
      - n8n_data:/home/node/.n8n

volumes:
  n8n_data:
Environment Configuration
Create a .env file in the same directory as docker-compose.yml.
Do NOT commit this file to version control.

env
N8N_HOST=automation.example.com
N8N_PORT=5678
N8N_PROTOCOL=https

WEBHOOK_URL=https://automation.example.com/

N8N_BASIC_AUTH_ACTIVE=true
N8N_BASIC_AUTH_USER=admin
N8N_BASIC_AUTH_PASSWORD=strong-password
Starting n8n
Run the following command from the directory containing docker-compose.yml:

bash
docker compose up -d
Verify container status:

bash
docker ps
Access n8n:

cpp
http://<VPS-IP>:5678
or
https://automation.example.com
Auto-Restart & Reliability
This setup uses:

yaml
restart: unless-stopped
This ensures:

n8n restarts automatically after VPS reboots

Containers recover from unexpected crashes

Manual container stops are respected

Data Persistence
All n8n data (workflows, credentials, execution history) is stored in Docker volumes.

Container restarts do not affect data

VPS reboots do not cause data loss

Upgrades are safe when volumes are preserved

cPanel Compatibility
Docker runs independently from cPanel services

Ports are selected to avoid conflicts with existing cPanel services

Reverse proxy can be configured via Apache or Nginx if required

No interference with hosted websites, email, or DNS services

Security Best Practices
Environment variables used for sensitive configuration

No secrets committed to version control

Basic authentication enabled

SSL/TLS strongly recommended for production

Firewall rules can restrict direct access to the n8n port

Updating n8n
To update n8n safely:

bash
docker compose pull
docker compose up -d
Logs & Monitoring
View logs:

bash
docker logs n8n
Check service status:

bash
docker compose ps
Backup Strategy (Recommended)
Regular backups of Docker volumes

Optional snapshot-based VPS backups

Workflow export from n8n UI for redundancy

Maintenance Commands
Restart service:

bash
docker compose restart
Stop service:

bash
docker compose down
Start service again:

bash
docker compose up -d
Author
Tarun Tej
DevOps & Cloud Engineer
Docker | VPS | CI/CD | Cloud Infrastructure
