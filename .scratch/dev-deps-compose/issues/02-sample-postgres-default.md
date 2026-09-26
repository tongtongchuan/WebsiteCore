# 02: sample 默认切到 Postgres

**What to build:** 新 checkout 的 tracked bootstrap config 直接以 PostgreSQL 为默认后端，且连接参数与 dev compose 起的服务一致。

**Blocked by:** 01 (需先确定 compose 的服务名、端口与凭据)

**Status:** ready-for-agent

- [ ] `config.yaml.sample` 的 `Features.Default` 由 `MySQL` 改为 `Postgres`，保留 `Web`/`Frontend:EmbedWeb`/`Meili`/`LocalOSS`/`BigCacheIndex`/`LoggerFile`，**不加** `Migration`
- [ ] 删除 `MySQL:` 段
- [ ] 新增 `Postgres:` 段（free-form DSN map）：`Host: 127.0.0.1`、`Port: 5432`、`User: paopao`、`Password: paopao`、`Dbname: websitecore`、`sslmode: disable`
- [ ] `Password` 行加一行注释，明示这是仅本地开发用的弱口令
- [ ] Meili key 仍为 `paopao-meilisearch`，与 compose 一致；JWT `Secret` 等必填占位保持不变
- [ ] 验证：`cp config.yaml.sample config.yaml` 后应用选中 Postgres 后端（连接在 schema 建好前可失败，但不能再走 MySQL）
