# 01: 增加与封禁账户区分的禁言功能

Category: enhancement
Status: needs-triage

## 问题描述

当前后台虽然显示“禁言”，实际操作是将账户状态设为 `UserStatusClosed`。该状态阻止用户登录，并在常规 JWT 鉴权链中拒绝已登录用户的请求，属于封禁账户，而不是单独限制发言。

需要新增独立禁言能力，能够在保留登录、浏览等非发言能力的情况下限制发言，并与封禁账户分别管理、展示。

## 代码依据

- `web/src/views/AdminUsers.vue`：`handleStatusChange` 通过 `admin/user/status` 切换正常/停用状态。
- `internal/servants/web/admin.go`：`ChangeUserStatus` 直接更新账户 `Status`。
- `internal/servants/web/pub.go`：登录时拒绝 `UserStatusClosed`。
- `internal/servants/chain/jwt.go`：常规鉴权拒绝非正常状态账户。
- `internal/dao/jinzhu/dbr/user.go`：当前账户状态只有正常、停用，没有独立禁言字段。

## 预期结果

- 禁言与封禁账户使用独立状态或限制模型，不再用停用账户表达禁言。
- 仅被禁言的用户仍能登录及浏览其原本有权访问的内容。
- 禁言限制在后端执行，覆盖最终确定的发言接口，不能仅隐藏前端按钮。
- 后台分别提供禁言、解除禁言和封禁、解除封禁入口，并准确说明效果。
- 封禁账户继续限制登录和账户操作；解除禁言不能解除封禁。

## 待明确

- 禁言范围：帖子、评论、回复、课程评论/回复、私信是否全部受限。
- 禁言期限：永久禁言、限时禁言及自动解禁是否同时支持。
- 谁可以执行禁言、解除禁言，是否允许操作管理账户。
- 禁言原因、操作记录、用户提示和通知的要求。
- 被禁言用户编辑已有内容、修改公开资料等操作是否受限。

## Comments

- 2026-10-04：用户要求先记录到本地 Issue；本票未实施。
