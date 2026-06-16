# Deployment

ChaCC API can run as a Docker container, as part of Docker Compose, or as a
standalone Linux service.

## Docker

Build and run a local image:

```bash
docker build -f deployment/docker/Dockerfile -t chacc-api:latest .
docker run -d -p 8080:8080 chacc-api:latest
```

The Docker image installs `chacc-api` from PyPI and starts the server with:

```bash
chacc run server
```

## Docker Compose

Use the production compose file:

```bash
docker compose -f deployment/docker/docker-compose.yml up -d
```

The compose stack includes:

- ChaCC API on port `8080`.
- PostgreSQL 15.
- Persistent PostgreSQL data volume.
- Persistent module storage volumes.
- Health checks for API and database readiness.

Example environment:

```bash
DB_PASSWORD=change-me
SECRET_KEY=replace-with-a-strong-random-secret
ADMIN_PASSWORD=replace-with-a-strong-admin-password
```

## Standalone Linux deployment

A deployment script is provided at `deployment/standalone/deploy.sh`.

```bash
sudo bash deployment/standalone/deploy.sh
```

The script:

1. Creates the `chacc-api` user.
2. Installs Python 3 if needed.
3. Creates a virtual environment under `/opt/chacc-api`.
4. Installs `chacc-api` from PyPI.
5. Writes a production `.env` file.
6. Installs a systemd service.
7. Starts the service.

Access the API at:

```text
http://localhost:8080
```

Check service logs:

```bash
journalctl -u chacc-api -f
```

## Health checks

| Endpoint | Use |
| --- | --- |
| `/health` | Basic service health. |
| `/health/ready` | Readiness including database connectivity. |
| `/health/live` | Lightweight process liveness. |

Container health checks can call:

```bash
python -c "import urllib.request; urllib.request.urlopen('http://localhost:8080/health')"
```

## Production environment

Recommended production settings:

```bash
CHACC_DEV_MODE=false
DATABASE_ENGINE=postgresql
DATABASE_HOST=localhost
DATABASE_PORT=5432
DATABASE_USER=chacc
DATABASE_NAME=chacc
DATABASE_PASSWORD=<strong-password>
SECRET_KEY=<random-32-plus-character-secret>
ENABLE_PLUGIN_HOT_RELOAD=false
PLUGIN_AUTO_DISCOVERY=false
ENABLE_PLUGIN_DEPENDENCY_RESOLUTION=false
MIGRATION_MODE=auto
MIGRATION_BACKUP=true
MIGRATION_BACKUP_DIR=backups
LOG_LEVEL=INFO
```

## Module persistence

In containerized deployments, persist these directories if modules are installed
at runtime:

- `.modules_installed/`
- `.modules_loaded/`
- `.chacc_cache/`
- `backups/`

Without persistence, installed modules may be lost when the container is
recreated.
