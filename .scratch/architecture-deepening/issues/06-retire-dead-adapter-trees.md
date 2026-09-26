# 06: Retire the dead adapter trees: sakila/slonik and WebDataServantA

**Status:** needs-triage
**Type:** deepening-candidate
**Strength:** Strong
**Dependency category:** ports & adapters
**Source:** `/improve-codebase-architecture` 2026-09-25（`../architecture-review-20260925.html#c6`）

**Files:**
- `internal/core/core.go:46-54` — `WebDataServantA` interface（内嵌 `TopicServantA`/`TweetServantA`/`TweetManageServantA`/`TweetHelpServantA`）
- `internal/dao/jinzhu/tweets.go:638-736` — 全部 `return nil, debug.ErrNotImplemented`
- `internal/dao/jinzhu/jinzhu.go:44-49, :82-92`
- `internal/dao/dao.go:24, :34-37, :71-89`
- `internal/servants/base/base.go:39, :501` — `Dsa` 字段的构造与赋值
- `internal/dao/sakila/sakila.go:15-27`、`internal/dao/slonik/slonik.go:15-27` — `logrus.Fatal("not support now")`

## Problem

整条平行的 `WebDataServantA` interface（20+ 方法）全部 `return nil, debug.ErrNotImplemented`（`tweets.go:638-736`，该文件内 `ErrNotImplemented` 出现 20 次），被构造并挂到每个 `DaoServant`（`base.go:501` 的 `Dsa: dao.WebDataServantA()`）却无人读取——`Dsa` 没有任何 read site。interface 复杂度拉满、behaviour 为零。

`sakila` / `slonik` 更直接：一被选中就 `logrus.Fatal`，是 `dao.go:71-89` 的 switch 里的导航陷阱（one adapter = hypothetical seam）。

## Solution

一个 backend 注册 module；不支持的 backend 在 wiring 时显式返回 error，删掉 `sakila`/`slonik` 两棵 stub 树与重复的 `AuthorizationManageService` cascade。把 `WebDataServantA` 折叠进既有 data interface（`DataService`），或删除，直到出现第二个真 adapter 再拆。

## Deletion test

**pass-through** — 删掉它，implementation seams 消失，没有复杂度在别处重现。

## Before / After

- **Before:** `DaoServant.Dsa → core.WebDataServantA → jinzhu stub tree`（unused seam，红）；`dao.initDsX cfg.If{Gorm|Sqlx|Sqlc} → jinzhu | Fatal | Fatal`。
- **After:** `DaoServant.Ds → core.DataService`；`dao → backend registry → jinzhu`，unsupported 显式报错。

## Wins

- deletion test：pass-through，直接删
- 删 20+ stub 方法
- 去掉 Fatal 型的导航陷阱
- interface 复杂度归零

## Related

`internal/dao/jinzhu/constraint.go:39` 有 `_ core.WebDataServantA = (*webDataSrvA)(nil)` 断言，删除时一并处理。
