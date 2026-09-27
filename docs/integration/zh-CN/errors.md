# 对接排障指南：HTTP 200、业务成功与结果未知要分开看

对接失败不总是一条红色报错。有时 HTTP 200 中的字段被丢弃，有时写操作超时但已经完成，有时知识同步失败而会员仍在读旧答案。验收要看接口含义与业务结果。

## 统一排查入口

调用客服系统的接口时，记录 code、HTTP 状态与 request_id，必要时带响应头 X-Request-Id。按 code 分支，不匹配可能变化的 message。分享排障材料前去掉凭据、签名和会员敏感数据。

| 状态或错误 | 先检查什么 |
| --- | --- |
| 401 UNAUTHENTICATED | 站点与凭据是否匹配、有效，站点是否可用 |
| 401 身份票或签名错误 | 过期、重复使用、签名内容与公钥 |
| 403 WIDGET_BOOTSTRAP_DENIED | 聊天来源与站点状态 |
| 422 UNKNOWN_FIELD / INVALID_FIELD | 对照契约及 data.field |
| 413 PAYLOAD_TOO_LARGE | 正文大小，通知不是批量数据同步 |
| 429 | Retry-After 与各接口的重试约定 |
| 5xx | 保留 request_id，按该接口规则处理，不统一盲重试 |

## 三种容易误判的“成功”

事件响应 dropped_fields 非空：系统收到事件，但模板没有声明部分字段。要补配置或移除字段。

信息接口 accepted：系统收下，不证明某位会员看到；信息会过期，没有逐用户阅读回执。

操作接口超时：不知道对方做没做。先核查业务，再决定人工重试，同一次操作沿用原 request_id。AI 员工写动作不自动补发。

## 不同读接口也不同

FAQ 超时或格式错误会继续使用旧内容，合法空 entries 才清空；普通看板错误可能只显示空白或缺字段；AI 专用订单和结果看板采用更严格校验及各自失败出口。

没有 code 的操作响应不能当成功。普通看板对旧响应的兼容也不能复制到写操作上。

## 给支持人员的最小材料

提供请求时间、接口用途、脱敏后的 HTTP 状态与 code、request_id、期望和实际表现。对于重复执行问题，再提供同一幂等编号对应的业务处理次数；对于卡片缺失，提供字段配置与 dropped_fields。

完整逐接口错误码表见帮助中心，不要只根据本篇的常用状态表推断未列出的行为。

## 产品与对接入口

- [SRICH CHAT 官网](https://chat.s.rich/)
- [官网演示入口](https://chat.s.rich/#demo)
- [帮助中心](https://chat.s.rich/docs)
- [GitHub 项目与公开文档](https://github.com/vnpayhahaha/srich-chat)
- [SRICH CHAT 专栏](https://bug.it.com/index.php/category/srich-chat/)
- [本主题的完整接口契约](https://chat.s.rich/docs/#/zh-CN/errors)

本文为 2026-09-22 整理的公开说明；功能使用取决于已部署版本、配置和对接情况。接口实现以对应版本的帮助中心契约为准。
