# 01: dev compose 依赖栈 + deps-* 目标

**What to build:** 本地开发一条命令起齐后端依赖（PostgreSQL、Redis、Meilisearch），并有一套 `deps-*` 目标管理其生命周期。这是后续所有 onboarding 的基础。

**Blocked by:** None (can start immediately)

**Status:** ready-for-agent

- [ ] 新增 root `docker-compose.dev.yml`，compose project 与命名卷名为 `websitecore`（卷 `websitecore-pgdata` / `websitecore-redisdata` / `websitecore-meilidata`），不写 `container_name`
- [ ] 服务只有 `postgres` / `redis` / `meilisearch`；**不含**应用、MySQL、MinIO、UI，也不提供 mysql profile
- [ ] 镜像 pin 确切 tag：`postgres:18.6`（Debian）、`redis:7.4.11`（Debian）、`getmeili/meilisearch:v1.54.0`
- [ ] 端口只发布到 `127.0.0.1`（`127.0.0.1:5432|6379|7700`）
- [ ] 数据用命名卷：pg 挂 `/var/lib/postgresql`（PG18 PGDATA 变更）、redis 挂 `/data`、meili 挂 `/meili_data`
- [ ] 三个服务都有 healthcheck（`pg_isready` / `redis-cli ping` / Meili 健康端点）
- [ ] Makefile 新增 `deps-up`（`up -d --wait`，等 healthcheck 通过才返回）、`deps-down`（停但保留卷）、`deps-logs`、`deps-status`、`deps-reset`（`down -v` 清库）
- [x] ~~`.github/dependabot.yml` 增加 `docker` ecosystem，指向 `docker-compose.dev.yml`（否则确切 pin 会腐化）~~ **暂不适用**：本 fork 未启用 dependabot、配置从 upstream 继承且 `target-branch: "dev"` 不存在，docker 条目不会产出 PR；已撤下该条目，启用 dependabot 另记为 `.scratch/dependabot/issues/01-inherited-config-inert-on-fork.md`
- [ ] 验证：干净环境 `make deps-up` 返回后三服务 healthy；`make deps-down` 后卷仍在；`make deps-reset && make deps-up` 幂等
