---
ref: https://developer.work.weixin.qq.com/document/path/91144
date: 2022/05/20
---

# 加解密方案说明

## 概述

企业微信在推送消息给企业时，会对消息内容做 AES 加密，以 XML 格式 POST 到企业应用的 URL 上。
企业在被动响应时，也需要对数据加密，以 XML 格式返回给企业微信。
本章节即是对加解密方法的说明。
阅读本章节前，需要了解以下术语：

- **msg_signature**： 消息签名，用于验证请求是否来自企业微信（防止攻击者伪造）。
- **EncodingAESKey**：用于消息体的加密，长度固定为 43 个字符，从 a-z, A-Z, 0-9 共 62 个字符中选取，是 AESKey 的 Base64 编码。解码后即为 32 字节长的 AESKey

  ```java
  AESKey=Base64_Decode(EncodingAESKey + “=”)
  ```

- **AESKey**：AES 算法的密钥，长度为 32 字节。
  AES 采用 CBC 模式，数据采用 PKCS#7 填充至 32 字节的倍数；IV 初始向量大小为 16 字节，取 AESKey 前 16 字节，详见：http://tools.ietf.org/html/rfc2315
- **msg**：为消息体明文，格式为 XML
- **msg_encrypt**：明文消息 msg 加密处理后的 Base64 编码。

## 使用已有库

鉴于加解密算法相对复杂，企业微信提供了算法库。
目前已有 c++/python/php/java/golang/c# 等语言版本。均提供了解密、加密、验证 URL 三个接口，企业可根据自身需要下载，[下载地址][download]。

NOTE:
使用现有库，用户不必细究加解密原理。对于找不到相应语言库的用户，请阅读后文原理详解自行实现。欢迎大家分享~
以 c++为例，使用示例见下载的文件夹中的 Sample.cpp, 此处做简单说明。

[download]: https://developer.work.weixin.qq.com/document/path/90307

### 初始化加解密类

回调 xml 示例：

```cpp
WXBizMsgCrypt wxcpt(sToken,sEncodingAESKey,sReceiveId);
```

要求传参数 sToken,sEncodingAESKey,sReceiveId。
sToken,sEncodingAESKey 即[设置接收消息的参数][setting]章节所述配置的 Token、EncodingAESKey。
特别注意, sReceiveId 在不同场景下有不同含义，见附注[^1]。

[setting]: https://developer.work.weixin.qq.com/document/path/90968#12977/%E8%AE%BE%E7%BD%AE%E6%8E%A5%E6%94%B6%E6%B6%88%E6%81%AF%E7%9A%84%E5%8F%82%E6%95%B0

### 验证 URL 函数

本函数实现：

- 签名校验
- 解密数据包，得到明文消息内容

```cpp
int VerifyURL(const string &sMsgSignature, const string &sTimeStamp, const string &sNonce, const string &sEchoStr, string &sReplyEchoStr);
```

参数说明

| 参数          | 必须 | 说明                                                                        |
| ------------- | ---- | --------------------------------------------------------------------------- |
| sMsgSignature | 是   | 从接收消息的 URL 中获取的 msg_signature 参数                                |
| sTimeStamp    | 是   | 从接收消息的 URL 中获取的 timestamp 参数                                    |
| sNonce        | 是   | 从接收消息的 URL 中获取的 nonce 参数                                        |
| sEchoStr      | 是   | 从接收消息的 URL 中获取的 echostr 参数。注意，此参数必须是 urldecode 后的值 |
| sReplyEchoStr | 是   | 解密后的明文消息内容，用于回包。注意，必须原样返回，不要做加引号或其它处理  |

## 解密函数

本函数实现：

签名校验
解密数据包，得到明文消息结构体
int DecryptMsg(const string &sMsgSignature, const string &sTimeStamp, const string &sNonce, const string &sPostData, string &sMsg);
参数说明

| 参数          | 必须 | 说明                                                              |
| ------------- | ---- | ----------------------------------------------------------------- |
| sMsgSignature | 是   | 从接收消息的 URL 中获取的 msg_signature 参数                      |
| sTimeStamp    | 是   | 从接收消息的 URL 中获取的 timestamp 参数                          |
| sNonce        | 是   | 从接收消息的 URL 中获取的 nonce 参数                              |
| sPostData     | 是   | 从接收消息的 URL 中获取的整个 post 数据                           |
| sMsg          | 是   | 用于返回解密后的 msg，以 xml 组织，参见普通消息格式和事件消息格式 |

## 加密函数

本函数实现：

加密明文消息结构体
生成签名
构造被动响应包

```cpp
int EncryptMsg(const string &sReplyMsg, const string &sTimeStamp, const string &sNonce, string &sEncryptMsg);
```

参数说明

| 参数        | 必须 | 说明                                              |
| ----------- | ---- | ------------------------------------------------- |
| sReplyMsg   | 是   | 返回的消息体原文                                  |
| sTimeStamp  | 是   | 时间戳，调用方生成                                |
| sNonce      | 是   | 随机数，调用方生成                                |
| sEncryptMsg | 是   | 用于返回的密文，以 xml 组织，参见被动回复消息格式 |

