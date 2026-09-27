# 在线客服接入指南：一行 script 之外，还要接好哪几件事

页面嵌入解决的是“把聊天放在哪里”。一个脚本会创建聊天 iframe 并铺进指定容器；会员身份与业务接口不是由这一行脚本自动完成的。

## 最小页面结构

```html
<div id="chat-tab" style="height: 70dvh"></div>
<script src="https://CHAT_HOST/chat/embed.js"
        data-site="SITE_CODE"
        data-container="#chat-tab"
        data-lang="zh-CN"></script>
```

大写值都是说明用占位符，需替换为部署方提供的值。`data-site` 和 `data-container` 必填；容器必须有明确尺寸。允许嵌入的站点域名也需在后台配置，并按网站 CSP 放行脚本与 iframe 来源。

## 登录、退出、恢复

| 调用或事件 | 对接方职责 |
| --- | --- |
| `aichat.identify(identity)` | 将自己后端生成的身份原样交给聊天 |
| `aichat.logout()` | 网站退出时清除聊天的旧身份 |
| `identity-needed` | 向自己的后端取新签名，再 identify |
| `unread` | 将未读数画在自己的网站导航上 |
| `ready` / `identity-state` | 处理连接就绪与会员／访客状态 |
| `open / close / toggle` | 重新测量位置，显示由容器决定 |

不要让浏览器自行决定要签哪个会员编号。身份必须来自买方后端当前登录态。退出调用不能省，尤其要测试共享设备切换账号。

## 样式与语言

页面可以在客服后台定义，接入后调整聊天页自身样式不要求买方重新发版。AI 助手生成支持范围内的样式草稿，由运营预览确认。

`data-lang` 影响界面语言，也提供会员阅读语言依据；翻译需开启相应配置与 AI 服务。界面中英文与可以作为翻译目标的语言标签不是同一范围。

## 上线前实际走一遍

切换网站导航后回来，聊天容器位置正确；手机使用动态视口高度并测试软键盘。登录成为会员，退出后换另一人，看不到旧身份内容。让身份失效，确认恢复回调能从后端拿到新签名。

容器显隐优先用 class、style 或 hidden；只改祖先上的自定义属性可能无法触发位置重算。持续会话里的历史仍受消息保留策略约束。

## 产品与对接入口

- [SRICH CHAT 官网](https://chat.s.rich/)
- [官网演示入口](https://chat.s.rich/#demo)
- [帮助中心](https://chat.s.rich/docs)
- [GitHub 项目与公开文档](https://github.com/vnpayhahaha/srich-chat)
- [SRICH CHAT 专栏](https://bug.it.com/index.php/category/srich-chat/)
- [本主题的完整接口契约](https://chat.s.rich/docs/#/zh-CN/embed)

本文为 2026-09-22 整理的公开说明；功能使用取决于已部署版本、配置和对接情况。接口实现以对应版本的帮助中心契约为准。
