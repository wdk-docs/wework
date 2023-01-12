---
title: 获取企业永久授权码
date: 2022/06/24
---

# 获取企业永久授权码[^1]

[^1]: WW-90603

该 API 用于使用临时授权码换取授权方的永久授权码，并换取授权信息、企业 access_token，临时授权码一次有效。

**请求方式**：POST（HTTPS）<br/>
**请求地址**：https://qyapi.weixin.qq.com/cgi-bin/service/get_permanent_code?suite_access_token=SUITE_ACCESS_TOKEN

**请求包体**：

```json
{
  "auth_code": "auth_code_value"
}
```

**参数说明**：

| 参数      | 是否必须 | 说明                                                                                                                                                                           |
| --------- | -------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| auth_code | 是       | [临时授权码](../enterprise-authorized-application.md)，会在授权成功时附加在 redirect_uri 中跳转回第三方服务商网站，或通过授权成功通知回调推送给服务商。长度为 64 至 512 个字节 |

**返回结果**：

```json
{
  "errcode": 0,
  "errmsg": "ok",
  "access_token": "xxxxxx",
  "expires_in": 7200,
  "permanent_code": "xxxx",
  "dealer_corp_info": {
    "corpid": "xxxx",
    "corp_name": "name"
  },
  "auth_corp_info": {
    "corpid": "xxxx",
    "corp_name": "name",
    "corp_type": "verified",
    "corp_square_logo_url": "yyyyy",
    "corp_user_max": 50,
    "corp_full_name": "full_name",
    "verified_end_time": 1431775834,
    "subject_type": 1,
    "corp_wxqrcode": "zzzzz",
    "corp_scale": "1-50 人",
    "corp_industry": "IT 服务",
    "corp_sub_industry": "计算机软件/硬件/信息服务"
  },
  "auth_info": {
    "agent": [
      {
        "agentid": 1,
        "name": "NAME",
        "round_logo_url": "xxxxxx",
        "square_logo_url": "yyyyyy",
        "appid": 1,
        "auth_mode": 1,
        "is_customized_app": false,
        "auth_from_thirdapp": false,
        "privilege": {
          "level": 1,
          "allow_party": [1, 2, 3],
          "allow_user": ["zhansan", "lisi"],
          "allow_tag": [1, 2, 3],
          "extra_party": [4, 5, 6],
          "extra_user": ["wangwu"],
          "extra_tag": [4, 5, 6]
        },
        "shared_from": {
          "corpid": "wwyyyyy",
          "share_type": 1
        }
      },
      {
        "agentid": 2,
        "name": "NAME2",
        "round_logo_url": "xxxxxx",
        "square_logo_url": "yyyyyy",
        "appid": 5,
        "shared_from": {
          "corpid": "wwyyyyy",
          "share_type": 0
        }
      }
    ]
  },
  "auth_user_info": {
    "userid": "aa",
    "open_userid": "xxxxxx",
    "name": "xxx",
    "avatar": "http://xxx"
  },
  "register_code_info": {
    "register_code": "1111",
    "template_id": "tpl111",
    "state": "state001"
  },
  "state": "state001"
}
```

**参数说明**：

