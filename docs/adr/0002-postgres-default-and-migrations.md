# PostgreSQL is the default database; schema comes from migrations

## Status

accepted

## Context

The tracked `config.yaml.sample` defaulted to MySQL and had no `Postgres:` section. A fresh database was expected to be seeded from `scripts/paopao-postgres.sql`, which predates the bcrypt password migration and is stale. The `make migrate` command that configuration comments referenced did not exist: `cmd/migrate` printed "not implemented", and real migration happened only during `serve` startup, gated by two independent switches.

## Decision

PostgreSQL 18.6 is the default backend: `config.yaml.sample` selects the `Postgres` feature with a `Postgres:` DSN section (host `127.0.0.1:5432`, dev-only credentials) and drops the `MySQL:` block. A fresh database is created by migrations, never by the bootstrap dump. The `Migration` feature is deliberately left **out** of the sample's default feature suite, so an ordinary `make run` does not auto-migrate; instead the stub `cmd/migrate` is implemented to force the `Migration` feature on, run the migrations, and exit, exposed as `make migrate`. Startup migration remains available to anyone who adds the feature.

## Considered Options

- **Keep MySQL as the default** — rejected: the team standardises on PostgreSQL.
- **Enable `Migration` in the sample** — rejected: it would make every `make run` migrate; an explicit `make migrate` is clearer for onboarding.
- **A one-shot migrator container in compose** — rejected: compose should only start clean backing services; schema setup stays host-side.

## Consequences

- `TAGS='migration'` alone never runs migrations — it only compiles migration support in; the `Migration` feature must also be on.
- A developer's onboarding is `make deps-up` → `make migrate` → `make run TAGS='embed'`.
- `docs/INSTALL*.md` and `docs/deploy/local/` must stop pointing at `scripts/paopao-*.sql` and manual `docker run`.
