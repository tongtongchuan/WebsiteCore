# 03: One visibility & audit policy module

**Status:** needs-triage
**Type:** deepening-candidate
**Strength:** Strong
**Dependency category:** in-process
**Source:** `/improve-codebase-architecture` 2026-09-25（`../architecture-review-20260925.html#c3`）

**Files:**
- `internal/servants/base/base.go:258-303` — `CanViewTweet` 等
- `internal/servants/web/loose.go:556-568` — `TweetDetail` 的 switch
- `internal/dao/jinzhu/comments.go:61-70, :135-143` — `addCommentAuditScope` 等 SQL predicate
- `internal/servants/web/priv.go:231, :378, :540, :677, :819, :845, :870, :896`
- `internal/dao/jinzhu/tweets.go:390-406` — `getUserTweets` predicate

## Problem

post 可见性 × audit status × viewer role 的真值表横跨约 10 个 module：servant 侧的 switch、DAO 侧的 SQL predicate、以及内存里的 reply 过滤各推一遍。`internal/servants/web/priv.go:819`、`:845`、`:870`、`:896` 四处各自留着同一条 TODO：

> `// TODO: 使用统一的permission checker来检查权限问题，这里好友可见post就没处理，是bug`

no locality、no seam。好友可见 post 的 bug 在四个调用点重复存在。

## Solution

一个深的 policy module 独占 `CanView` / `CanComment` 裁决：输入 `(viewer, target)`，输出 decision。servant 调用它得到答案；DAO 接收它给出的 **filter 数据**（例如 audit scope / visibility set），而不是自己再推一遍 SQL 规则。

## Deletion test

**concentrates** — 删掉 `base.CanViewTweet`，那条 switch 会在 `TweetDetail`、`DownloadAttachment`、`TweetComments`、`CreateComment`、`CreateCommentReply` 多处重现。

## Before / After

- **Before:** `base.CanViewTweet` ⇄ `loose.TweetDetail` switch ⇄ `comments.addCommentAuditScope` ⇄ `tweets.getUserTweets`，policy 泄漏在各 seam 之间（红）。
- **After:** `ViewerPolicy → { servant answers, DAO filter }`。

## Wins

- 删 `CanViewTweet` → switch 在 5 处重现（concentrates）
- locality：好友可见 bug 只修一处
- interface：`query + viewer → decision`
- DAO 收 filter 数据而非规则

## Related

`CONTEXT.md` 的 domain（feed / topic / comment / user / moderation）。这是 hot spot：`internal/servants/web/*` 在近 100 次提交里改动最多。
