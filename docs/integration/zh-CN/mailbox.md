# 邮箱接口：指定会员通知与汇总，怎样写进同一条服务会话

事件按预建槽位发卡片，邮箱接口按结构化类型写信。两者最终都进入会员聊天。邮箱适合明确发给谁的通知或汇总；它与网页上短暂显示的信息通道不同。

## 两种信

`POST /api/v1/webhook/sites/SITE_CODE/mailbox`，使用站点 Bearer 凭据。

顶层是 `event_id`、`kind`、`audience`、可选 `lang` 与 `payload`。当前接口 audience 只接受 `member_ids`，一批 1–100 个编号，不接受 all、members 或 visitors。运营在后台主动写信是另一条使用方式。

| kind | 必填内容 | 可选内容 |
| --- | --- | --- |
| notice | title（1–100 字）、body（1–1000 字） | link |
| summary | title、lines（1–20 行 label/value） | total、note、link |

每行 label 最多 40 字、value 最多 100 字；note 最多 300 字。link 同时有 text 和 HTTPS url，展示成按钮。文本作为文字显示，不执行 HTML。正文最多 16 KB，未知字段拒绝。

## 重试不会多写一封

响应按收信人返回 message_code 与 replayed。重复 event_id 对已收到的人返回原信，新增收信人可以收到。复用编号不能修改已经送达的内容，也不能把重发理解为编辑历史。

没有打开过聊天的会员可以因信件建立会话。离线恢复只能读到仍在保留期内的记录；默认消息清理为 30 天，运营可调整。

## 配置与开发分工

买方开发提供触发条件、名单和数据，运营配置支持的信件样式。summary 可以显示一件事的结果，但不会替买方计算结算、名单或资金；卡片只是结果的呈现。

## 验收

分别发送 notice 与 summary，检查文字、明细和链接；重发相同编号检查未重复；确认未授权受众被拒。遇到 429 MAILBOX_RATE_LIMITED，按 Retry-After 等待并重发同一封。

邮箱限流按网站统计收信人数，不是只数请求数。大规模群发应走运营后台的相应能力，不要把接口的一批名单无限扩大。

## 产品与对接入口

- [SRICH CHAT 官网](https://chat.s.rich/)
- [官网演示入口](https://chat.s.rich/#demo)
- [帮助中心](https://chat.s.rich/docs)
- [GitHub 项目与公开文档](https://github.com/vnpayhahaha/srich-chat)
- [SRICH CHAT 专栏](https://bug.it.com/index.php/category/srich-chat/)
- [本主题的完整接口契约](https://chat.s.rich/docs/#/zh-CN/mailbox)

本文为 2026-09-22 整理的公开说明；功能使用取决于已部署版本、配置和对接情况。接口实现以对应版本的帮助中心契约为准。
