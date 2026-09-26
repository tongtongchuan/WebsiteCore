# 01: Collapse four DB-engine cascades into one engine-descriptor module

**Status:** needs-triage
**Type:** deepening-candidate
**Strength:** Strong
**Dependency category:** ports & adapters
**Source:** `/improve-codebase-architecture` 2026-09-25（`../architecture-review-20260925.html#c1`）

**Files:**
- `internal/conf/db.go:70-83` — `newSqlDB` cascade
- `internal/conf/db_gorm.go:72-84` — GORM dialector cascade
- `internal/infra/migration/migration_embed.go:42-53` — open-DB cascade
- `internal/infra/migration/migration_embed.go:59-71` — pick migration driver cascade
- 对照真实的 two-adapter seam：`internal/conf/db_cgo.go:21-28` / `internal/conf/db_nocgo.go:22-29`

## Problem

同一个 feature-suite → driver / DSN / dialector / migration-source 映射在四个 seam 各写一遍，且已经分叉：

- `internal/conf/db_gorm.go:75` 只认 `cfg.If("Postgres")`，而 `internal/conf/db.go:74` 与 `internal/infra/migration/migration_embed.go:45` 还接受 `"PostgreSQL"` 别名——用 `"PostgreSQL"` 拼写时 GORM 会落到 `else` 分支（`db_gorm.go:81-83` 的 MySQL fallback），与 SQL 侧选择不一致。
- `internal/infra/migration/migration_embed.go:49` 用 `_, db, err = conf.OpenSqlite3()` 丢弃了它返回的 driver（`conf.OpenSqlite3` 返回 `"sqlite3"`），随后 `dbName` 保持空串传进 `:81`。

engine 知识没有 locality：任一处改动都要在四处同步，且已经漏同步。

## Solution

让一个 module 独占 engine 选择，产出一个 **descriptor**（SQL driver、DSN、GORM dialector、migration source 目录、migration DB driver）。`db.go`、`db_gorm.go`、`migration_embed.go` 成为薄 adapter，只消费 descriptor。`db_cgo.go`/`db_nocgo.go` 的双 adapter 证明这一带的 seam 是真实的（两个 adapter = real seam）。

## Deletion test

**concentrates** — 删掉任一 cascade，其 consumer 都必须重新自行推导 engine 知识；分叉本身就是没有 locality 的证明。

## Evidence

embedded base `internal/conf/config.yaml:39` 的 `Default: []`（没有任何 DB feature 时），使三处 `else → MySQL` fallback 今天仍可达：

- `internal/conf/db.go:79-82`
- `internal/conf/db_gorm.go:81-83`
- `internal/infra/migration/migration_embed.go:50-52`

## Before / After

- **Before:** `cfg` → 四个 cascade，各自内嵌 engine 知识（泄漏），分别产出 `sql.DB`、`gorm.DB`、migration source+driver。
- **After:** `cfg` → engine-descriptor module → 薄 SQL / GORM / migration adapter；engine 知识只在一个 box 里。

## Wins

- locality：engine 知识收敛到一处
- leverage：三个 caller 共享一个 descriptor
- 修掉 `"PostgreSQL"` 别名分叉
- 修掉 sqlite driver 被丢弃
- deletion test：concentrates

## Related

ADR 0002（PostgreSQL 为默认、schema 来自迁移）；`CONTEXT.md` 的 **Feature suite**、**Migration build tag**。
