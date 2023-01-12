# 内网穿透工具[^1]

[^1]: https://developer.work.weixin.qq.com/devtool/introduce?id=40600

## 介绍

开发者在应用的开发测试阶段，应用服务通常是部署在开发环境，在有数据回调的开发场景下，企业微信的回调数据无法直接请求到开发环境的服务。
内网穿透工具可以帮助开发者将应用开发调试过程中的回调请求，穿透到本地的开发环境。

## 使用指引

内网穿透工具与第三方应用配套使用，在应用的回调配置栏有一个对应的 `内网调试` 开关：

![](https://wework.qpic.cn/wwpic/764067_r5gCv3-kQbOqq3M_1659443536/0)

- 内网调试 `关闭`
  : 企业微信针对该应用的指令回调和数据回调，将正常回调到填写的【指令回调 URL】和 【数据回调 URL】上。
- 内网调试 `开启`
  : 将忽略在应用【回调配置】填写的 【数据回调 URL】和【指令回调 URL】，该应用的指令回调和数据回调，**将通过安全通道请求到内网穿透工具上**，然后工具把这些回调的数据 **转发到本地服务指定的端口与路由**。

### 下载工具

内网穿透工具是一个可以直接运行的二进制工具，开发者可以根据系统的类型，选择下载对应的二进制压缩包。

- 下载 Windows 64 位版本
- [下载 Mac Intel 版本](https://dldir1.qq.com/wework/wwopen/developer/wecom-tunnel/mac-intel/tunnel.gz)
- 下载 Mac M1 版本
- 下载 Linux 64 位版本

### 开启调试

在使用工具之前，需要开启第三方应用的内网调试。在【应用详情】-【回调配置】-【内网调试】点击【前往开启】，打开【开启】开关按钮，得到对应的回调授权码，点击【确定】保存。

![](https://wework.qpic.cn/wwpic/233815_YoYvAJSfRGGWv7d_1659085962/0)

### 使用工具

请勿直接双击打开应用，须在命令行下使用本穿透工具。在穿透工具所在目录执行以下命令查看版本信息，以确认穿透工具运行正常：

```console
$ ./tunnel -V
1.0.0
```

!!! warning "Mac 电脑使用提示"

    文件权限 在 Mac 上使用前，需要配置穿透工具的文件权限，在穿透工具所在目录执行命令 :
    ```zsh
    chmod 777 ./tunnel
    ```
    安全性与隐私 当提示 “无法打开 tunnel ，因为 Apple 无法检查其是否包含恶意软件” 时，须到
    : 【系统偏好设置】-【安全性与隐私】-【通用】下方的菜单中，点击【仍然允许】 。

#### 1. 配置调试信息

使用 `config set <key> <value>` 命令配置本地服务调试信息：

```sh
# 配置回调授权码
./tunnel config set auth xxxxxxxxxxxxxx
# 配置本地应用服务的端口
./tunnel config set port 8090
# 配置本地应用服务的指令回调地址
./tunnel config set commandCallback /callback/command
# 配置本地应用服务的数据回调地址
./tunnel config set dataCallback /callback/data
# 配置完成后，检查确认配置信息。
./tunnel config list
```

#### 2. 启动调试工具

确认无误后使用 start 命令启动穿透服务：

```console
$ ./tunnel start
[Tunnel online] waiting callback
```

#### 3. 开始调试

在第三方应用的【指令回调 URL】点击【刷新 Ticket】，触发一次回调，查看上下行数据：

![](https://wework.qpic.cn/wwpic/459187_44ZTGqwOT2ea6QW_1659429588/0)

## 配置说明

| 配置项                     | 说明                    | 默认              | 备注                         |
| -------------------------- | ----------------------- | ----------------- | ---------------------------- |
| config set auth            | 回调授权码 7 天临时有效 |
| config set ip              | 本地服务 IP             | 127.0.0.1         |
| config set port            | 本地服务端口            | 8090              |
| config set dataCallback    | 本地服务数据回调 URL    | /callback/data    |
| config set commandCallback | 本地服务指令回调 URL    | /callback/command |
| config set debug           | 开启本地调试模式        | false             | 调试模式下可打印更详细的信息 |

## 其他说明

1. 回调授权码
   回调授权码 7 天临时有效，可在回调授权码过期前手动点击【重新获取】换取新的授权码。回调授权码过期之后，内网调试开关也会自动关闭。一个应用的回调授权码只能同时被一个内网穿透工具使用。

2. 内网调试的场景
   仅【测试企业列表】中的企业，通过【安装测试】方式安装的应用，对应的回调才能走到内网调试来。

3. 其他回调场景
   代开发应用、企业自建应用的内网调试，将会陆续支持。

## 常见问题

???+ question "1. linux 后台运行失败"

    当在 linux 使用 `nohup` 命令在终端后台运行时可能会在输出日志中看到以下报错：

    ```js
    events.js:377
        throw er; // Unhandled 'error' event
        ^

    Error: EBADF: bad file descriptor, read
    Emitted 'error' event on ReadStream instance at:
        at internal/fs/streams.js:173:14
        at FSReqCallback.wrapper [as oncomplete] (fs.js:563:5) {
    errno: -9,
    code: 'EBADF',
    syscall: 'read'
    }
    ```

    当遇到类似问题时可以按照如下操作来关闭 `nohup` 命令的标准输入：

    ```zsh
    nohup ./tunnel start </dev/null >> output.log
    ```
