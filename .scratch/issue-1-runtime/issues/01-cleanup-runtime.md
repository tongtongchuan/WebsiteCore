# Issue #1: remaining runtime cleanup

Status: resolved
Owner: Codex / cleanup/issue-1-runtime
Source: https://github.com/BZYA-Community/WebsiteCore/issues/1
Baseline: 91e69747

## Scope

- 删除旧独立 Admin 后台服务（m/v1 空壳）
- 删除服务 SpaceX / Bot / Mobile(gRPC) / NativeOBS
- 对象存储仅保留 LocalOSS + AliOSS
- 日志输出仅保留 LoggerFile

Preserve Web admin APIs and LocalOSS /oss/ downloads and signatures.
Database and Zinc search cleanup are already checked and handled on separate branches.
Remove coupled OTLP initialization with LoggerOtlp; preserve Sentry error monitoring.
Validation: existing behavior tests and builds; deletion-only change, no new test seam.

## Answer

Implemented in cleanup/issue-1-runtime, commit 658e7dcc.
Removed standalone Admin, SpaceX, Bot, Mobile/gRPC, NativeOBS schemas/services and generated stubs.
Retained LocalOSS/AliOSS adapters and LoggerFile sink, removed retired configuration, admin settings and SDK/exporter dependencies.
Preserved Web management APIs, LocalOSS downloads/signatures, Sentry monitoring, and main worktree.
Updated tracked installation and configuration documentation. CONTEXT.md is ignored and exists only in main; it was not altered.

Validation passed: go build ./...; go build -tags 'constraint migration docs' ./...;
go test ./...; go vet ./...; go mod tidy -diff; git diff --check;
go build -tags generate -o /tmp/websitecore-issue1-mir-generator ./mirc.

Code review: Standards 0 findings; Spec 0 findings. See ../review.md.
User reviewed the change, completed manual smoke testing, and authorized PR publication.
Branch pushed to fork; PR: https://github.com/BZYA-Community/WebsiteCore/pull/65
Smoke app on port 18008 and its compose containers stopped; volumes, media and logs retained.
