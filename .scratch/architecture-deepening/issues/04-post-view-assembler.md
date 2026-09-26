# 04: One post-view assembler; retire the MergePosts / RevampPosts twins

**Status:** needs-triage
**Type:** deepening-candidate
**Strength:** Strong
**Dependency category:** in-process
**Source:** `/improve-codebase-architecture` 2026-09-25（`../architecture-review-20260925.html#c4`）

**Files:**
- `internal/dao/jinzhu/tweets.go:83-163` — `MergePosts` / `RevampPosts`
- `internal/servants/base/base.go:305-362` — `PrepareTweets`
- `internal/servants/web/loose.go:109-118, :233-242, :272-281, :314-323, :523-549`
- `internal/servants/web/core.go:192-197, :257-262`；身份映射 `:64-80`
- `internal/model/web/core.go:32-46` — `PostFormated` / `UserInfoResp`
- `internal/servants/web/admin.go:147-157, :168-178`
- `internal/servants/web/loose.go:354-369`；`internal/servants/web/chat.go:131-137`

## Problem

`list` / `detail` 视图的组装是一条 shallow wrapper，并没有减少 caller 需要知道的东西：

- `MergePosts`（`tweets.go:83`）与 `RevampPosts`（`tweets.go:126`）是 near-twin；`GetTweetBy` / `TweetDetail` 又手写一遍同样的组装。
- `UserInfoResp` 由同一组 `RoleList` / `IdentityOf` / `MaskPhone` 在 `core.go:64-80`、`loose.go:354-369`、`admin.go:147-157`、`admin.go:168-178`、`chat.go:131-137` 反复重映射。

## Solution

一个深的 view assembler 接收 `(viewer, posts|user)`，一次返回格式化后的形状，取代 `MergePosts`/`RevampPosts` 双胞胎与各处的 `PrepareTweets` 排序 + 身份字段重映射。

## Deletion test

**concentrates** — 删 `RevampPosts`，复杂度并入其 twin；删 `GetTweetBy`，同样的组装在 `TweetDetail` 内联重现。

## Before / After

- **Before:** `{core, loose, audit, trends} → MergePosts/RevampPosts → PrepareTweets`，身份字段在另外 4 处重映射（红）。
- **After:** 读站点 → `PostView.Format(viewer, rows)`；twin 与身份映射全在 module 内部。

## Wins

- 删 `RevampPosts` → 复杂度并入 twin
- 删 `GetTweetBy` → 逻辑在内部
- 身份字段映射收敛一处
- leverage：一个 view，N 个读站点

## Related

`internal/model/web/core.go` 的响应形状；hot spot（`servants/web/*`、`dao/jinzhu/tweets.go`）。
