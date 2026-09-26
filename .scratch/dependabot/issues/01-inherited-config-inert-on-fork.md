# 01: dependabot 配置从 upstream 继承，在本 fork 上不生效

**Status:** needs-triage

**现象：** `.github/dependabot.yml` 从未产出任何 PR（`gh pr list --repo BZYA-Community/WebsiteCore --state all --author app/dependabot` 返回空）。

**已核实（2026-09-25）：**
- `origin` = `BZYA-Community/WebsiteCore`，是 `rocboss/paopao-ce` 的 **fork**；默认分支 `main`，远程**只有 `main`**（`git ls-remote --heads origin`、`gh api repos/BZYA-Community/WebsiteCore/branches`）。
- 配置里两条（`gomod`、新加的 `docker`）都写了 `target-branch: "dev"` → 该分支不存在。
- `reviewers: rocboss / alimy` 是 upstream 维护者，很可能不是本 fork collaborator（无法核实：列 collaborator 需 push 权限，API 返回 403）。
- Dependabot 在本 fork 很可能**未启用**（`gh api repos/BZYA-Community/WebsiteCore/vulnerability-alerts` 返回 404）。

**影响：** ticket 01 的验收点「dependabot `docker` ecosystem 跟踪 `docker-compose.dev.yml` 的镜像 pin」实际不生效，pin 仍会腐化。新加的 docker 条目继承了 upstream 的 `dev` / 老 reviewers，因此同样失效。

**可选修法（二选一，待定）：**
- **想真跑：** 仓库 Settings → Code security 打开 Dependabot version updates；`target-branch` 改为 `main`（或删掉该行，默认走默认分支）；去掉 `reviewers` 或换成真实 collaborator。
- **不打算用：** 删除 dependabot 配置（含既有 `gomod` 条目），ticket 01 该验收点按「暂不适用」处理。

**备注：** 用户 2026-09-25 决定暂不修改，仅记录为本地 issue。