| 参数               | 子参数                 | 说明                                                                                                                                          |
| ------------------ | ---------------------- | --------------------------------------------------------------------------------------------------------------------------------------------- |
| access_token       |                        | 授权方（企业）access_token,最长为 512 字节。代开发自建应用安装时不返回。                                                                      |
| expires_in         |                        | 授权方（企业）access_token 超时时间（秒）。代开发自建应用安装时不返回。                                                                       |
| permanent_code     |                        | 企业微信永久授权码,最长为 512 字节                                                                                                            |
| auth_corp_info     |                        | 授权方企业信息                                                                                                                                |
|                    | corpid                 | 授权方企业微信 id                                                                                                                             |
|                    | corp_name              | 授权方企业名称，即企业简称                                                                                                                    |
|                    | corp_type              | 授权方企业类型，认证号：verified, 注册号：unverified                                                                                          |
|                    | corp_square_logo_url   | 授权方企业方形头像                                                                                                                            |
|                    | corp_user_max          | 授权方企业用户规模                                                                                                                            |
|                    | corp_full_name         | 授权方企业的主体名称(仅认证或验证过的企业有)，即企业全称。企业微信将逐步回收该字段，后续实际返回内容为企业名称，即 auth_corp_info.corp_name。 |
|                    | subject_type           | 企业类型，1. 企业; 2. 政府以及事业单位; 3. 其他组织, 4.团队号                                                                                 |
|                    | verified_end_time      | 认证到期时间                                                                                                                                  |
|                    | corp_wxqrcode          | 授权企业在微信插件（原企业号）的二维码，可用于关注微信插件                                                                                    |
|                    | corp_scale             | 企业规模。当企业未设置该属性时，值为空                                                                                                        |
|                    | corp_industry          | 企业所属行业。当企业未设置该属性时，值为空                                                                                                    |
|                    | corp_sub_industry      | 企业所属子行业。当企业未设置该属性时，值为空                                                                                                  |
| auth_info          |                        | 授权信息。如果是通讯录应用，且没开启实体应用，是没有该项的。通讯录应用拥有企业通讯录的全部信息读写权限                                        |
| auth_info.agent    |                        | 授权的应用信息，注意是一个数组，但仅旧的多应用套件授权时会返回多个 agent，对新的单应用授权，永远只返回一个 agent                              |
|                    | agentid                | 授权方应用 id                                                                                                                                 |
|                    | name                   | 授权方应用名字                                                                                                                                |
|                    | square_logo_url        | 授权方应用方形头像                                                                                                                            |
|                    | round_logo_url         | 授权方应用圆形头像                                                                                                                            |
|                    | appid                  | 旧的多应用套件中的对应应用 id，新开发者请忽略                                                                                                 |
|                    | auth_mode              | 授权模式，0 为管理员授权；1 为成员授权                                                                                                        |
|                    | is_customized_app      | 是否为代开发自建应用                                                                                                                          |
|                    | auth_from_thirdapp     | 来自第三方应用接口唤起,仅通过第三方应用添加自建应用 获取授权链接授权代开发自建应用时，才返回该字段                                            |
|                    | privilege              | 应用对应的权限                                                                                                                                |
|                    | privilege.allow_party  | 应用可见范围（部门）                                                                                                                          |
|                    | privilege.allow_tag    | 应用可见范围（标签）                                                                                                                          |
|                    | privilege.allow_user   | 应用可见范围（成员）                                                                                                                          |
|                    | privilege.extra_party  | 额外通讯录（部门）                                                                                                                            |
|                    | privilege.extra_user   | 额外通讯录（成员）                                                                                                                            |
|                    | privilege.extra_tag    | 额外通讯录（标签）                                                                                                                            |
|                    | privilege.level        | 权限等级。                                                                                                                                    |
|                    |                        | 1:通讯录基本信息只读                                                                                                                          |
|                    |                        | 2:通讯录全部信息只读                                                                                                                          |
|                    |                        | 3:通讯录全部信息读写                                                                                                                          |
|                    |                        | 4:单个基本信息只读                                                                                                                            |
|                    |                        | 5:通讯录全部信息只写                                                                                                                          |
|                    | shared_from            | 共享了应用的企业信息，仅当由企业互联或者上下游共享应用触发的安装时才返回                                                                      |
|                    | shared_from.corpid     | 共享了应用的企业信息，仅当企业互联或者上下游共享应用触发的安装时才返回                                                                        |
|                    | shared_from.share_type | 共享了途径，0 表示企业互联，1 表示上下游                                                                                                      |
| auth_user_info     |                        | 授权管理员的信息，可能不返回                                                                                                                  |
|                    | userid                 | 授权管理员的 userid，可能为空                                                                                                                 |
|                    | open_userid            | 授权管理员的 open_userid，可能为空                                                                                                            |
|                    | name                   | 授权管理员的 name，可能为空                                                                                                                   |
|                    | avatar                 | 授权管理员的头像 url，可能为空                                                                                                                |
| dealer_corp_info   |                        | 代理服务商企业信息。应用被代理后才有该信息                                                                                                    |
|                    | corpid                 | 代理服务商企业微信 id                                                                                                                         |
|                    | corp_name              | 代理服务商企业微信名称                                                                                                                        |
| register_code_info |                        | 推广二维码安装相关信息，扫推广二维码安装时返回。成员授权时暂不支持。（注：无论企业是否新注册，只要通过扫推广二维码安装，都会返回该字段）      |
|                    | register_code          | 注册码                                                                                                                                        |
|                    | template_id            | 推广包 ID                                                                                                                                     |
|                    | state                  | 仅当获取注册码指定该字段时才返回                                                                                                              |
| state              |                        | 安装应用时，扫码或者授权链接中带的 state 值。详见 state 说明                                                                                  |

NOTE: 因历史原因，该接口在调用失败时才返回 errcode。没返回 errcode 视为调用成功

**state 说明**：

目前会返回 state 包含以下几个场景。

1. 扫带参二维码授权代开发模版。
