# 信息通道：网页实时展示与聊天留底，为什么分开设计

“会员当时看到的一句提示”与“需要留在服务记录里的通知”有不同生命周期。SRICH CHAT 用信息通道向网站页面传结构化数据，用邮箱和事件记录需要留底的消息。

信息由买方网页自己渲染。没有接前端回调，就不会自动出现飘屏或其他样式。

## 两端都要接

后端向 `POST /api/v1/webhook/sites/SITE_CODE/broadcasts` 发 JSON 与 Bearer 凭据；网站前端监听 `aichat.on('broadcast', callback)`。

请求包含 `event_id`、`kind`、`audience`、`occurred_at`、`expires_at`、`payload`；group 与 display 按类型使用。时间为 Unix 秒，首次到达必须未过期；受众按类型可为全场、会员、访客或指定会员名单。

普通种类包括 big_win、vip_enter、text，另有三类红包事件。member_enter 是系统按身份属性与运营规则生成的类型，不由该 Webhook 接收。

## 接收不等于看到

首次接收返回 202 accepted，data 有 seq 与 expires_at。seq 是十进制字符串，不转换成 JavaScript Number。相同编号与原内容重发返回 200 duplicate，内容改变则返回 409 冲突。

accepted 只表示系统收下，不代表会员看见，也不是活动参与成功回执。一般信息最长有效期 300 秒，红包留言最长 60 秒。重连可以补仍有效的信息，过期内容不补。

## 展示与金额

前端把 payload 当数据处理，用安全的文字与组件渲染，不将它当 HTML 插入。普通金额展示值为十进制字符串，与 currency 配对；这与看板和操作使用最小单位整数不同。

网站可以按自己的品牌决定位置、动画、排队或折叠。客服系统负责这条通信，不替网站实现整套展示交互。

## 验收

测试首次发送、原样重复、同编号改正文、到期、切换会员和重连。普通信息与红包各有开关，站点停用会影响整个站点。

429 时按 Retry-After 退避，重试不改变编号、时间或正文。过期不能改时间冒充同一个原请求。同一会员当前只有最后打开的页面接管连接，不承诺每个页面都同时收到。

## 产品与对接入口

- [SRICH CHAT 官网](https://chat.s.rich/)
- [官网演示入口](https://chat.s.rich/#demo)
- [帮助中心](https://chat.s.rich/docs)
- [GitHub 项目与公开文档](https://github.com/vnpayhahaha/srich-chat)
- [SRICH CHAT 专栏](https://bug.it.com/index.php/category/srich-chat/)
- [本主题的完整接口契约](https://chat.s.rich/docs/#/zh-CN/broadcast)

本文为 2026-09-22 整理的公开说明；功能使用取决于已部署版本、配置和对接情况。接口实现以对应版本的帮助中心契约为准。
