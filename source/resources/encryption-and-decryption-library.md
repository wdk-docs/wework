---
date: 2021/02/20
---

# 加解密库下载与返回码

加解密库的返回码

| 返回码 | 说明                     |
| ------ | ------------------------ |
| -40001 | 签名验证错误             |
| -40002 | xml/json 解析失败        |
| -40003 | sha 加密生成签名失败     |
| -40004 | AESKey 非法              |
| -40005 | ReceiveId 校验错误       |
| -40006 | AES 加密失败             |
| -40007 | AES 解密失败             |
| -40008 | 解密后得到的 buffer 非法 |
| -40009 | base64 加密失败          |
| -40010 | base64 解密失败          |
| -40011 | 生成 xml/json 失败       |

加解密库下载及示例

## c++库

xml 版本(2018 年 10 月 11 日更新,点击下载)

注意事项：

WXBizMsgCrypt.h 声明了 WXBizMsgCrypt 类，提供用户接入企业微信的三个接口。WXBizMsgCrypt.cpp 文件提供了三个接口的实现。Sample.cpp 文件提供了如何使用这三个接口的示例。
WXBizMsgCrypt 类封装了 VerifyURL, DecryptMsg, EncryptMsg 三个接口，分别用于开发者验证接收消息的 url，收到用户回复消息的解密以及开发者回复消息的加密过程。使用方法可以参考 Sample.cpp 文件。
加解密协议请参考企业微信官方文档。
加解密过程使用了开源的 openssl 和 tinyxml2 库，请开发者自行安装之后使用。
openssl 的版本号是 openssl-1.0.1h，https://www.openssl.org/
tinyxml2 的版本号是 tinyxml2-2.1.0，https://github.com/leethomason/tinyxml2
json 版本[(2020 年 9 月 1 日更新,点击下载)]

注意事项：

1. 回调 sdk json 版本
2. WXBizJsonMsgCrypt.h 声明了 WXBizJsonMsgCrypt 类，提供用户接入企业微信的三个接口。WXBizJsonMsgCrypt.cpp 文件提供了三个接口的实现。Sample.cpp 文件提供了如何使用这三个接口的示例。
3. WXBizJsonMsgCrypt 类封装了 VerifyURL, DecryptMsg, EncryptMsg 三个接口，分别用于开发者验证回调 url，收到用户回复消息的解密以及开发者回复消息的加密过程。使用方法可以参考 Sample.cpp 文件。
4. 加解密协议请参考企业微信官方文档。
5. 加解密过程使用了开源的 openssl 和 rapidJson 库，请开发者自行安装之后使用。

   openssl 的版本号是 openssl-1.0.1h,https://www.openssl.org/,
   rapidjson 的版本号是 rapidjson v1.1.0, https://github.com/Tencent/rapidjson/releases/tag/v1.1.0。

## python 库

(点击下载)

注意事项：

WXBizMsgCrypt.py 文件封装了 WXBizMsgCrypt 接口类，提供了用户接入企业微信的三个接口，Sample.py 文件提供了如何使用这三个接口的示例，ierror.py 提供了错误码。
WXBizMsgCrypt 封装了 VerifyURL, DecryptMsg, EncryptMsg 三个接口，分别用于开发者验证接收消息的 url、接收消息的解密以及开发者回复消息的加密过程。使用方法可以参考 Sample.py 文件。
本代码用到了 pycrypto 第三方库，请开发者自行安装此库再使用。

## php 库

(点击进入)

注意事项：

WXBizMsgCrypt.php 文件提供了 WXBizMsgCrypt 类的实现，是用户接入企业微信的接口类。Sample.php 提供了示例以供开发者参考。errorCode.php, pkcs7Encoder.php, sha1.php, xmlparse.php 文件是实现这个类的辅助类，开发者无须关心其具体实现。
WXBizMsgCrypt 类封装了 VerifyURL, DecryptMsg, EncryptMsg 三个接口，分别用于开发者验证接收消息的 url、接收消息的解密以及开发者回复消息的加密过程。使用方法可以参考 Sample.php 文件。

## java 库

xml 版本(2018 年 10 月 11 日更新,点击下载)

注意事项：

