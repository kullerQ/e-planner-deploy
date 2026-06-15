# E-Planner - Deployment

Run the full E-Planner stack (PostgreSQL + backend API + frontend) from prebuilt
Docker images.

## Prerequisites

- [Docker Desktop](https://www.docker.com/products/docker-desktop/) (or Docker Engine + Compose v2)

## Quick start (3 steps)

```bash
# 1. Create your environment file from the template
cp .env.example .env        # Windows PowerShell: copy .env.example .env

# [Optional for testing]
# 2. Edit .env and set the secrets (JWT_SECRET / E_PLANNER_BACKEND_AUTH__JWT_SECRET_KEY).

# 3. Start everything
docker compose up -d
```

Then open the app:

- Frontend: http://localhost:3000
- Backend API: http://localhost:8000

The first run pulls the images and applies database migrations automatically
(the `migrator` service runs before the backend starts).

## Common commands

```bash
docker compose ps              # show service status
docker compose logs -f         # follow logs (Ctrl+C to stop following)
docker compose down            # stop and remove containers (keeps the database)
docker compose down -v         # stop and ALSO delete the database volume (full reset)
docker compose pull            # fetch newer images for the current versions
```

## Ports

| Service  | Container | Host (default) | Override in `.env` |
| -------- | --------- | -------------- | ------------------ |
| frontend | 3000      | 3000           | `FRONTEND_PORT`    |
| backend  | 8000      | 8000           | (fixed)            |
| db       | 5432      | 5432           | (fixed)            |

## Gotchas

- **Shared JWT secret.** `JWT_SECRET` (frontend) and
  `E_PLANNER_BACKEND_AUTH__JWT_SECRET_KEY` (backend) must be identical, or login
  tokens issued by the backend will be rejected by the frontend. Both live in the
  single `.env` file.

- **CORS.** `E_PLANNER_BACKEND_CORS_ORIGINS` must contain the origin the browser
  uses to load the frontend (e.g. `["http://localhost:3000"]` for local use). If
  you expose the app on another host/port, add that origin here.

- **Migrations.** Schema migrations run automatically via the `migrator` service.
  If the backend keeps restarting on first boot, check `docker compose logs migrator`.

## What runs

```text
db        PostgreSQL 18 (data persisted in the e_planner_backend-db-data volume)
migrator  applies Alembic migrations, then exits
backend   FastAPI API on :8000  (ghcr.io/kullerq/e-planner-backend)
frontend  Next.js app on :3000  (ghcr.io/kullerq/e-planner-frontend)
```
