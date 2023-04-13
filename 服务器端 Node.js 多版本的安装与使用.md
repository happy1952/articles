# 服务器端 Node.js 多版本的安装与使用

最近由于新老项目交替，常常需要在多个 Node.js 版本之间进行切换开发。于是我将自己的部署过程整理下来，方便后面的同学使用。

这里我使用的是 [nvm](https://github.com/nvm-sh/nvm) 这个 Node 版本管理器。下面就开始我们具体的操作步骤吧。

## 安装

```bash
# 下载并安装 nvm，注意这个文件不会科学上网的话是下载不下来的，不过没关系下面我会贴一下我下载好的脚本文件。
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.3/install.sh | bash

# 安装完成后继续执行下面的命令，请根据自己的运行环境执行对应的命令。
source ~/.bashrc # bash
source ~/.zshrc # zsh
. ~/.profile # ksh

# 验证是否安装成功，执行如下命令，如果输出了 nvm 则说明已经安装成功了。
command -v nvm
```

## 基本使用

```bash
# 安装最新稳定版
nvm install node

# 安装指定版本
nvm install 18.16.0

# 安装最新的 LTS 版
nvm install --lts

# 列出所有可用版本
nvm ls-remote

# 使用已安装的版本，默认使用第一个安装的版本
nvm use node

# 使用指定版本
nvm use 18.16.0

# 查看指定版本安装路径
nvm which 18.16.0

# 列出已安装版本
nvm ls --no-alias

# 移除指定版本
nvm uninstall 19.9.0

# 获取当前 node 版本对应的最新版的 npm
nvm install-latest-npm

# 使用系统以前安装的版本
nvm use system

# 查看以前系统安装版的信息
nvm run system --version
```

## 其他
* [nvm 安装脚本](./source/nvm_install.zip)