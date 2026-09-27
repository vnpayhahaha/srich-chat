# 事件通知接入：业务发生后，怎样成为会员聊天里的一张卡

买方系统发生注册完成、订单状态变化等事件后，可以调用事件接口。运营先定义事件槽位、字段、模板和语言版本，业务系统只发事件名与数据，客服系统将它们渲染成聊天卡片。

事件通知需要买方开发配合，不是填写后台模板之后就自动知道业务发生了什么。

## 请求

`POST /api/v1/webhook/sites/SITE_CODE/events`，站点凭据通过 Authorization Bearer 传递，正文为 JSON。

| 字段 | 规则 |
| --- | --- |
| action | 已启用事件槽位的名称，最多 64 字符 |
| event_id | 1–128 个可见字符，标识同一个业务事件 |
| user_id | 当前站点的会员编号，和身份接入保持一致 |
| display_name | 可选显示名，最多 100 字符 |
| lang | 可选语言，最多 16 字符 |
| fields | 可选对象，最多 50 个键，不能传数组 |

顶层不接受额外字段，正文最多 16 KB。业务字段放 fields，只保留槽位声明过的字段。

## 一个成功响应也要看内容

响应 data 包含 `message_code`、`replayed` 和 `dropped_fields`。replayed 表示同事件已处理，未产生第二张卡。dropped_fields 非空意味着发来的某些字段没有声明，被丢弃；HTTP 200 不代表卡片内容已经完整。

同一个事件重发保持同一个 event_id。不要每次重试生成新的随机编号，否则会产生新的通知。事件消息收到时渲染并保存，后续修改模板不回写旧卡片。

## 离线与保留

在线会员可实时收到，离线会员在重新连接后读取仍在保留范围内的消息。事件不是等待客服回复的咨询，不应混入人工待处理需求。历史消息按配置保留，默认启用 30 天清理。

## 验收与限流

首次发送检查消息编号与会员卡片；原请求重发检查 replayed 且无第二张卡；确认 dropped_fields 为空。未知或停用槽位返回 EVENT_SLOT_NOT_FOUND，字段错误按 422 检查，超大正文按 413 处理。

遇到 EVENT_RATE_LIMITED（429），按 Retry-After 退避，继续用同一个 event_id。这个限制按网站和 action 区分；不要立即死循环重试。

## 产品与对接入口

- [SRICH CHAT 官网](https://chat.s.rich/)
- [官网演示入口](https://chat.s.rich/#demo)
- [帮助中心](https://chat.s.rich/docs)
- [GitHub 项目与公开文档](https://github.com/vnpayhahaha/srich-chat)
- [SRICH CHAT 专栏](https://bug.it.com/index.php/category/srich-chat/)
- [本主题的完整接口契约](https://chat.s.rich/docs/#/zh-CN/events)

本文为 2026-09-22 整理的公开说明；功能使用取决于已部署版本、配置和对接情况。接口实现以对应版本的帮助中心契约为准。
