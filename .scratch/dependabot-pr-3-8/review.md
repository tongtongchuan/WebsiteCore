# PR #3–#8 安全依赖修复审阅

修复分支：`fix/dependabot-pr-3-8`  
工作树：`/tmp/websitecore-dependabot-pr-3-8`  
基线：`origin/main`，`91e6974767a553b031cabb50c96411f6f55c6750`  
状态：用户审阅通过后已提交 `619d536d` 并推送；已创建 [PR #60](https://github.com/BZYA-Community/WebsiteCore/pull/60)，等待 CI 和合并。原 PR 和远端告警未操作。

## 改动

| 依赖 | 原版本 | 最终版本 |
|---|---|---|
| OpenTelemetry core / SDK / metrics | 1.38.0 | 1.45.0 |
| OTLP trace / metrics exporters | 1.36.0 | 1.45.0 |
| OTLP log exporter | 0.10.0 | 0.21.0 |
| OpenTelemetry log API / SDK | 0.12.2 | 0.21.0 |
| otellogrus bridge | 0.11.0 | 0.20.0 |
| x/crypto | 0.47.0 | 0.56.0 |
| gRPC | 1.72.1 | 1.83.2 |
| 最低 Go 版本 | 1.24.0 | 1.26.0 |
| golangci-lint | 1.64.8 | 2.14.0 |

- 旧 otellogrus 使用新版 log API 已移除的类型，编译失败；升级 bridge 修复。
- gRPC 要求 x/crypto 至少 0.55.0；官方漏洞数据库指出两项 SSH 漏洞要到 0.56.0 才修复，因此采用 0.56.0。这也要求 Go 1.26。
- 对齐关联 SDK、API、exporter，更新 go.mod/go.sum，清理不再需要的依赖。
- CI 改用 golangci-lint v2 并迁移配置，保留 govet、ineffassign、gofmt、goimports 和 auto/ 的排除规则。
- 业务源码未修改。

## 验证

- Go 1.27.1：`go build ./...` 通过。
- Go 1.27.1：`go build -tags 'embed migration' ./...` 通过。
- Go 1.27.1：`go test ./...` 全部通过，干净检出无 config.yaml。
- `golangci-lint config verify` 通过；`golangci-lint run ./...`：0 issues。
- `golangci-lint fmt --diff`：无差异。
- `go mod verify`：all modules verified。
- `git diff --check`：通过。
- Go 1.26.0 最低工具链：默认构建、embed/migration 构建和完整 `go test ./...` 全部通过。

## 漏洞对照

使用 Go 官方 `govulncheck v1.8.0`、Go 1.27.1 对同一 main 基线与升级依赖扫描。JSON 模式为生成报告而使用；扫描进程退出 0 不代表没有漏洞。

| 去重后的 Go 漏洞条目 | 基线 | 升级后 |
|---|---:|---:|
| 依赖模块层面的条目 | 56 | 22 |
| 扫描发现调用路径的条目 | 21 | 12 |

共移除 34 项，无新增条目。调用路径仅说明分析结果，不等同于生产环境可利用性。

GitHub 当前快照中，本次目标模块共有 25 条开放告警。最终版本达到其修复版本；传递升级 x/net 还覆盖额外告警 #17。GitHub 的告警状态需要合并到 main、重新扫描后才能确认关闭。

剩余 22 条 Go 数据库条目涉及 x/image、pgx、mapstructure、edwards25519、compress、fasthttp 及 x/crypto/openpgp。前六类不属于本次 PR 范围。OpenPGP 条目 GO-2026-5932 没有修复版本，本项目扫描未导入该包；x/crypto 的其他已报告条目已消除。剩余漏洞仍需另行处理。

### 目标 GitHub 告警

| 告警 | 模块 | 修复版本 | GHSA |
|---|---|---|---|
| [#9](https://github.com/BZYA-Community/WebsiteCore/security/dependabot/9) | go.opentelemetry.io/otel/sdk | 1.40.0 | GHSA-9h8m-3fm2-qjrq |
| [#10](https://github.com/BZYA-Community/WebsiteCore/security/dependabot/10) | google.golang.org/grpc | 1.79.3 | GHSA-p77j-4mvh-x3m3 |
| [#12](https://github.com/BZYA-Community/WebsiteCore/security/dependabot/12) | go.opentelemetry.io/otel/sdk | 1.43.0 | GHSA-hfvc-g4fc-pqhx |
| [#15](https://github.com/BZYA-Community/WebsiteCore/security/dependabot/15) | go.opentelemetry.io/otel | 1.41.0 | GHSA-mh2q-q3fh-2475 |
| [#19](https://github.com/BZYA-Community/WebsiteCore/security/dependabot/19) | golang.org/x/crypto | 0.52.0 | GHSA-q4h4-gmj2-qvw2 |
| [#20](https://github.com/BZYA-Community/WebsiteCore/security/dependabot/20) | golang.org/x/crypto | 0.52.0 | GHSA-45gg-vh54-h5m9 |
| [#21](https://github.com/BZYA-Community/WebsiteCore/security/dependabot/21) | golang.org/x/crypto | 0.52.0 | GHSA-78mq-xcr3-xm33 |
| [#22](https://github.com/BZYA-Community/WebsiteCore/security/dependabot/22) | golang.org/x/crypto | 0.52.0 | GHSA-qpw4-5x99-6vjp |
| [#23](https://github.com/BZYA-Community/WebsiteCore/security/dependabot/23) | golang.org/x/crypto | 0.52.0 | GHSA-vgwf-h737-ff37 |
| [#24](https://github.com/BZYA-Community/WebsiteCore/security/dependabot/24) | golang.org/x/crypto | 0.52.0 | GHSA-w879-237q-wc7r |
| [#25](https://github.com/BZYA-Community/WebsiteCore/security/dependabot/25) | golang.org/x/crypto | 0.52.0 | GHSA-89gr-r52h-f8rx |
| [#26](https://github.com/BZYA-Community/WebsiteCore/security/dependabot/26) | golang.org/x/crypto | 0.52.0 | GHSA-rm3j-f69w-wqmq |
| [#27](https://github.com/BZYA-Community/WebsiteCore/security/dependabot/27) | golang.org/x/crypto | 0.52.0 | GHSA-5cgq-3rg8-m6cv |
| [#28](https://github.com/BZYA-Community/WebsiteCore/security/dependabot/28) | golang.org/x/crypto | 0.52.0 | GHSA-x527-x647-q7gg |
| [#29](https://github.com/BZYA-Community/WebsiteCore/security/dependabot/29) | golang.org/x/crypto | 0.52.0 | GHSA-jppx-rxg9-jmrx |
| [#30](https://github.com/BZYA-Community/WebsiteCore/security/dependabot/30) | golang.org/x/crypto | 0.52.0 | GHSA-f5wc-c3c7-36mc |
| [#31](https://github.com/BZYA-Community/WebsiteCore/security/dependabot/31) | golang.org/x/crypto | 0.52.0 | GHSA-9m57-25v3-79x9 |
| [#32](https://github.com/BZYA-Community/WebsiteCore/security/dependabot/32) | google.golang.org/grpc | 1.82.1 | GHSA-hrxh-6v49-42gf |
| [#33](https://github.com/BZYA-Community/WebsiteCore/security/dependabot/33) | google.golang.org/grpc | 1.83.1 | GHSA-vp52-pcj8-j9qc |
| [#34](https://github.com/BZYA-Community/WebsiteCore/security/dependabot/34) | google.golang.org/grpc | 1.83.1 | GHSA-qc2q-p7wx-3px3 |
| [#35](https://github.com/BZYA-Community/WebsiteCore/security/dependabot/35) | google.golang.org/grpc | 1.82.2 | GHSA-2v4p-qf9q-27wj |
| [#36](https://github.com/BZYA-Community/WebsiteCore/security/dependabot/36) | go.opentelemetry.io/otel/sdk | 1.45.0 | GHSA-8wmf-6v46-5gfg |
| [#37](https://github.com/BZYA-Community/WebsiteCore/security/dependabot/37) | go.opentelemetry.io/otel/exporters/otlp/otlptrace | 1.45.0 | GHSA-8wmf-6v46-5gfg |
| [#38](https://github.com/BZYA-Community/WebsiteCore/security/dependabot/38) | go.opentelemetry.io/otel/exporters/otlp/otlptrace/otlptracegrpc | 1.45.0 | GHSA-8wmf-6v46-5gfg |
| [#39](https://github.com/BZYA-Community/WebsiteCore/security/dependabot/39) | go.opentelemetry.io/otel/exporters/otlp/otlplog/otlploggrpc | 0.21.0 | GHSA-w34q-cm8f-9c5x |

## 审阅材料

- 完整补丁：`/tmp/websitecore-dependabot-pr-3-8.patch`
- 完整测试输出：`/tmp/websitecore-security-tests.log`、`/tmp/websitecore-security-go126-tests.log`
- 原始扫描报告：`/tmp/websitecore-security-baseline.json`、`/tmp/websitecore-security-upgraded.json`
- 原 GitHub 告警快照：`/tmp/websitecore-security-github-alerts.json`

## PR 草稿

标题：`fix(deps): restore security fixes from Dependabot PRs #3–#8`

Dependabot PRs #3–#8 were closed without merging, leaving their dependency vulnerabilities unresolved. Upgrade OpenTelemetry to 1.45.0 (logs 0.21.0), gRPC to 1.83.2, and x/crypto to 0.56.0, with compatible OTLP exporters and the Logrus bridge.

Raise the minimum Go version to 1.26 for x/crypto and migrate CI to golangci-lint v2 while preserving the existing checks. This replaces the upgrades proposed in #3, #4, #5, #6, #7, and #8.

Validation: default and embed/migration builds and full Go tests on Go 1.26.0 and 1.27.1, lint (0 issues), dependency checksum verification, and baseline/comparison govulncheck scans. The comparison removes 34 Go vulnerability entries with no additions. Unrelated existing findings remain; GitHub alerts will be rechecked after merging to main.
