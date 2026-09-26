# 03: make migrate 建库（实现 cmd/migrate）

**What to build:** 空库 → 有完整 schema，一条 `make migrate` 完成，不再依赖过时 bootstrap SQL。

**Blocked by:** 02 (migrate 读 sample 派生的 config.yaml，必须已指向 compose 的 Postgres)

**Status:** ready-for-agent

- [ ] `cmd/migrate` 从空壳（当前只打印 "not implemented"）改为：强制打开 `Migration` feature → 执行迁移 → 退出，且**不启动任何服务**
- [ ] Makefile 新增 `migrate` 目标，带 `migration` build tag 调用该命令
- [ ] 验证：`make deps-up && make migrate` 在全新库上建出 27 张 `p_` 表、`p_schema_migrations` 版本 21、`p_user.password` 宽度 255
- [ ] 普通 `make run`（sample 不含 Migration）**不**自动迁移
- [ ] 显式打开 Migration feature 时的 boot 迁移路径仍可用（未被破坏）
- [ ] 重复 `make migrate` 幂等（无新变更时 no-op，不报错）
