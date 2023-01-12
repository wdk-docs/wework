---
date: 2022/04/19
---

# 开始开发

作为第三方服务商的开发者，在开始开发之前，需要先了解各种接口凭证的差别，以更好的理解第三方的开放体系。

## 企业接口的 token

除了第三方相关的接口外，前文所述的为企业开放的所有接口，都必须以企业的 corpid 和永久授权码来获取 access_token，然后调用通讯录、应用、消息等接口服务于企业。接口详情参见“获取企业 access_token”

## 应用授权的 token

企业在授权应用时，第三方需要以 suite_id（第三方应用 ID）、suite_secret（第三方应用密钥）换取 suite_access_token，再以 suite_access_token 访问应用授权的接口。在最终访问授权企业的接口时，再将 suite_access_token 换为企业的 access_token。接口详情参见“获取第三方应用凭证”。

suite_id（第三方应用 ID）和 suite_secret（第三方应用密钥）获取方法

服务商：登录服务商管理后台->标准应用服务->应用管理栏，点进某个应用即可看到。
开发者：登录开发者中心->工具->网页应用开发，点进某个应用即可看到。

## 服务商的 token

以 corpid、provider_secret（获取方法为：登录服务商管理后台->标准应用服务->通用开发参数，可以看到）换取 provider_access_token，代表的是服务商的身份，而与应用无关。请求单点登录、注册定制化等接口需要用到该凭证。接口详情如下：

- 请求方式：POST（HTTPS）
- 请求地址： https://qyapi.weixin.qq.com/cgi-bin/service/get_provider_token

请求包体：

```json
{
  "corpid": "xxxxx",
  "provider_secret": "xxx"
}
```

参数说明：

| 参数            | 是否必须 | 说明                                  |
| --------------- | -------- | ------------------------------------- |
| corpid          | 是       | 服务商的 corpid                       |
| provider_secret | 是       | 服务商的 secret，在服务商管理后台可见 |

返回结果：

```json
{
  "errcode": 0,
  "errmsg": "ok",
  "provider_access_token": "enLSZ5xxxxxxJRL",
  "expires_in": 7200
}
```

参数说明：

| 参数                  | 说明                                     |
| --------------------- | ---------------------------------------- |
| provider_access_token | 服务商的 access_token，最长为 512 字节。 |
| expires_in            | provider_access_token 有效期（秒）       |
