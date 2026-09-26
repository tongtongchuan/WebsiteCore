# 05: Shared search query plan; adapters translate only

**Status:** needs-triage
**Type:** deepening-candidate
**Strength:** Strong
**Dependency category:** ports & adapters
**Source:** `/improve-codebase-architecture` 2026-09-25（`../architecture-review-20260925.html#c5`）

**Files:**
- `internal/dao/search/meili.go:79-95, :97-160` — `Search` dispatch + 三个 `queryBy*`
- `internal/dao/search/zinc.go:75-91, :93-140` — 同一 dispatch 的克隆
- `internal/servants/web/loose.go:44-51` — `SearchType` switch
- `internal/dao/dao.go:116-131` — search adapter 选择

## Problem

每个 adapter 独立重推同一个 content / tag / any 三选一决策（`meili.go:80-86`、`zinc.go:76-82`），并各写一遍三个 query 变体，差异只在 transport。interface 几乎与每个 implementation 一样复杂——shallow，且 dispatch 在 seam 两侧重复。

## Solution

把「选哪个 query 变体 + 构造 backend-neutral 的 query plan」移进一个深 module；每个 adapter 只把 plan 翻成 Meili / Zinc 细节并映射命中。Meili 与 Zinc 两个 adapter 使这个 seam 是真实的（两个 adapter = real seam）。

## Deletion test

**concentrates** — 删掉任一 adapter，重复的 dispatch 会集中进幸存者 + 新的 shared planner；共享 planner 因此 earned its keep。

## Before / After

- **Before:** `loose.SearchType switch → meili.Search / zinc.Search → meili.queryBy*(3) | zinc.queryBy*(3)`，dispatch 泄漏进两个 adapter（红）。
- **After:** `loose → Search dispatch (shared) → QueryPlan → meili adapter | zinc adapter`。

## Wins

- 两个 adapter 是真实 seam
- dispatch 不再是泄漏点
- 新增 backend 只写翻译
- deletion test：concentrates

## Related

`CONTEXT.md` 的 **Backing service**（search engine）；`internal/dao/search/zinc.go` 是 legacy（文档已标）。
