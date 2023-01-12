---
date: 2021/07/30
ref: WW-90601
---

# 获取预授权码

该 API 用于获取预授权码。预授权码用于企业授权时的第三方服务商安全验证。

**请求方式**：GET（HTTPS）<br/>
**请求地址**： https://qyapi.weixin.qq.com/cgi-bin/service/get_pre_auth_code?suite_access_token=SUITE_ACCESS_TOKEN

**参数说明**：

| 参数               | 是否必须 | 说明                                    |
| ------------------ | -------- | --------------------------------------- |
| suite_access_token | 是       | 第三方应用 access_token,最长为 512 字节 |

**返回结果**：

```json
{
  "errcode": 0,
  "errmsg": "ok",
  "pre_auth_code": "Cx_Dk6qiBE0Dmx4EmlT3oRfArPvwSQ-oa3NL_fwHM7VI08r52wazoZX2Rhpz1dEw",
  "expires_in": 1200
}
```

**参数说明**：

| 参数          | 说明                     |
| ------------- | ------------------------ |
| pre_auth_code | 预授权码,最长为 512 字节 |
| expires_in    | 有效期（秒）             |
