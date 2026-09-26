# 06: 处置过时的 bootstrap SQL

**What to build:** 消除 `scripts/paopao-*.sql` 这个有误导性的 onboarding 路径——它是 bcrypt 迁移之前的旧 schema（`p_user.password` 为 `VARCHAR(32)`），谁拿它建库谁踩坑。

**Blocked by:** 04

**Status:** ready-for-agent

- [ ] 决定并执行：要么把 `scripts/paopao-*.sql` 对齐当前迁移序列，要么显式标记为废弃（若要保留历史用途）
- [ ] 若只做标记，另开一个 issue 记录「对齐或删除」，别在本 ticket 拖住
- [ ] 确认仓库内不再有任何文档把这三个 SQL 当作建库方式
- [ ] 验证：grep 全仓无指向它们作为 onboarding 的引用