## 原理详解

目前官方已提供了 php、python、c++等版本的加解密库，如果开发者需要进行别的语言的开发，需要自行根据加解密原理实现算法。

### 消息体签名校验

为了让企业确认调用来自企业微信，企业微信在回调给接收消息 url 时会带上消息签名，以参数 msg_signature 标识，企业需要验证此参数的正确性后再解密。
验证步骤如下：

计算签名

```cpp
dev_msg_signature=sha1(sort(token、timestamp、nonce、msg_encrypt))。
```

sort 的含义是将参数值按照字母字典排序，然后从小到大拼接成一个字符串
sha1 处理结果要编码为可见字符，编码的方式是把每字节散列值打印为%02x（即 16 进制，C printf 语法）格式，全部小写
比较 dev_msg_signature 和 msg_signature 是否相等，相等则表示验证通过
在被动响应消息时，企业同样需要用如上方法生成签名并传给企业微信

### 明文 msg 的加密过程

拼接明文字符串

```cpp
rand_msg = random(16B) + msg_len(4B) + msg + receiveid
```

明文字符串由 16 个字节的随机字符串、4 个字节的 msg 长度、明文 msg 和 receiveid 拼接组成。其中 msg_len 为 msg 的字节数，网络字节序；sReceiveId 在不同场景下有不同含义，见附注
明文字符串
对明文字符串加密并 Base64 编码

```cpp
msg_encrypt = Base64_Encode(AES_Encrypt(rand_msg))
```

将明文字符串 AESKey 加密后，再进行 Base64 编码，即获得密文 msg_encrypt。

### 密文解密得到 msg 的过程

对密文 BASE64 解码

```cpp
aes_msg=Base64_Decode(msg_encrypt)
```

使用 AESKey 做 AES-256-CBC 解密

```
rand_msg=AES_Decrypt(aes_msg)
```

去掉 rand_msg 头部的 16 个随机字节和 4 个字节的 msg_len，截取 msg_len 长度的部分即为 msg，剩下的为尾部的 receiveid
验证解密后的 receiveid、msg_len。注意，receiveid 在不同场景含义不同。

### 举例说明

假设在服务商管理端为某个套件有如下配置参数：

```cpp
corpId = "wx5823bf96d3bd56c7"
token = "QDG6eK"
encodingAesKey = "jWmYm7qr5nMoAUwZRjGtBxmz3KA1tkAj3ykkR6q2B2C"
```

收到来自企业微信的回调为：
xml 请求示例：

```http
POST /cgi-bin/wxpush?msg_signature=477715d11cdb4164915debcba66cb864d751f3e6&timestamp=1409659813&nonce=1372623149 HTTP/1.1
Host: qy.weixin.qq.com
Content-Length: 603
<xml>
<ToUserName><![CDATA[wx5823bf96d3bd56c7]]></ToUserName>
<Encrypt><![CDATA[RypEvHKD8QQKFhvQ6QleEB4J58tiPdvo+rtK1I9qca6aM/wvqnLSV5zEPeusUiX5L5X/0lWfrf0QADHHhGd3QczcdCUpj911L3vg3W/sYYvuJTs3TUUkSUXxaccAS0qhxchrRYt66wiSpGLYL42aM6A8dTT+6k4aSknmPj48kzJs8qLjvd4Xgpue06DOdnLxAUHzM6+kDZ+HMZfJYuR+LtwGc2hgf5gsijff0ekUNXZiqATP7PF5mZxZ3Izoun1s4zG4LUMnvw2r+KqCKIw+3IQH03v+BCA9nMELNqbSf6tiWSrXJB3LAVGUcallcrw8V2t9EL4EhzJWrQUax5wLVMNS0+rUPA3k22Ncx4XXZS9o0MBH27Bo6BpNelZpS+/uh9KsNlY6bHCmJU9p8g7m3fVKn28H3KDYA5Pl/T8Z1ptDAVe0lXdQ2YoyyH2uyPIGHBZZIs2pDBS8R07+qN+E7Q==]]></Encrypt>
<AgentID><![CDATA[218]]></AgentID>
</xml>
```

#### 第一步：准备相关参数

```
AESKey = Base64_Decode(EncodingAESKey + "=")
signature = "477715d11cdb4164915debcba66cb864d751f3e6";
timestamps = "1409659813";
nonce = "1372623149";
msg_encrypt = "RypEvHKD8QQKFhvQ6QleEB4J58tiPdvo+rtK1I9qca6aM/wvqnLSV5zEPeusUiX5L5X/0lWfrf0QADHHhGd3QczcdCUpj911L3vg3W/sYYvuJTs3TUUkSUXxaccAS0qhxchrRYt66wiSpGLYL42aM6A8dTT+6k4aSknmPj48kzJs8qLjvd4Xgpue06DOdnLxAUHzM6+kDZ+HMZfJYuR+LtwGc2hgf5gsijff0ekUNXZiqATP7PF5mZxZ3Izoun1s4zG4LUMnvw2r+KqCKIw+3IQH03v+BCA9nMELNqbSf6tiWSrXJB3LAVGUcallcrw8V2t9EL4EhzJWrQUax5wLVMNS0+rUPA3k22Ncx4XXZS9o0MBH27Bo6BpNelZpS+/uh9KsNlY6bHCmJU9p8g7m3fVKn28H3KDYA5Pl/T8Z1ptDAVe0lXdQ2YoyyH2uyPIGHBZZIs2pDBS8R07+qN+E7Q==";
```

