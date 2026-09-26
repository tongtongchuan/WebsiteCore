# 仅支持 PostgreSQL

项目仍处于开发阶段，没有已部署应用或其他使用者。为减少多数据库的维护成本并统一数据库行为，生产、本地开发和测试统一仅支持 PostgreSQL，移除 MySQL 与 SQLite 的支持代码及依赖。

不提供跨数据库数据迁移工具。现有 SQLite 数据库测试改用隔离的 PostgreSQL 测试库，保留原有行为断言。

取消通过 Features 选择数据库，直接使用 PostgreSQL。删除未实现的 Sqlx/MySQL 与 Sqlc/PostgreSQL 访问选项，只保留现有 GORM 实现；原先挂在 MySQL 配置下的连接池参数迁入通用 Database 配置。

同步修改相关使用与维护文档，排除 docs/proposal/ 下 paopao 的旧提案。最初不增加 CI 数据库服务或测试步骤；在合并主分支新增的测试步骤后，CI 因缺少 PostgreSQL 环境失败。2026-09-26 获准调整范围：为现有 CI 测试步骤提供临时 PostgreSQL 18.6 服务和测试 DSN，继续使用隔离 PostgreSQL 测试库，不跳过数据库测试。

以上决策已于 2026-09-26 确认并获准实施。本文取代 ADR 0001、0002 中关于保留 MySQL 支持和通过 Postgres 特性选择数据库的描述，其他决策仍然有效。
