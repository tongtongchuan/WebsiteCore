# Local dependencies run in a docker compose stack, separate from production

## Status

accepted

## Context

The repo had no compose file. `docs/deploy/local/` started Meilisearch, MinIO, and Zinc through one-off `docker run` commands, and onboarding required installing a database, a cache, and a search engine by hand. Production deployment is documented per-cloud and via Kubernetes, not compose.

## Decision

Local development's backing services — PostgreSQL, Redis, and Meilisearch — run from a single root `docker-compose.dev.yml` (compose project and volumes named `websitecore`). Only those three: the application, MySQL, and object storage are excluded. Ports bind to `127.0.0.1`, data lives in named volumes, every service has a healthcheck, and `deps-up` waits for them. Images are pinned to exact tags: `postgres:18.6` (Debian, whose glibc matches production/CI), `redis:7.4.11` (the maintained 7.4 line; Redis 7.4 onward is RSALv2/SSPLv1 rather than BSD, which was reviewed and accepted — the project self-hosts an unmodified Redis as a backing service and never resells it as a managed service, so the licence imposes no practical obligation), and `getmeili/meilisearch:v1.54.0` (the 1.x line `meilisearch-go v0.27.2` supports). Dev and production never share a compose file: dev publishes ports and uses throwaway credentials, production needs secrets, restart policies, and images rather than source.

## Considered Options

- **`postgres:18.6-alpine`** — rejected: musl collation differs from glibc and has historically diverged on text-index ordering and constraints.
- **Redis 7.2.16 / Valkey** — rejected: the BSD line (Redis ≤ 7.2, continued by the Linux Foundation's Valkey fork) is cleaner for licence review, but the project never resells Redis as a managed service and never modifies it, so 7.4/8's source-available licences carry no practical obligation; the maintained 7.4 line wins. This is recorded because the pin was initially misread as BSD.
- **A combined dev + prod compose file** — rejected: the two have opposite requirements, and no production compose exists today.
- **A `mysql` profile** — rejected: the team standardises on PostgreSQL; MySQL stays a supported feature but is out of the dev stack.

## Consequences

- `docker-compose.dev.yml` is dev-only and local-only; production keeps its cloud/k8s docs.
- Adding a service to the stack is a change to `docker-compose.dev.yml` alone.
