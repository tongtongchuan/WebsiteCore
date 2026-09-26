# 07: One cache-key module owns write and invalidation

**Status:** needs-triage
**Type:** deepening-candidate
**Strength:** Worth exploring
**Dependency category:** in-process
**Source:** `/improve-codebase-architecture` 2026-09-25（`../architecture-review-20260925.html#c7`）

**Files:**
- `internal/servants/web/core.go:386`
- `internal/servants/web/loose.go:139-209`
- `internal/servants/web/trends.go:57`
- `internal/servants/web/events.go:242-246, :282, :320-328`
- `internal/servants/web/followship.go:86-89, :113-116`

## Problem

每个 servant 手拼 raw prefix + format key，而失效逻辑在 `events.go` 里又把同一批 pattern 硬编码一遍；`internal/servants/web/followship.go:85` 与 `:112` 还注明 event fan-out 待合并。写 key 与过期 key 之间没有 locality。

## Solution

一个深的 cache-key module 拥有 key 构造并暴露失效操作，caller 永不手写 pattern。

## Deletion test

**concentrates** — 删掉 key-builder helper，format string 会在每个 servant 加两个 event 文件里重现。

## Before / After

- **Before:** `servant → raw fmt key → AppCache`，`event → raw fmt pattern → AppCache`（同一语法两处，红）。
- **After:** `servant/event → KeySpace → AppCache`。

## Wins

- 删 helper → format string 在 5 处重现（concentrates）
- locality：写与失效同一处
- interface：操作，不是字符串

## Related

`internal/servants/web/priv.go:294, :326` 另有「缓存逻辑合并处理」TODO，属同一摩擦面。
