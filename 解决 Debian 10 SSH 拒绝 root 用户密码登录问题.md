# 解决 Debian 10 SSH 拒绝 root 用户密码登录问题

## 问题
今天新装了 Debian 10，发现 root 用户无法通过 xshell 用密码登录，但是别的账号都可以正常登录系统。第一反应就想到了 sshd_config 配置文件禁用了什么选项，网上搜了一下。果然如此，这里记录一下方便自己后面再碰到类似问题时好用来参考。

## 解决办法
```bash
# 编辑 sshd_config 文件
vim /etc/ssh/sshd_config

# 开启允许密码登录选项
PasswordAuthentication yes

# 修改 PermitRootLogin，将其值改为 yes
PermitRootLogin yes

# 重启 ssh
systemctl restart ssh
```