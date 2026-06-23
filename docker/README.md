# DanKai Local Dev Stack

A local development environment running three services via Docker Compose on WSL2.

## Services

| Service | What it does |
|---|---|
| `db` | Postgres 16 database — stores n8n workflows and data |
| `adminer` | Web UI to browse and query the database |
| `n8n` | Workflow automation engine connected to Postgres |

All services run on a private Docker network and communicate using service names as hostnames.

## How to start

First time:
```bash
docker compose up -d
```
Already created but stopped:
```bash
docker compose start
```

## How to stop

```bash
docker compose stop
```

## Data persistence

Data lives in Docker volumes, separate from containers. Survives `docker compose down`. Only `docker compose down -v` deletes data permanently.

## Access

| Service | URL |
|---|---|
| n8n | http://localhost:5678 |
| Adminer | http://localhost:8080 |
