# AI 查单员对接：两个看板、一个操作与最终结果确认

查单员处理一项明确任务：会员已付款，但某笔订单需要核查。她不回答所有业务问题，也不根据会员输入计算加账金额。运行前需配置并发布员工流程、AI 服务、图片存储、Telegram Bot 与上游群映射，并完成业务接口联调。

## 业务侧提供什么

| 名称 | 形态 | 契约重点 |
| --- | --- | --- |
| deposit_orders | 列表型看板 | 每笔提供 order_no、gateway_id、amount_minor、currency、created_at |
| deposit_complete | 操作 | fields 仅含 order_no 与 gateway_id，按原订单验证与处理 |
| deposit_result | 记录型看板 | 按当前会员与订单读取最终业务状态 |

订单列表最多每页 20 笔，页码最多 1000；金额非负整数最小单位，时间 Unix 秒。不能附带支付账户、姓名等无关数据。专用看板格式错误会让该步骤失败，不采用客服普通看板少显示一格的降级方式。

操作请求的执行者是 task_employee 类型 actor。买方必须核验网站、会员与订单归属，按网站加订单永久去重，同一订单重新查询也不能重复生效；request_id 同样要留下。

## 最后再读一次结果

deposit_result 请求包含 user_id、order_no 与 request_id。data.status 只接受：processed（已收到并提交处理，含已入账）、not_received、unknown。

可选返回实际 amount_minor 与 currency、完整 balance 对象及 result_time。不能把“完成接口返回 OK”直接替代这个最终结果。

## 等待与失败有各自出口

上游核查有最长等待时间，固定节奏提醒，会员可以退出。技术失败可结束任务并提供转人工按钮；最终结果读不到则明确说暂时没有取得结果，不重新调用完成操作，也不把所有未知结果统一当作技术失败。

只有订单与最终结果读取按规定重试，含首次最多三次。写入 Telegram 和完成订单不自动补发。上游迟到回执与已结束任务也有既定处理规则，需按完整契约联调，不能简单丢弃所有迟到事件。

## 怎样证明对接好了

覆盖正常核查、上游未收到、无效水单、换水单、超时、重复回执、重复订单完成、结果未知和越权订单。查看后台时间线，分别核对会员任务状态、上游回执与业务结果。

一个可信 AI Agent 的能力来自这整条链路，而不只来自模型的一段回答。

## 产品与对接入口

- [SRICH CHAT 官网](https://chat.s.rich/)
- [官网演示入口](https://chat.s.rich/#demo)
- [帮助中心](https://chat.s.rich/docs)
- [GitHub 项目与公开文档](https://github.com/vnpayhahaha/srich-chat)
- [SRICH CHAT 专栏](https://bug.it.com/index.php/category/srich-chat/)
- [本主题的完整接口契约](https://chat.s.rich/docs/#/zh-CN/employee-capabilities)

本文为 2026-09-22 整理的公开说明；功能使用取决于已部署版本、配置和对接情况。接口实现以对应版本的帮助中心契约为准。
