# frp 内网穿透

[frp](https://gofrp.org/) 是一款简单高效的内网穿透工具，今天我们就来简单整理下如何利用 frp 来实现内网穿透。

首先，我们先简单了解下什么是 frp?

frp 是一款采用 go 语言开发的基于 C/S 架构的软件，它分为服务端和客户端。我们将服务端部署在具有公网 IP 的服务器上，客户端部署在内网机器上，通过访问暴露在服务器上的端口，反向代理处于内网的服务。顾名思义，所以我们至少需要一台具有公网 IP 的服务器才行。

## 准备工作

1. 具有公网 IP 的服务器
2. 想要将服务映射到公网的机器

## 服务器端设置

1. 准备软件安装包，在 [Github Release](https://github.com/fatedier/frp/releases) 页面下载最新版本。这里我们使用的是 v0.47.0。

```bash
# 获取最新稳定版本的安装包
wget https://github.com/fatedier/frp/releases/download/v0.47.0/frp_0.47.0_linux_amd64.tar.gz

# 解压到指定目录
sudo tar -zxvf frp_0.47.0_linux_amd64.tar.gz -C /usr/local/

# 目录重命名
sudo mv /usr/local/frp_0.47.0_linux_amd64/ /usr/local/frp
```

2. 文件结构，这里我们重点关注下面这几个文件即可。

```code
frpc            客户端程序
frpc_full.ini   客户端配置文件模板
frpc.ini        客户端配置文件
frps            服务器端程序
frps_full.ini   服务器端配置文件模板
frps.ini        服务器端配置文件
```

3. 编辑服务器端配置文件，修改文件内容如下：

```ini
[common]
# 服务端监听地址
bind_addr = 0.0.0.0

# 服务端监听端口
bind_port = 7000

# 身份验证方式，token 或 oidc，默认为 token。
authentication_method = token

# 鉴权使用的 token 值（客户端需要设置一样的值才能鉴权通过）
token = token12345!

# 启用 Dashboard 监听的本地端口
dashboard_port = 7500

# HTTP BasicAuth 用户名
dashboard_user = admin

# HTTP BasicAuth 密码
dashboard_pwd = admin

# 为 HTTP 类型代理监听的端口
vhost_http_port = 10080
vhost_https_port = 10433
```

4. 启动服务

```bash
/usr/local/frp/frps -c /usr/local/frp/frps.ini
```

5. 使用 systemd 管理

- 如Linux服务端上没有安装 systemd，可以使用 yum 或 apt 等命令安装 systemd。

```bash
# yum
sudo yum install systemd
# apt
sudo apt install systemd
```

- 使用文本编辑器，如 vim 创建并编辑 frps.service 文件。

```bash
sudo vim /etc/systemd/system/frps.service
```

- 写入如下内容：

```ini
[Unit]
# 服务名称，可自定义
Description = frp server
After = network.target syslog.target
Wants = network.target

[Service]
Type = simple
# 启动frps的命令，需修改为您的frps的安装路径
ExecStart = /path/to/frps -c /path/to/frps.ini

[Install]
WantedBy = multi-user.target
```

- 使用 systemd 命令，管理 frps。

```bash
# 启动frp
sudo systemctl start frps
# 停止frp
sudo systemctl stop frps
# 重启frp
sudo systemctl restart frps
# 查看frp状态
sudo systemctl status frps
```

- 配置 frps 开机自启。

```bash
sudo systemctl enable frps
```

## 客户端端设置

1. 将客户端配置文件内容修改如下：

```ini
[common]
# 注意，这里的 IP 地址，是你服务器的公网 IP。
server_addr = 127.0.0.1

# 与服务器端 bind_port 对应
server_port = 7000

# 与服务器端 token 保持一致
token = token12345!

# 下面这两项配置可用于启动客户端配置文件热加载（/usr/local/bin/frp reload）
# 启用 AdminUI 监听的本地地址
admin_addr = 127.0.0.1
# 启用 AdminUI 监听的本地端口
admin_port = 7400

[web]
type = http

# 本地 Web 服务监听端口
local_port = 80

# 将域名解析到你服务器的公网 IP。然后通过浏览器访问 http://www.yourdomain.com:10080 就可以访问你内网服务器上的网站了。
# 如果觉得每次带上端口很麻烦，可以在服务器端使用 nginx 配置反向代理，这里不再过多赘述。
custom_domains = www.yourdomain.com
```

2. 启动客户端

```bash
/usr/local/frp/frpc -c /usr/local/frp/frpc.ini
```

## 配置参数说明

- [服务端配置](https://gofrp.org/docs/reference/server-configures/)
- [客户端配置](https://gofrp.org/docs/reference/client-configures/)
- [代理配置](https://gofrp.org/docs/reference/proxy/)