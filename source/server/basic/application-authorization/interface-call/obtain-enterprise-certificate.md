---
date: 2021/11/30
ref: WW-90605
---

# 获取企业凭证

第三方服务商在取得企业的永久授权码后，通过此接口可以获取到企业的 access_token。
获取后可通过通讯录、应用、消息等企业接口来运营这些应用。

此处获得的企业 access_token 与企业获取 access_token 拿到的 token，本质上是一样的，只不过获取方式不同。获取之后，就跟普通企业一样使用 token 调用 API 接口
调用企业接口所需的 access_token 获取方法如下。

**请求方式**：POST（HTTPS）<br />
**请求地址**： https://qyapi.weixin.qq.com/cgi-bin/service/get_corp_token?suite_access_token=SUITE_ACCESS_TOKEN

**请求包体**：

```json
{
  "auth_corpid": "auth_corpid_value",
  "permanent_code": "code_value"
}
```

**参数说明**：

| 参数           | 是否必须 | 说明                                     |
| -------------- | -------- | ---------------------------------------- |
| auth_corpid    | 是       | 授权方 corpid                            |
| permanent_code | 是       | 永久授权码，通过 get_permanent_code 获取 |

"**返回结果**"：

```json
{
  "errcode": 0,
  "errmsg": "ok",
  "access_token": "xxxxxx",
  "expires_in": 7200
}
```

**参数说明**：

| 参数         | 说明                                       |
| ------------ | ------------------------------------------ |
| access_token | 授权方（企业）access_token,最长为 512 字节 |
| expires_in   | 授权方（企业）access_token 超时时间        |

!!! note "因历史原因，该接口在调用失败时才返回 errcode。没返回 errcode 视为调用成功"
