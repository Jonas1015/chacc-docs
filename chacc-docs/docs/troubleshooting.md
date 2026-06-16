# Troubleshooting

## Server will not start

Check environment validation first.

```bash
chacc run server --debug
```

Common causes:

| Symptom | Fix |
| --- | --- |
| `SECRET_KEY is required in production mode` | Set a strong `SECRET_KEY`. |
| `SECRET_KEY must be at least 32 characters` | Use a longer random secret. |
| `SECRET_KEY matches insecure pattern` | Replace default or test-like values. |
| `DATABASE_HOST is required when using PostgreSQL` | Set `DATABASE_HOST`. |
| `DATABASE_USER is required when using PostgreSQL` | Set `DATABASE_USER`. |
| `DATABASE_PASSWORD is required when using PostgreSQL` | Set `DATABASE_PASSWORD`. |
| `DATABASE_NAME is required when using PostgreSQL` | Set `DATABASE_NAME`. |
| `ENABLE_PLUGIN_HOT_RELOAD must be disabled in production` | Set it to `false`. |
| `PLUGIN_AUTO_DISCOVERY must be disabled in production` | Set it to `false`. |

## Module fails to load

Check the logs for the module name and entry point.

Common causes:

| Symptom | Fix |
| --- | --- |
| Invalid `entry_point` | Use `package.module:function` format. |
| Entry point file not found | Confirm the file path in `entry_point` exists. |
| Setup function not found | Export `setup_plugin` or the function named in metadata. |
| Setup function returns wrong type | Return a FastAPI `APIRouter`. |
| Relative import issue | Import from the module package, not from sibling paths. |
| Dependency resolution failed | Add required packages to `requirements.txt`. |

Restart the backbone after installing, enabling, disabling, or uninstalling a
module.

## Migration fails

| Symptom | Fix |
| --- | --- |
| Destructive operation skipped | This is expected in `MIGRATION_MODE=auto`; use `full` only when intentional. |
| Backup failed | Confirm `MIGRATION_BACKUP_DIR` is writable. |
| SQLite table already exists | Usually handled by idempotency; inspect logs for non-duplicate errors. |
| PostgreSQL enum sync skipped | Install `alembic-postgresql-enum`. |
| Migration timeout | Review long-running operations and database locks. |

Preview changes without applying them:

```bash
MIGRATION_MODE=preview chacc run server
```

## Database issues

| Symptom | Fix |
| --- | --- |
| SQLite database missing | Start the server once to create `chaccapi.db`. |
| PostgreSQL connection refused | Confirm host, port, user, password, and database. |
| Readiness check reports database error | Check database credentials and network access. |
| Migration tracker table missing | The runner creates it on startup. |

## Redis issues

If Redis is enabled but unavailable, the backbone logs a warning and continues
without Redis.

Check:

```bash
REDIS_ENABLED=true
REDIS_HOST=localhost
REDIS_PORT=6379
REDIS_DB=0
```

## Deployment issues

| Symptom | Fix |
| --- | --- |
| `chacc deploy` cannot connect | Confirm `CHACC_DEPLOY_URL` points to a running API. |
| Deployment times out | Increase `CHACC_DEPLOY_TIMEOUT`. |
| Deployment returns 401 | Set `CHACC_DEPLOY_API_KEY` to a valid bearer token. |
| Module installed but not active | Restart the remote ChaCC API server. |

## Clean reset

For local development only, stop the server and remove generated state:

```bash
rm -rf .modules_loaded .modules_installed .chacc_cache backups chaccapi.db
```

Do not run this on production data unless you intend to delete the database and
installed modules.
