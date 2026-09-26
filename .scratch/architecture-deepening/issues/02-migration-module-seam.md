# 02: Give the migration module a seam instead of global reads

**Status:** needs-triage
**Type:** deepening-candidate
**Strength:** Strong
**Dependency category:** ports & adapters
**Source:** `/improve-codebase-architecture` 2026-09-25（`../architecture-review-20260925.html#c2`）

**Files:**
- `internal/infra/migration/migration.go:16-21` — `!migration` build-tag stub
- `internal/infra/migration/migration_embed.go:28-91` — `migration` build-tag 实现
- `internal/internal.go:14-18` — `Initial()` 调用点
- `cmd/migrate/migrate.go:32, :40-43` — CLI 调用点

## Problem

`Run() error` 不接任何入参，直接伸手读进程级 feature registry（`cfg.If`，`migration.go:17`、`migration_embed.go:29`）与约 40 个 conf 全局（`conf.MysqlSetting`，`migration_embed.go:44`）。结果：

- enabled 分支（`cfg.If("Migration")` 为真）与 unsupported 分支都无法从 interface 触发。
- 没有任何测试；两个 build-tag 变体只能靠编译开关切换，不能用测试驱动。

`!migration` stub 本身 earned its keep（`internal.Initial` 无条件 import 它），所以这个 seam 是真实的，只是浅。

## Solution

把 migration 做成一个接收 **engine descriptor**（见候选 01）与 embedded source、返回 typed outcome 的 module；两个 build-tag 变体成为同一 interface 的两个 adapter。caller 不再依赖全局注册表。

## Deletion test

删掉 `!migration` stub 会让 `internal.Initial` 无法编译（它无条件 import migration）——seam 真实；deepening 之后 enablement 与依赖集中在 module 内，而非散在 caller 与全局。

## Before / After

- **Before:** `serve` 与 `migrate cmd` → `Run()` → 隐藏地读 `cfg`/`conf` 全局 → backing service（泄漏）。
- **After:** caller → migration module（outcome）→ adapter(descriptor, source)；两个 build-tag adapter 在同一 seam。

## 关联：Migration 触发与错误策略也应集中

同一处 friction 的邻接问题（可作为本候选的一部分，也可独立）：

- `cmd/migrate/migrate.go:32` 用全局突变 `conf.Initial([]string{"Migration"}, false)` 打开 feature。
- `internal/internal.go:16-18` 对失败只 `logrus.Errorf` 后继续；`cmd/migrate/migrate.go:40-43` 对失败 `os.Exit(1)`——同一失败两种策略，散在四文件。
- migration 在触库前还需要一个无关的 `JWT.Secret`（`internal/conf/conf.go:154-157` 的 `os.Exit(1)`），server-only 的配置校验挡在了共享 bootstrap 路径上。

## Wins

- interface 即 test surface
- 两个 build-tag adapter 证明 seam 真实
- caller 各自决定失败策略
- locality：enablement 只读一处

## Related

`CONTEXT.md` 的 **Migration feature**、**Migration build tag**；ADR 0002；候选 01（engine descriptor）。
