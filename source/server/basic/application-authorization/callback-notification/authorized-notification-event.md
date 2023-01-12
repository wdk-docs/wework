---
title: 授权通知事件
date: 2022/12/06
---

# 授权通知事件[^1]

[^1]: WW-91116

## 授权成功通知

从企业微信应用市场发起授权时，企业微信后台会推送授权成功通知。

> 从第三方服务商网站发起的应用授权流程，由于授权完成时会跳转第三方服务商管理后台，因此不会通过此接口向第三方服务商推送授权成功通知。

**请求方式**：POST（HTTPS）<br/>
**请求地址**：https://127.0.0.1/suite/receive?msg_signature=3a7b08bb8e6dbce3c9671d6fdb69d15066227608&timestamp=1403610513&nonce=380320359

**请求包体**：

```xml
<xml>
	<SuiteId><![CDATA[ww4asffe9xxx4c0f4c]]></SuiteId>
	<AuthCode><![CDATA[AUTHCODE]]></AuthCode>
	<InfoType><![CDATA[create_auth]]></InfoType>
	<TimeStamp>1403610513</TimeStamp>
	<State><![CDATA[123]]></State>
</xml>
```

> 服务商的响应必须在 1000ms 内完成，以保证用户安装应用的体验。
> 建议在接收到此事件时，先记录下 AuthCode，并立即回应企业微信，之后再做相关业务的处理。

**参数说明**：

| 参数      | 说明                                                                                                                                |
| --------- | ----------------------------------------------------------------------------------------------------------------------------------- |
| SuiteId   | 第三方应用的 SuiteId 或者代开发应用模板 id                                                                                          |
| AuthCode  | 临时授权码,最长为 512 字节。用于[获取企业永久授权码](https://developer.work.weixin.qq.com/document/path/90642#14942)。10 分钟内有效 |
| InfoType  | create_auth                                                                                                                         |
| TimeStamp | 时间戳                                                                                                                              |
| State     | 构造授权链接指定的 state 参数                                                                                                       |

## 变更授权通知

当授权方（即授权企业）在企业微信管理端的授权管理中，修改了对应用的授权后（包括修改应用的可见范围），企业微信服务器推送变更授权通知。支持第三方应用以及代开发应用。
服务商接收到变更通知之后，需自行调用获取企业授权信息进行授权内容变更比对。

**请求方式**：POST（HTTPS）<br/>
**请求地址**：https://127.0.0.1/suite/receive?msg_signature=3a7b08bb8e6dbce3c9671d6fdb69d15066227608&timestamp=1403610513&nonce=380320359

请求包体：

```xml
<xml>
	<SuiteId><![CDATA[ww4asffe99exxx0f4c]]></SuiteId>
	<InfoType><![CDATA[change_auth]]></InfoType>
	<TimeStamp>1403610513</TimeStamp>
	<AuthCorpId><![CDATA[wxf8b4f85f3a794e77]]></AuthCorpId>
	<State><![CDATA[abc]]></State>
</xml>
```

服务商的响应必须在 1000ms 内完成，以保证用户变更授权的体验。建议在接收到此事件时，立即回应企业微信，之后再做相关业务的处理。

参数说明：

| 参数       | 说明                                       |
| ---------- | ------------------------------------------ |
| SuiteId    | 第三方应用的 SuiteId 或者代开发应用模板 id |
| InfoType   | change_auth                                |
| TimeStamp  | 时间戳                                     |
| AuthCorpId | 授权方的 corpid                            |
| State      | 构造授权链接指定的 state 参数              |

## 取消授权通知

当授权方（即授权企业）在企业微信管理端的授权管理中，取消了对应用的授权托管后，企业微信后台会推送取消授权通知。

**请求方式**：POST（HTTPS）<br/>
**请求地址**：https://127.0.0.1/suite/receive?msg_signature=3a7b08bb8e6dbce3c9671d6fdb69d15066227608&timestamp=1403610513&nonce=380320359

请求包体：

```xml
<xml>
	<SuiteId><![CDATA[ww4asffe99e54cxxxx]]></SuiteId>
	<InfoType><![CDATA[cancel_auth]]></InfoType>
	<TimeStamp>1403610513</TimeStamp>
	<AuthCorpId><![CDATA[wxf8b4f85fxx794xxx]]></AuthCorpId>
</xml>
```

服务商的响应必须在 1000ms 内完成，以保证用户取消授权的体验。建议在接收到此事件时，立即回应企业微信，之后再做相关业务的处理。注意，服务商收到取消授权事件后，应当确保删除该企业所有相关的数据。
参数说明：

| 参数       | 说明                                       |
| ---------- | ------------------------------------------ |
| SuiteId    | 第三方应用的 SuiteId 或者代开发应用模板 id |
| InfoType   | cancel_auth                                |
| TimeStamp  | 时间戳                                     |
| AuthCorpId | 授权方企业的 corpid                        |
