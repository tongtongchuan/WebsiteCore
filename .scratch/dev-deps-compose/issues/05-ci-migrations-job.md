# 05: CI migrations job（第二 PR）

**What to build:** CI 能对迁移文件做 up/down 冒烟，防止某一方言的迁移被改坏而无人发现。按既定决定，本 ticket 单独走第二个 PR，不混进 01–04。

**Blocked by:** 03

**Status:** ready-for-agent

- [ ] 新增 `scripts/test-migrations.sh`：对 `scripts/migration/{postgres,mysql}/` 跑 up → 校验 `p_schema_migrations` 版本 → `down 1` → 再 up，并带 `x-migrations-table=p_schema_migrations` 对齐应用侧
- [ ] Makefile 新增 `test-migrations`（仅覆盖 PostgreSQL + MySQL 两个方言）
- [ ] `.github/workflows/ci.yml` 新增 `migrations` job：service containers 用 `postgres:18.6` + `mysql:8.0`（PG tag 与 compose 一致），步骤 checkout → setup-go → 安装 golang-migrate → `make test-migrations`
- [ ] 验证：job 通过；故意改坏任一方言的迁移文件时 job 失败
- [ ] 明确作为独立 PR 提交，不与 01–04 同 PR
