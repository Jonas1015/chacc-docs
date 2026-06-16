# Changelog

## 1.0.0-b4

### Added

- GUID `TypeDecorator` for cross-database UUID support: PostgreSQL native UUID
  and SQLite text storage.
- SQLite batch operation support for constraints, indexes, and columns through
  `batch_alter_table()`.
- Automatic `.env` creation from `.env.sample` on application startup.
- `.env.sample` included in the package distribution.
- Migration version counter suffix to prevent duplicate migration versions.
- Database engine detection to distinguish PostgreSQL from other engines.

### Fixed

- Migration tracker `rollback_available` column conversion for PostgreSQL and
  SQLite compatibility.
- Migration runner now uses `run_in_executor()` for synchronous database
  operations.
- Tracker and backup properties are lazy to defer database initialization.
- `ProgrammingError` and `OperationalError` for existing resources are caught to
  make migrations idempotent.
- GUID values now return `uuid.UUID` instances directly.
- Tracker table is filtered from migration detection to prevent accidental drops.
- Requirements path handling for installed package layout.

### Removed

- Redundant database type conversion in the GUID `TypeDecorator`.

> Caution: users migrating to `1.0.0-b4` should start from a clean database. The
> tracker column type and GUID column changes require a fresh schema migration.

## 1.0.0-b3

### Added

- Automatic `.env` creation from `.env.sample` when `.env` is missing.
- `.env.sample` included in package distribution.

### Fixed

- Improved migration engine database detection for default database and
  PostgreSQL database configurations.
