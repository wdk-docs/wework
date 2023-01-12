---
title: 企业授权应用
date: 2020/12/16
---

# 企业授权应用[^1]

[^1]: WW-90597

企业微信的系统管理员可以授权安装第三方应用，安装后企业微信后台会将授权凭证、授权信息等推送给服务商后台。
授权可以有两种发起方式：

- 从服务商网站发起
- 从企业微信应用市场发起

以上两种授权发起方式并不冲突，服务商可以同时支持。

## 从服务商网站发起

系统管理员在第三方服务商网站找到适用的应用后，可在服务商网站发起授权请求。
此方式下第三方服务商需构造授权链接，引导用户进入授权页面完成授权过程，并取得临时授权码。
流程如图示。

![](https://p.qpic.cn/pic_wework/3478211281/f4f236ff7054fe212c05c05c716c7eae46a853da4201dd5b/0)

!!! note

    - 获取预授权码
        预授权码是应用实现授权托管的安全凭证，见[获取预授权码](./interface-call/get-the-pre-authorization-code.md)。
    - 引导用户进入授权页
        第三方服务商在自己的网站中放置“企业微信应用授权”的入口，引导企业微信管理员进入应用授权页。授权页网址为:

        https://open.work.weixin.qq.com/3rdapp/install?suite_id=SUITE_ID&pre_auth_code=PRE_AUTH_CODE&redirect_uri=REDIRECT_URI&state=STATE

        跳转链接中，第三方服务商需提供 suite_id、预授权码、授权完成回调 URI 和 state 参数。

        其中 redirect_uri 是授权完成后的回调网址，redirect_uri 需要经过一次 urlencode 作为参数；state 可填 a-zA-Z0-9 的参数值（不超过 128 个字节），用于第三方自行校验 session，防止跨域攻击。

    - 授权成功，返回临时授权码
        用户确认授权后，会进入回调 URI(即 redirect_uri)，并在 URI 参数中带上临时授权码、过期时间以及 state 参数。第三方服务商据此获得临时授权码。回调地址为：redirect_uri?auth_code=xxx&expires_in=600&state=xx
    - 临时授权码 10 分钟后会失效，第三方服务商需尽快使用临时授权码换取永久授权码及授权信息。
        每个企业授权的每个应用的永久授权码、授权信息都是唯一的，第三方服务商需妥善保管。后续可以[通过永久授权码获取企业 access_token](./interface-call/obtain-an-enterprise-permanent-authorization-code.md)，进而调用企业微信相关 API 为授权企业提供服务。

## 从企业微信应用市场发起

企业可以在应用市场找到想要使用的应用并授权安装，具体流程如图示。

![](https://p.qpic.cn/pic_wework/3478211281/6e68a865fdddcc0817e6af92d0e738d2e00d66efcbde7aac/0)

!!! NOTE

    - 回调临时授权码的详细说明见[授权成功通知](./callback-notification/authorized-notification-event.md)
    - 利用临时授权码获取永久授权码见[获取永久授权码](./interface-call/obtain-an-enterprise-permanent-authorization-code.md)；
    - 临时授权码 10 分钟后会失效，第三方服务商需尽快使用临时授权码换取永久授权码及授权信息。
    - 每个企业授权的每个应用的永久授权码、授权信息都是唯一的，第三方服务商需妥善保管。
      后续可以通过[永久授权码获取企业 access_token](./callback-notification/../interface-call/obtain-enterprise-certificate.md)，进而调用企业微信相关 API 为授权企业提供服务。
