---
date: 2020/08/31
---

# 概述[^1]

[^1]: WW-90613

在发生授权、通讯录变更、ticket 变化等事件时，企业微信服务器会向应用的“指令回调 URL”推送相应的事件消息。
消息结构体将使用创建应用时的 [EncodingAESKey](../../../../provider-create-app.md#配置开发信息) 进行加密（**特别注意, 在第三方回调事件中使用加解密算法，receiveid 的内容为 suiteid**），请参考[接收消息][使用接收消息]解析数据包。

本章节的回调事件，服务商在收到推送后 **都必须直接返回字符串 `"success"`**，若返回值不是 `"success"`，企业微信会把返回内容当作错误信息。

> 各个事件皆假设指令回调 URL 设置为：`https://127.0.0.1/suite/receive`

> 收到的数据包中 `ToUserName` 为产生事件的 `SuiteId`，`AgentID `为空
> 各个事件的数据包仅是接收的数据包中的 [Encrypt 参数][使用接收消息]解密后的内容说明

[使用接收消息]: ../../message-push/receive-messages-and-events/#使用接收消息

---

- [授权通知事件](./authorized-notification-event.md)
