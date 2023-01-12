# 服务端 第三方应用接口

## 概述

第三方服务商拥有自己的 API 集合，主要用于完成授权流程以及实现授权后的相关功能。

特别注意，第三方服务商调用所有 API（包括第三方 API 及通讯录、发消息等所有 API）都需要验证来源 IP。
只有在服务商信息中填写的合法 IP 列表内才能合法调用，其他一律拒绝。

## 接口说明

第三方 API（即接口）如下：

| API 名称                                  | 功能                                |
| ----------------------------------------- | ----------------------------------- |
| [get_suite_token](#get_suite_token)       | 获取第三方应用凭证                  |
| [get_pre_auth_code](#get_pre_auth_code)   | 获取预授权码                        |
| [set_session_info](#set_session_info)     | 设置授权配置                        |
| [get_permanent_code](#get_permanent_code) | 获取企业的永久授权码                |
| [get_corp_token](#get_corp_token)         | 获取企业 access_token               |
| [get_auth_info](#get_auth_info)           | 获取企业授权信息                    |
| [get_admin_list](#get_admin_list)         | 获取应用的管理员列表                |
| [getuserinfo3rd](#getuserinfo3rd)         | 第三方根据 code 获取企业成员信息    |
| [getuserdetail3rd](#getuserdetail3rd)     | 第三方使用 user_ticket 获取成员详情 |

第三方应用在获得企业授权后，服务商可以获取企业 access_token，调用企业微信相关的 API 进行发消息、管理通讯录和应用等操作。

## 获取第三方应用凭证

<a id="get_suite_token"></a>
该 API 用于获取第三方应用凭证（suite_access_token）。

!!! warning

    由于第三方服务商可能托管了大量的企业，其安全问题造成的影响会更加严重，故 API 中除了合法来源 IP 校验之外，还额外增加了 suite_ticket 作为安全凭证。

    获取 suite_access_token 时，需要 suite_ticket 参数。suite_ticket 由企业微信后台定时推送给“指令回调 URL”，每十分钟更新一次，见推送 suite_ticket。

    suite_ticket 实际有效期为 30 分钟，可以容错连续两次获取 suite_ticket 失败的情况，但是请永远使用最新接收到的 suite_ticket。

    通过本接口获取的 suite_access_token 有效期为 2 小时，开发者需要进行缓存，不可频繁获取。

- **请求方式**：POST（HTTPS）
- **请求地址**： https://qyapi.weixin.qq.com/cgi-bin/service/get_suite_token

**请求包体**：

```json
{
  "suite_id": "id_value",
  "suite_secret": "secret_value",
  "suite_ticket": "ticket_value"
}
```

**参数说明**：

| 参数         | 是否必须 | 说明                                                     |
| ------------ | -------- | -------------------------------------------------------- |
| suite_id     | 是       | 以 ww 或 wx 开头应用 id（对应于旧的以 tj 开头的套件 id） |
| suite_secret | 是       | 应用 secret                                              |
| suite_ticket | 是       | 企业微信后台推送的 ticket                                |

**返回结果**：

```json
{
  "errcode": 0,
  "errmsg": "ok",
  "suite_access_token": "61W3mEpU66027wgNZ_MhGHNQDHnFATkDa9-2llMBjUwxRSNPbVsMmyD-yq8wZETSoE5NQgecigDrSHkPtIYA",
  "expires_in": 7200
}
```

**参数说明**：

| 参数               | 说明                                    |
| ------------------ | --------------------------------------- |
| suite_access_token | 第三方应用 access_token,最长为 512 字节 |
| expires_in         | 有效期                                  |

## 获取预授权码

<a id="get_pre_auth_code"></a>
该 API 用于获取预授权码。预授权码用于企业授权时的第三方服务商安全验证。

- **请求包体**：GET（HTTPS）
- **请求地址**： https://qyapi.weixin.qq.com/cgi-bin/service/get_pre_auth_code?suite_access_token=SUITE_ACCESS_TOKEN

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
| expires_in    | 有效期                   |

<a id="set_session_info"></a>

## 设置授权配置

该接口可对某次授权进行配置。可支持测试模式（应用未发布时）。

- **请求包体**：POST（HTTPS）
- **请求地址**： https://qyapi.weixin.qq.com/cgi-bin/service/set_session_info?suite_access_token=SUITE_ACCESS_TOKEN

**请求包体**：

```json
{
  "pre_auth_code": "xxxxx",
  "session_info": {
    "appid": [1, 2, 3],
    "auth_type": 1
  }
}
```

**参数说明**：

| 参数               | 是否必须 | 说明                                                                                                                           |
| ------------------ | -------- | ------------------------------------------------------------------------------------------------------------------------------ |
| suite_access_token | 是       | 第三方应用 access_token                                                                                                        |
| pre_auth_code      | 是       | 预授权码                                                                                                                       |
| session_info       | 是       | 本次授权过程中需要用到的会话信息                                                                                               |
| appid              | 否       | 允许进行授权的应用 id，如 1、2、3， 不填或者填空数组都表示允许授权套件内所有应用（仅旧的多应用套件可传此参数，新开发者可忽略） |
| auth_type          | 否       | 授权类型：0 正式授权， 1 测试授权。 默认值为 0。注意，请确保应用在正式发布后的授权类型为“正式授权”                             |

**返回结果**：

```json
{
  "errcode": 0,
  "errmsg": "ok"
}
```

**参数说明**：

| 参数    | 说明                   |
| ------- | ---------------------- |
| errcode | 返回码                 |
| errmsg  | 对返回码的文本描述内容 |

## 获取企业永久授权码

<a id="get_permanent_code"></a>
该 API 用于使用临时授权码换取授权方的永久授权码，并换取授权信息、企业 access_token，临时授权码一次有效。建议第三方以 userid 为主键，来建立自己的管理员账号。

**请求方式**：POST（HTTPS）

**请求地址**： https://qyapi.weixin.qq.com/cgi-bin/service/get_permanent_code?suite_access_token=SUITE_ACCESS_TOKEN

**请求包体**：

```json
{
  "auth_code": "auth_code_value"
}
```

**参数说明**：

| 参数      | 是否必须 | 说明                                                                                                                    |
| --------- | -------- | ----------------------------------------------------------------------------------------------------------------------- |
| auth_code | 是       | 临时授权码，会在授权成功时附加在 redirect_uri 中跳转回第三方服务商网站，或通过回调推送给服务商。长度为 64 至 512 个字节 |

**返回结果**：

```json
{
  "errcode": 0,
  "errmsg": "ok",
  "access_token": "xxxxxx",
  "expires_in": 7200,
  "permanent_code": "xxxx",
  "auth_corp_info": {
    "corpid": "xxxx",
    "corp_name": "name",
    "corp_type": "verified",
    "corp_square_logo_url": "yyyyy",
    "corp_user_max": 50,
    "corp_agent_max": 30,
    "corp_full_name": "full_name",
    "verified_end_time": 1431775834,
    "subject_type": 1,
    "corp_wxqrcode": "zzzzz",
    "corp_scale": "1-50 人",
    "corp_industry": "IT 服务",
    "corp_sub_industry": "计算机软件/硬件/信息服务",
    "location": "广东省广州市"
  },
  "auth_info": {
    "agent": [
      {
        "agentid": 1,
        "name": "NAME",
        "round_logo_url": "xxxxxx",
        "square_logo_url": "yyyyyy",
        "appid": 1,
        "privilege": {
          "level": 1,
          "allow_party": [1, 2, 3],
          "allow_user": ["zhansan", "lisi"],
          "allow_tag": [1, 2, 3],
          "extra_party": [4, 5, 6],
          "extra_user": ["wangwu"],
          "extra_tag": [4, 5, 6]
        }
      },
      {
        "agentid": 2,
        "name": "NAME2",
        "round_logo_url": "xxxxxx",
        "square_logo_url": "yyyyyy",
        "appid": 5
      }
    ]
  },
  "auth_user_info": {
    "userid": "aa",
    "name": "xxx",
    "avatar": "http://xxx"
  }
}
```

**参数说明**：

| 参数                 | 说明                                                                                                             |
| -------------------- | ---------------------------------------------------------------------------------------------------------------- |
| access_token         | 授权方（企业）access_token,最长为 512 字节                                                                       |
| expires_in           | 授权方（企业）access_token 超时时间                                                                              |
| permanent_code       | 企业微信永久授权码,最长为 512 字节                                                                               |
| auth_corp_info       | 授权方企业信息                                                                                                   |
| corpid               | 授权方企业微信 id                                                                                                |
| corp_name            | 授权方企业微信名称                                                                                               |
| corp_type            | 授权方企业微信类型，认证号：verified, 注册号：unverified                                                         |
| corp_square_logo_url | 授权方企业微信方形头像                                                                                           |
| corp_user_max        | 授权方企业微信用户规模                                                                                           |
| corp_full_name       | 所绑定的企业微信主体名称(仅认证过的企业有)                                                                       |
| subject_type         | 企业类型，1. 企业; 2. 政府以及事业单位; 3. 其他组织, 4.团队号                                                    |
| verified_end_time    | 认证到期时间                                                                                                     |
| corp_wxqrcode        | 授权企业在微工作台（原企业号）的二维码，可用于关注微工作台                                                       |
| corp_scale           | 企业规模。当企业未设置该属性时，值为空                                                                           |
| corp_industry        | 企业所属行业。当企业未设置该属性时，值为空                                                                       |
| corp_sub_industry    | 企业所属子行业。当企业未设置该属性时，值为空                                                                     |
| location             | 企业所在地信息, 为空时表示未知                                                                                   |
| auth_info            | 授权信息。如果是通讯录应用，且没开启实体应用，是没有该项的。通讯录应用拥有企业通讯录的全部信息读写权限           |
| agent                | 授权的应用信息，注意是一个数组，但仅旧的多应用套件授权时会返回多个 agent，对新的单应用授权，永远只返回一个 agent |
| agentid              | 授权方应用 id                                                                                                    |
| name                 | 授权方应用名字                                                                                                   |
| square_logo_url      | 授权方应用方形头像                                                                                               |
| round_logo_url       | 授权方应用圆形头像                                                                                               |
| appid                | 旧的多应用套件中的对应应用 id，新开发者请忽略                                                                    |
| privilege            | 应用对应的权限                                                                                                   |
| allow_party          | 应用可见范围（部门）                                                                                             |
| allow_tag            | 应用可见范围（标签）                                                                                             |
| allow_user           | 应用可见范围（成员）                                                                                             |
| extra_party          | 额外通讯录（部门）                                                                                               |
| extra_user           | 额外通讯录（成员）                                                                                               |
| extra_tag            | 额外通讯录（标签）                                                                                               |
| level                | 权限等级。1:通讯录基本信息只读 2:通讯录全部信息只读 3:通讯录全部信息读写 4:单个基本信息只读 5:通讯录全部信息只写 |
| auth_user_info       | 授权管理员的信息                                                                                                 |
| userid               | 授权管理员的 userid，可能为空（内部管理员一定有，不可更改）                                                      |
| name                 | 授权管理员的 name，可能为空（内部管理员一定有，不可更改）                                                        |
| avatar               | 授权管理员的头像 url                                                                                             |

## 获取企业授权信息

<a id="get_auth_info"></a>
该 API 用于通过永久授权码换取企业微信的授权信息。 永久 code 的获取，是通过临时授权码使用 get_permanent_code 接口获取到的 permanent_code。

- **请求包体**：POST（HTTPS）
- **请求地址**： https://qyapi.weixin.qq.com/cgi-bin/service/get_auth_info?suite_access_token=SUITE_ACCESS_TOKEN

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

**返回结果**：

```json
{
  "errcode": 0,
  "errmsg": "ok",
  "auth_corp_info": {
    "corpid": "xxxx",
    "corp_name": "name",
    "corp_type": "verified",
    "corp_square_logo_url": "yyyyy",
    "corp_user_max": 50,
    "corp_agent_max": 30,
    "corp_full_name": "full_name",
    "verified_end_time": 1431775834,
    "subject_type": 1,
    "corp_wxqrcode": "zzzzz",
    "corp_scale": "1-50 人",
    "corp_industry": "IT 服务",
    "corp_sub_industry": "计算机软件/硬件/信息服务",
    "location": "广东省广州市"
  },
  "auth_info": {
    "agent": [
      {
        "agentid": 1,
        "name": "NAME",
        "round_logo_url": "xxxxxx",
        "square_logo_url": "yyyyyy",
        "appid": 1,
        "privilege": {
          "level": 1,
          "allow_party": [1, 2, 3],
          "allow_user": ["zhansan", "lisi"],
          "allow_tag": [1, 2, 3],
          "extra_party": [4, 5, 6],
          "extra_user": ["wangwu"],
          "extra_tag": [4, 5, 6]
        }
      },
      {
        "agentid": 2,
        "name": "NAME2",
        "round_logo_url": "xxxxxx",
        "square_logo_url": "yyyyyy",
        "appid": 5
      }
    ]
  }
}
```

**参数说明**：

| 参数                 | 说明                                                                                                             |
| -------------------- | ---------------------------------------------------------------------------------------------------------------- |
| auth_corp_info       | 授权方企业信息                                                                                                   |
| corpid               | 授权方企业微信 id                                                                                                |
| corp_name            | 授权方企业微信名称                                                                                               |
| corp_type            | 授权方企业微信类型，认证号：verified, 注册号：unverified                                                         |
| corp_square_logo_url | 授权方企业微信方形头像                                                                                           |
| corp_user_max        | 授权方企业微信用户规模                                                                                           |
| corp_full_name       | 所绑定的企业微信主体名称（仅认证过的企业有）                                                                     |
| subject_type         | 企业类型，1. 企业; 2. 政府以及事业单位; 3. 其他组织, 4.团队号                                                    |
| verified_end_time    | 认证到期时间                                                                                                     |
| corp_wxqrcode        | 授权企业在微工作台（原企业号）的二维码，可用于关注微工作台                                                       |
| corp_scale           | 企业规模。当企业未设置该属性时，值为空                                                                           |
| corp_industry        | 企业所属行业。当企业未设置该属性时，值为空                                                                       |
| corp_sub_industry    | 企业所属子行业。当企业未设置该属性时，值为空                                                                     |
| location             | 企业所在地信息, 为空时表示未知                                                                                   |
| auth_info            | 授权信息。如果是通讯录应用，且没开启实体应用，是没有该项的。通讯录应用拥有企业通讯录的全部信息读写权限           |
| agent                | 授权的应用信息，注意是一个数组，但仅旧的多应用套件授权时会返回多个 agent，对新的单应用授权，永远只返回一个 agent |
| agentid              | 授权方应用 id                                                                                                    |
| name                 | 授权方应用名字                                                                                                   |
| square_logo_url      | 授权方应用方形头像                                                                                               |
| round_logo_url       | 授权方应用圆形头像                                                                                               |
| appid                | 旧的多应用套件中的对应应用 id，新开发者请忽略                                                                    |
| privilege            | 应用对应的权限                                                                                                   |
| allow_party          | 应用可见范围（部门）                                                                                             |
| allow_tag            | 应用可见范围（标签）                                                                                             |
| allow_user           | 应用可见范围（成员）                                                                                             |
| extra_party          | 额外通讯录（部门）                                                                                               |
| extra_user           | 额外通讯录（成员）                                                                                               |
| extra_tag            | 额外通讯录（标签）                                                                                               |
| level                | 权限等级。1:通讯录基本信息只读 2:通讯录全部信息只读 3:通讯录全部信息读写 4:单个基本信息只读 5:通讯录全部信息只写 |

## 获取企业 access_token

<a id="get_corp_token"></a>
第三方服务商在取得企业的永久授权码后，通过此接口可以获取到企业的 access_token。
获取后可通过通讯录、应用、消息等企业接口来运营这些应用。

此处获得的企业 access_token 与企业获取 access_token 拿到的 token，本质上是一样的，只不过获取方式不同。获取之后，就跟普通企业一样使用 token 调用 API 接口
调用企业接口所需的 access_token 获取方法如下。

**请求方式**：POST（HTTPS）

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

**返回结果**：

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

## 获取应用的管理员列表

<a id="get_admin_list"></a>
第三方服务商可以用此接口获取授权企业中某个第三方应用的管理员列表(不包括外部管理员)，以便服务商在用户进入应用主页之后根据是否管理员身份做权限的区分。

该应用必须与 SUITE_ACCESS_TOKEN 对应的 suiteid 对应，否则没权限查看

- **请求方式**：POST（HTTPS）
- **请求地址**： https://qyapi.weixin.qq.com/cgi-bin/service/get_admin_list?suite_access_token=SUITE_ACCESS_TOKEN

**请求包体**：

```json
{
  "auth_corpid": "auth_corpid_value",
  "agentid": 1000046
}
```

**参数说明**：

| 参数        | 是否必须 | 说明                     |
| ----------- | -------- | ------------------------ |
| auth_corpid | 是       | 授权方 corpid            |
| agentid     | 是       | 授权方安装的应用 agentid |

**返回结果**：

```json
{
  "errcode": 0,
  "errmsg": "ok",
  "admin": [
    { "userid": "zhangsan", "auth_type": 1 },
    { "userid": "lisi", "auth_type": 0 }
  ]
}
```

**参数说明**：

| 参数      | 说明                                              |
| --------- | ------------------------------------------------- |
| errcode   | 错误码，0 表示正常返回，可读取 admin 的管理员列表 |
| errmsg    | 错误说明                                          |
| admin     | 应用的管理员列表（不包括外部管理员）              |
| userid    | 管理员的 userid                                   |
| auth_type | 该管理员对应用的权限：0=发消息权限，1=管理权限    |

## 网页授权登录第三方

<a id="authorize"></a>
第三方网页授权登录请使用此接口。关于授权流程，与企业内的“网页授权登录”基本一致，此处不再赘述。

构造第三方 oauth2 链接
如果第三方应用需要在打开的网页里面携带用户的身份信息，第一步需要构造如下的链接来获取 code：

https://open.weixin.qq.com/connect/oauth2/authorize?appid=APPID&redirect_uri=REDIRECT_URI&response_type=code&scope=SCOPE&state=STATE#wechat_redirect

**参数说明**：

| 参数             | 必须 | 说明                                                                                                                                                                                                                                             |
| ---------------- | ---- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| appid            | 是   | 第三方应用 id（即 ww 或 wx 开头的 suite_id）。注意与企业的网页授权登录不同                                                                                                                                                                       |
| redirect_uri     | 是   | 授权后重定向的回调链接地址，请使用 urlencode 对链接进行处理 ，注意域名需要设置为第三方应用的可信域名                                                                                                                                             |
| response_type    | 是   | 返回类型，此时固定为：code                                                                                                                                                                                                                       |
| scope            | 是   | 应用授权作用域。snsapi_base：静默授权，可获取成员的基础信息（UserId 与 DeviceId）；snsapi_userinfo：静默授权，可获取成员的详细信息，但不包含手机、邮箱等敏感信息；snsapi_privateinfo：手动授权，可获取成员的详细信息，包含手机、邮箱等敏感信息。 |
| state            | 否   | 重定向后会带上 state 参数，企业可以填写 a-zA-Z0-9 的参数值，长度不可超过 128 个字节                                                                                                                                                              |
| #wechat_redirect | 是   | 终端使用此参数判断是否需要带上身份信息                                                                                                                                                                                                           |

企业员工点击后，页面将跳转至 redirect_uri?code=CODE&state=STATE，第三方应用可根据 code 参数获得企业员工的 corpid 与 userid。code 长度最大为 512 字节。

**权限说明**：

使用 snsapi_privateinfo 的 scope 时，第三方应用必须有'成员敏感信息授权'的权限。

## 第三方根据 code 获取企业成员信息

<a id="getuserinfo3rd"></a>
**请求方式**：GET（HTTPS）

**请求地址**：https://qyapi.weixin.qq.com/cgi-bin/service/getuserinfo3rd?access_token=SUITE_ACCESS_TOKEN&code=CODE

**参数说明**：

| 参数         | 必须 | 说明                                                                                                                      |
| ------------ | ---- | ------------------------------------------------------------------------------------------------------------------------- |
| access_token | 是   | 第三方应用的 suite_access_token，参见“获取第三方应用凭证”                                                                 |
| code         | 是   | 通过成员授权获取到的 code，最大为 512 字节。每次成员授权带上的 code 将不一样，code 只能使用一次，5 分钟未被使用自动过期。 |

**权限说明**：

跳转的域名须完全匹配 access_token 对应第三方应用的可信域名，否则会返回 50001 错误。

**返回结果**：

a) 当用户属于某个企业，返回示例如下：

```json
{
  "errcode": 0,
  "errmsg": "ok",
  "CorpId": "CORPID",
  "UserId": "USERID",
  "DeviceId": "DEVICEID",
  "user_ticket": "USER_TICKET",
  "expires_in": 7200
}
```

| 参数        | 说明                                                                                            |
| ----------- | ----------------------------------------------------------------------------------------------- |
| errcode     | 返回码                                                                                          |
| errmsg      | 对返回码的文本描述内容                                                                          |
| CorpId      | 用户所属企业的 corpid                                                                           |
| UserId      | 用户在企业内的 UserID，如果该企业与第三方应用有授权关系时，返回明文 UserId，否则返回密文 UserId |
| DeviceId    | 手机设备号(由企业微信在安装时随机生成，删除重装会改变，升级不受影响)                            |
| user_ticket | 成员票据，最大为 512 字节。                                                                     |

scope 为 snsapi_userinfo 或 snsapi_privateinfo，且用户在应用可见范围之内时返回此参数。
后续利用该参数可以获取用户信息或敏感信息，参见"第三方使用 user_ticket 获取成员详情"。
expires_in user_ticket 的有效时间（秒），随 user_ticket 一起返回
b) 若用户不属于任何企业，返回示例如下：

```json
{
  "errcode": 0,
  "errmsg": "ok",
  "OpenId": "OPENID",
  "DeviceId": "DEVICEID"
}
```

| 参数     | 说明                                                                 |
| -------- | -------------------------------------------------------------------- |
| errcode  | 返回码                                                               |
| errmsg   | 对返回码的文本描述内容                                               |
| OpenId   | 非企业成员的标识，对当前服务商唯一                                   |
| DeviceId | 手机设备号(由企业微信在安装时随机生成，删除重装会改变，升级不受影响) |

出错返回示例：

```json
{
  "errcode": 40029,
  "errmsg": "invalid code"
}
```

## 第三方使用 user_ticket 获取成员详情

<a id="getuserdetail3rd"></a>
**请求方式**：POST（HTTPS）

**请求地址**：https://qyapi.weixin.qq.com/cgi-bin/service/getuserdetail3rd?access_token=SUITE_ACCESS_TOKEN

**请求包体**：

```json
{
  "user_ticket": "USER_TICKET"
}
```

**参数说明**：

| 参数         | 必须 | 说明                                                      |
| ------------ | ---- | --------------------------------------------------------- |
| access_token | 是   | 第三方应用的 suite_access_token，参见“获取第三方应用凭证” |
| user_ticket  | 是   | 成员票据                                                  |

**权限说明**：

成员必须在授权应用的可见范围内。

**返回结果**：

```json
{
  "errcode": 0,
  "errmsg": "ok",
  "corpid": "wwxxxxxxyyyyy",
  "userid": "lisi",
  "name": "李四",
  "mobile": "15913215421",
  "gender": "1",
  "email": "xxx@xx.com",
  "avatar": "http://shp.qpic.cn/bizmp/xxxxxxxxxxx/0",
  "qr_code": "https://open.work.weixin.qq.com/wwopen/userQRCode?vcode=vcfc13b01dfs78e981c"
}
```

**参数说明**：

| 参数    | 说明                                                                                                    |
| ------- | ------------------------------------------------------------------------------------------------------- |
| errcode | 返回码                                                                                                  |
| errmsg  | 对返回码的文本描述内容                                                                                  |
| corpid  | 用户所属企业的 corpid                                                                                   |
| userid  | 成员 UserID                                                                                             |
| name    | 成员姓名                                                                                                |
| mobile  | 成员手机号，仅在用户同意 snsapi_privateinfo 授权时返回                                                  |
| gender  | 性别。0 表示未定义，1 表示男性，2 表示女性                                                              |
| email   | 成员邮箱，仅在用户同意 snsapi_privateinfo 授权时返回                                                    |
| avatar  | 头像 url。注：如果要获取小图将 url 最后的"/0"改成"/100"即可。仅在用户同意 snsapi_privateinfo 授权时返回 |
| qr_code | 员工个人二维码（扫描可添加为外部联系人），仅在用户同意 snsapi_privateinfo 授权时返回                    |

<!-- <swagger-ui src="./openapi-spec/auth.yaml"/> -->

---

- [开发指南](dev-guide/)
- [基础](basic/index.md)
- [连接微信](wechat/index.md)
- [办公](office/index.md)
- [智慧硬件](iot/index.md)
