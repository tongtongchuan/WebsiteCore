# 08: Make the sitesetting Definition table the single source

**Status:** needs-triage
**Type:** deepening-candidate
**Strength:** Worth exploring
**Dependency category:** in-process
**Source:** `/improve-codebase-architecture` 2026-09-25（`../architecture-review-20260925.html#c8`）

**Files:**
- `internal/sitesetting/registry.go:34-49` — snapshot struct
- `internal/sitesetting/registry.go:192-262` — fill 函数（`bootstrapSnapshot` 等）
- `internal/sitesetting/registry.go:270-363` — `Definition` 表
- `internal/sitesetting/service.go:22-37, :111-131, :133-157, :420-452` — `EditableProfile` / `GetProfile` / `UpdateEditableProfile` / `validateProfileInput`

## Problem

加一个 setting 要同时改 snapshot struct、fill 函数、`Definition` 表，再加上 `EditableProfile`、`GetProfile`、`UpdateEditableProfile`、`validateProfileInput`——add-a-setting 的 interface 十分浅，同一份字段知识重复七处。

## Solution

让 `Definition` 表成为单一来源；snapshot、profile 映射与校验都从它派生。

## Deletion test

删 `bootstrapSnapshot`：若把表做成权威，它的镜像工作 **concentrates** 进表；否则会 **reappear** 在其余六个站点——两种结局的差别正是这个候选要拿下的。

## Before / After

- **Before:** `conf ⇄ bootstrapSnapshot ⇄ Registry ⇄ GetProfile ⇄ EditableProfile`，每个 box 都镜像同一份字段知识（红）。
- **After:** `Registry (authoritative) → snapshot / profile / validation`。

## Wins

- 加 setting 从 7 处降到 1 处
- deletion test：镜像 concentrate 进表
- 唯一有测试的 module（`internal/sitesetting/service_test.go`），可放大 leverage

## Related

`internal/sitesetting/registry.go` 在近 100 次提交里改动 6 次，是 hot spot。