#### 第二步：校验签名

token、timestamp、nonce、msg_encrypt 这四个参数按照字典序排序

```
"1372623149"
"1409659813"
"QDG6eK"
"RypEvHKD8QQKFhvQ6QleEB4J58tiPdvo+rtK1I9qca6aM/wvqnLSV5zEPeusUiX5L5X/0lWfrf0QADHHhGd3QczcdCUpj911L3vg3W/sYYvuJTs3TUUkSUXxaccAS0qhxchrRYt66wiSpGLYL42aM6A8dTT+6k4aSknmPj48kzJs8qLjvd4Xgpue06DOdnLxAUHzM6+kDZ+HMZfJYuR+LtwGc2hgf5gsijff0ekUNXZiqATP7PF5mZxZ3Izoun1s4zG4LUMnvw2r+KqCKIw+3IQH03v+BCA9nMELNqbSf6tiWSrXJB3LAVGUcallcrw8V2t9EL4EhzJWrQUax5wLVMNS0+rUPA3k22Ncx4XXZS9o0MBH27Bo6BpNelZpS+/uh9KsNlY6bHCmJU9p8g7m3fVKn28H3KDYA5Pl/T8Z1ptDAVe0lXdQ2YoyyH2uyPIGHBZZIs2pDBS8R07+qN+E7Q=="
```

拼接为一个字符串

```
sort_str = "13726231491409659813QDG6eKRypEvHKD8QQKFhvQ6QleEB4J58tiPdvo+rtK1I9qca6aM/wvqnLSV5zEPeusUiX5L5X/0lWfrf0QADHHhGd3QczcdCUpj911L3vg3W/sYYvuJTs3TUUkSUXxaccAS0qhxchrRYt66wiSpGLYL42aM6A8dTT+6k4aSknmPj48kzJs8qLjvd4Xgpue06DOdnLxAUHzM6+kDZ+HMZfJYuR+LtwGc2hgf5gsijff0ekUNXZiqATP7PF5mZxZ3Izoun1s4zG4LUMnvw2r+KqCKIw+3IQH03v+BCA9nMELNqbSf6tiWSrXJB3LAVGUcallcrw8V2t9EL4EhzJWrQUax5wLVMNS0+rUPA3k22Ncx4XXZS9o0MBH27Bo6BpNelZpS+/uh9KsNlY6bHCmJU9p8g7m3fVKn28H3KDYA5Pl/T8Z1ptDAVe0lXdQ2YoyyH2uyPIGHBZZIs2pDBS8R07+qN+E7Q=="
```

对该字符串进行 sha1 计算得到签名

```
signature = sha1(sort_str) = "477715d11cdb4164915debcba66cb864d751f3e6"
```

对比从 URL 得到的签名，发现两者一致，签名通过，说明没被篡改，是安全的

#### 第三步： 解密消息

对密文 base64 解码

```
aes_msg = base64_decode(msg_encrypt)
```

使用 AESKey 做 AES 解密（注意，不是 EncodingAESKey）

```
rand_msg = aes_decrypt(aes_msg, AESKey)
```

去掉 rand_msg 头部的 16 个随机字节和 4 个字节的 msg_len，截取 msg_len 长度的部分即为 msg，剩下的为尾部的 receiveid
下面为类似 python 的伪代码

```
content = rand_msg[16:] # 去掉前 16 随机字节
msg_len = str_to_uint(content[0:4]) # 取出 4 字节的 msg_len
msg = content[4:msg_len+4] # 截取 msg_len 长度的 msg
receiveid = content[msg_len+4:] = "wx5823bf96d3bd56c7" # 剩余字节为 receiveid
```

对于回调 xml 解密后得到明文为：

```xml
<xml>
    <ToUserName><![CDATA[wx5823bf96d3bd56c7]]></ToUserName>
    <FromUserName><![CDATA[mycreate]]></FromUserName>
    <CreateTime>1409659813</CreateTime>
    <MsgType><![CDATA[text]]></MsgType>
    <Content><![CDATA[hello]]></Content>
    <MsgId>4561255354251345929</MsgId>
    <AgentID>218</AgentID>
</xml>
```

根据明文中的 MsgType 可知，此为应用消息回调，因此 receiveid 应该为 corpid，对比 receiveid 与 corpid 是否一致。

[^1]: 附注：ReceiveId 含义

    加解密库里，ReceiveId 在各个场景的含义不同：

    - 企业应用的回调，表示 corpid
    - 第三方事件的回调，表示 suiteid
    - 个人主体的第三方应用的回调，ReceiveId 是一个空字符串