com\qq\weixin\mp\aes 目录下是用户需要用到的接入企业微信的接口，其中 WXBizMsgCrypt.java 文件提供的 WXBizMsgCrypt 类封装了用户接入企业微信的三个接口，其它的类文件用户用于实现加解密，用户无须关心。sample.java 文件提供了接口的使用示例。
WXBizMsgCrypt 封装了 VerifyURL, DecryptMsg, EncryptMsg 三个接口，分别用于开发者验证接收消息的 url、接收消息的解密以及开发者回复消息的加密过程。使用方法可以参考 Sample.java 文件。
请开发者使用 jdk1.6 或以上的版本。针对 org.apache.commons.codec.binary.Base64，需要导入 jar 包 commons-codec-1.9（或 comm ons-codec-1.8 等其他版本），我们有提供，官方下载地址：
https://commons.apache.org/proper/commons-codec/download_codec.cgi
异常 java.security.InvalidKeyException:illegal Key Size 的解决方案：在官方网站下载 JCE 无限制权限策略文件（请到官网下载对应的版本， 例如 JDK7 的下载地址：https://www.oracle.com/technetwork/java/javase/downloads/jce-7-download-432124.html )：下载后解压，可以看到 local_policy.jar 和 US_export_policy.jar 以及 readme.txt。
如果安装了 JRE，将两个 jar 文件放到%JRE_HOME% \lib\security 目录下覆盖原来的文件，如果安装了 JDK，将两个 jar 文件放到%JDK_HOME%\jre\lib\security 目录下覆盖原来文件。
json 版本(2020 年 9 月 1 日更新,点击下载

注意事项：

1. 企业微信回调 sdk json 版本
2. com\qq\weixin\mp\aes 目录下是用户需要用到的接入企业微信的接口，其中 WXBizJsonMsgCrypt.java 文件提供的 WXBizJsonMsgCrypt 类封装了用户接入企业微信的三个接口，其它的类文件用户用于实现加解密，用户无须关心。sample.java 文件提供了接口的使用求例。
3. WXBizJsonMsgCrypt 封装了 VerifyURL, DecryptMsg, EncryptMsg 三个接口，分别用于开发者验证回调 url，收到用户回复消息的解密以及开发者回复消息的加密过程。使用方法可以参考 Sample.java 文件。
4. 加解密协议请参考企业微信官方文档。
5. 请开发者使用 jdk1.6 以上的版本。针对 org.apache.commons.codec.binary.Base64，需要导入架包 commons-codec-1.9（或 commons-codec-1.8 等其他版本），我们有提供，官方下载地址：https://commons.apache.org/proper/commons-codec/download_codec.cgi
6. 针对 json 解析，需要编译导入架包 json.jar，我们有提供，官方源码下载地址 : https://github.com/stleary/JSON-java 7. 异常 java.security.InvalidKeyException:illegal Key Size 的解决方案：

在官方网站下载 JCE 无限制权限策略文件（JDK7 的下载地址：https://www.oracle.com/technetwork/java/javase/downloads/jce-7-download-432124.html
下载后解压，可以看到 local_policy.jar 和 US_export_policy.jar 以及 readme.txt。如果安装了 JRE，将两个 jar 文件放到%JRE_HOME%\lib\security 目录下覆盖原来的文件，如果安装了 JDK，将两个 jar 文件放到%JDK_HOME%\jre\lib\security 目录下覆盖原来文件

## c#库

(2018 年 10 月 11 日更新,点击下载)

注意事项：

Cryptography.cs 文件封装了 AES 加解密过程，用户无须关心具体实现。WXBizMsgCrypt.cs 文件提供了用户接入企业微信的三个接口，Sample.cs 文件提供了如何使用这三个接口的示例。
WXBizMsgCrypt.cs 封装了 VerifyURL, DecryptMsg, EncryptMsg 三个接口，分别用于开发者验证接收消息的 url、接收消息的解密以及开发者回复消息的加密过程。使用方法可以参考 Sample.cs 文件。

## golang 库

xml 版本(2019 年 1 月 18 日更新,点击下载)

注意事项：

wxbizmsgcrypt.go 文件封装了 AES 加解密过程，并且实现了 VerifyURL, DecryptMsg, EncryptMsg 三个接口，分别用于开发者验证接收消息的 url、接收消息的解密以及开发者回复消息的加密过程。用户无须关心具体实现。sample.go 文件提供了如何使用这三个接口的示例。
json 版本(2020 年 9 月 1 日更新,点击下载

注意事项：

1. 回调 sdk json 版本
2. wxbizjsonmsgcrypt.go 文件中声明并实现了 WXBizJsonMsgCrypt 类，提供用户接入企业微信的三个接口。sample.go 文件提供了如何使用这三个接口的示例。
3. WXBizJsonMsgCrypt 类封装了 VerifyURL, DecryptMsg, EncryptMsg 三个接口，分别用于开发者验证回调 url，收到用户回复消息的解密以及开发者回复消息的加密过程。使用方法可以参考 sample.go 文件。
4. 加解密协议请参考企业微信官方文档。

## node 库

```zsh
# with npm
npm install @wecom/crypto
# with yarn
yarn add @wecom/crypto
```
