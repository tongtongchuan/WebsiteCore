# 04: 文档改走 compose + migrate

**What to build:** 任何人照文档从干净 checkout 能起本地环境——不再有手工 `docker run`、废弃 Zinc 或过时 bootstrap SQL 的引导。

**Blocked by:** 01, 03

**Status:** ready-for-agent

- [ ] `docs/INSTALL.md` 与 `docs/INSTALL_ZH.md` 本地流程改为 `make deps-up → make migrate → make run TAGS='embed'`，并说明 JWT `Secret` 的生成
- [ ] `docs/deploy/local/001-本地开发依赖环境部署.md` 重写为指向 compose，删除散装 `docker run`、废弃的 Zinc 与旧的 Meili `v0.29.0`
- [ ] README 快速开始改用 `make deps-up`
- [ ] 所有 onboarding 文档不再把 `scripts/paopao-*.sql` 当作建库方式
- [ ] 验证：照文档从干净环境能起依赖、建 schema、启动应用
