---
layout: default
sidebar: references
title: "Docker Compose (Telemetry)"
nav_order: 12
permalink: /reference/dockercomposetelemetry
tags: [repodb, telemetry, docker]
parent: REFERENCES
---

# Docker Compose (Telemetry)

---

This page contains the reference `docker-compose.yml` for standing up [RepoDB.Insights](https://repodb.net/tutorial/get-started-telemetry), the companion solution that receives, stores, and visualizes telemetry published by `RepoDb.Telemetry.Default`. See the [Get Started for Telemetry](/tutorial/get-started-telemetry) tutorial for how it fits into the overall setup.

## docker-compose.yml

```yaml
services:
  pgsql:
    image: repodb/insights-postgres:latest
    container_name: pgsql
    environment:
      POSTGRES_PASSWORD: RepoDB2026
    ports:
      - "5432:5432"
    volumes:
      - pgdata:/var/lib/postgresql/data
    networks:
      - repodb

  collector:
    image: repodb/telemetry-default-collector:latest
    container_name: collector
    environment:
      API_KEY: ${REPODB_API_KEY:-RepoDB2026}
      CONNECTION_STRING: postgresql://postgres:${REPODB_PG_PASSWORD:-RepoDB2026}@pgsql:5432/repodb_insights
    ports:
      - "5000:5000"
    depends_on:
      - pgsql
    networks:
      - repodb

  query:
    image: repodb/telemetry-default-query:latest
    container_name: query
    environment:
      API_KEY: ${REPODB_API_KEY:-RepoDB2026}
      CONNECTION_STRING: postgresql://postgres:${REPODB_PG_PASSWORD:-RepoDB2026}@pgsql:5432/repodb_insights
      DIRECTORY_PATH: /tmp/repodb/telemetry
      ALLOWED_ORIGINS: ${REPODB_VISUALIZATION_ORIGIN:-http://localhost:3000}
    volumes:
      - telemetry_data:/tmp/repodb/telemetry
    ports:
      - "5001:5001"
    depends_on:
      - pgsql
    networks:
      - repodb

  filedatasinker:
    image: repodb/telemetry-default-filedatasinker:latest
    container_name: filedatasinker
    cpus: 0.25
    environment:
      CONNECTION_STRING: postgresql://postgres:${REPODB_PG_PASSWORD:-RepoDB2026}@pgsql:5432/repodb_insights
      DIRECTORY_PATH: /tmp/repodb/telemetry
      FREQUENCY_IN_MINUTES: ${REPODB_FILEDATASINKER_FREQUENCY_IN_MINUTES:-60}
    volumes:
      - telemetry_data:/tmp/repodb/telemetry
    depends_on:
      - pgsql
    networks:
      - repodb

  purger:
    image: repodb/telemetry-default-purger:latest
    container_name: purger
    cpus: 0.25
    environment:
      CONNECTION_STRING: postgresql://postgres:${REPODB_PG_PASSWORD:-RepoDB2026}@pgsql:5432/repodb_insights
      RETENTION_PERIOD_IN_MINUTES: ${REPODB_PURGER_RETENTION_PERIOD_IN_MINUTES:-10080}
      FREQUENCY_IN_MINUTES: ${REPODB_PURGER_FREQUENCY_IN_MINUTES:-5}
    depends_on:
      - pgsql
    networks:
      - repodb

  visualization:
    image: repodb/insights-visualization:latest
    container_name: visualization
    environment:
      GF_SECURITY_ADMIN_PASSWORD: ${GF_SECURITY_ADMIN_PASSWORD:-RepoDB2026}
      REPODB_PG_HOST: ${REPODB_PG_HOST:-pgsql}
      REPODB_PG_PORT: ${REPODB_PG_PORT:-5432}
      REPODB_PG_PASSWORD: ${REPODB_PG_PASSWORD:-RepoDB2026}
      REPODB_COMPANY_NAME: ${REPODB_COMPANY_NAME:-Your Company Name}
      REPODB_COMPANY_LOGO: ${REPODB_COMPANY_LOGO:-https://raw.githubusercontent.com/mikependon/RepoDB/refs/heads/master/128x128.png}
      REPODB_API_KEY: ${REPODB_API_KEY:-RepoDB2026}
    ports:
      - "3000:3000"
    depends_on:
      - pgsql
    networks:
      - repodb

volumes:
  pgdata:
  telemetry_data:

networks:
  repodb:
```

{: .note }
> Everything above runs with local defaults (`RepoDB2026` as the shared password/API key) meant for trying things out, not production. Override them with environment variables — e.g. in a `.env` file next to `docker-compose.yml` — before deploying anywhere real: `REPODB_PG_PASSWORD`, `REPODB_API_KEY`, `GF_SECURITY_ADMIN_PASSWORD`, `REPODB_COMPANY_NAME`, `REPODB_COMPANY_LOGO`.

## Usage

Save the file as `docker-compose.yml` and run:

```bash
docker compose up -d
```

| Service | Port | Purpose |
|---|---|---|
| `pgsql` | `5432` | Database |
| `collector` | `5000` | Telemetry Collector API |
| `query` | `5001` | Telemetry Query API |
| `filedatasinker` | — | Archives old telemetry to Parquet (no exposed port) |
| `purger` | — | Deletes expired telemetry (no exposed port) |
| `visualization` | `3000` | Grafana dashboards |
