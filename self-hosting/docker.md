---
title: Deploy with Docker
description: Build and run ZAR as a container, with or without docker-compose.
---

# Deploy with Docker

ZAR ships a `Dockerfile` based on `python:3.12-slim`. It installs the system libraries tree-sitter and cryptography need, then runs the FastAPI app with uvicorn.

## Build

```bash
docker build -t zar .
```

## Run

Pass configuration as environment variables. The private key is easiest to inject inline:

```bash
docker run -p 8000:8000 \
  -e GITHUB_APP_ID=123456 \
  -e GITHUB_WEBHOOK_SECRET=your-strong-secret \
  -e GITHUB_PRIVATE_KEY="$(cat ./github-app.private-key.pem)" \
  -e ANTHROPIC_API_KEY=sk-ant-... \
  -e APP_ENV=production \
  -e SESSION_SECRET="$(python -c 'import secrets; print(secrets.token_hex(32))')" \
  zar
```

The container serves on port `8000` (`uvicorn app.main:app --host 0.0.0.0 --port 8000`). Put it behind a TLS-terminating reverse proxy and point your GitHub App's Webhook URL at `https://YOUR_DOMAIN/github/webhook`.

By default this uses SQLite inside the container, which is **ephemeral** — fine for a trial, not for production. For anything real, mount a volume or use Postgres (below).

## docker-compose

The repo includes a `docker-compose.yml` with a Redis service. A practical compose setup adds Postgres and your env file:

```yaml
services:
  zar:
    build: .
    ports:
      - "8000:8000"
    env_file: .env
    environment:
      DATABASE_URL: postgresql+asyncpg://zar:zar@db:5432/zar
      REDIS_URL: redis://redis:6379/0
    depends_on:
      - db
      - redis

  db:
    image: postgres:16-alpine
    environment:
      POSTGRES_USER: zar
      POSTGRES_PASSWORD: zar
      POSTGRES_DB: zar
    volumes:
      - pgdata:/var/lib/postgresql/data

  redis:
    image: redis:7-alpine
    volumes:
      - redis-data:/data

volumes:
  pgdata:
  redis-data:
```

Run it:

```bash
docker compose up --build
```

Keep secrets in `.env` (referenced via `env_file`) and **never commit it**.

## Persistence & scaling

- Use **Postgres** for production; SQLite in a container is lost on redeploy unless you mount a volume.
- If you run more than one ZAR replica, add a shared lock so replicas don't double-process a repo's events. See the [scaling note](/self-hosting/overview#scaling-note).

## Verify

```bash
curl -s http://localhost:8000/healthz
# {"ok": true}
```

## Next steps

- **[Self-hosting overview](/self-hosting/overview)** — registering the GitHub App.
- **[Environment variables](/reference/environment-variables)** — full configuration.
- **[Railway](/self-hosting/railway)** — a managed alternative.
