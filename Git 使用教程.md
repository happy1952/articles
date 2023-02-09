# Git 使用教程

## Git 工作流程

![Git 工作流程](./images/git.webp)

## 常见概念

* Workspace：工作区（就是我们本地的存放代码的目录）
* Index / Stage：暂存区（我们 git add 后，修改的内容会先存放到暂存区 .git/index）
* Repository：仓库区（或本地仓库）（也就是我们项目目录下的 .git 文件夹，这个文件不属于工作区，它是本地版本仓库。）
* Remote：远程仓库

## 开发工作流程

```bash
# 接到需求后，比如要开发一个新的登录注册功能模块，先新建一个本地分支并切到新分支下
git checkout -b login-register

# 每次开发前先更新主分支代码到本地(login-register)分支
git pull origin master

# 开发代码...

# 本地测试...

# 登录注册功能完成后，合并本地分支(login-register)到主分支
# 切换到主分支
git checkout master

# 合并分支
git merge login-register

# 本地主分支下测试...

# 提交代码到远程仓库
git push origin master

# 登录生产服务器，更新并同步代码
git pull origin master

# 同步代码到其他 web 服务器
/opt/api-inotify-rsync.sh 同步后端接口代码

# 前端代码需要先编译后同步
/opt/m-inotify-rsync.sh 同步后台前端页面代码
/opt/h5-inotify-rsync.sh 同步H5、小程序代码
```

## 常用设置

```bash
# 列出当前项目下 git 的配置信息
git config --list

# 设置用户信息
git config --global user.name "John Doe"
git config --global user.email johndoe@example.com

# 设置文本编辑器
git config --global core.editor emacs

# 开启 git 命令行的颜色显示
git config --global color.ui true

# 不检测文件权限变化
git config core.filemode false # 针对当前项目设置
git config --global core.filemode false # 全局设置

# 文件换行符转换设置
git config --global core.autocrlf true # 提交时转换为LF，检出时转换为CRLF
git config --global core.autocrlf input # 提交时转换为LF，检出时不转换
git config --global core.autocrlf false # 提交检出均不转换

# 如果把 autocrlf 设置为 false 时，那另一个配置项 safecrlf 最好设置为 ture。该选项用于检查文件是否包含混合换行符，其有三个可选项：
git config --global core.safecrlf true # 拒绝提交包含混合换行符的文件
git config --global core.safecrlf false # 允许提交包含混合换行符的文件
git config --global core.safecrlf warn # 提交包含混合换行符的文件时给出警告
```

## 常用命令

```bash
# 创建并进入 test 目录
mkdir test && cd test

# 创建 git 仓库
git init

# 添加并编辑测试文件
touch readme.md

# 添加 111111 内容到 readme 文件
# git status 查看状态
# git add -A 添加所有修改文件到暂存区
# 添加 readme 文件到暂存区
git add readme.md

# 提交暂存区到本地仓库
git commit -m"添加 111111 到 readme 文件"

# 重复上面的步骤，添加 222222 到 readme 文件
# 查看提交记录
git log 
# git log -5 查看最近的 5 条提交记录
# git log -p 查看提交记录并展示修改内容
# git log --author=username 查看指定用户的提交记录
# git log --pretty=oneline 精简显示提交记录
# git log --graph 以图形的形式展示分支合并记录
# git log --before={3.weeks.ago} 查下指定日期之前的提交记录
# git log --before={2021-01-21} 查下指定日期之前的提交记录
# git log --after={2023-02-07} 查看指定日期之后的提交记录
# git log -1 -p 885adacca54f6c7d08505b37290dc0b5c113b59c | cat 当修改内容的长度超过终端显示宽度的时候可以使用这种方式查看具体的修改内容
# git log -p 885adacca54f6c7d08505b37290dc0b5c113b59c | less -FX 当修改内容的长度超过终端显示宽度的时候可以使用这种方式查看具体的修改内容

# git commit 之前如何撤销？
# 撤销暂存区
git reset HEAD filename

# git commit 之后如何撤销？
# 版本回退
# 回退到上一个版本
git reset --hard HEAD^
# 回退到上上一个版本
git reset --hard HEAD^^
# 回退到前100个版本
git reset --hard HEAD~100
# 回退到指定版本，谨慎使用，操作失误容易导致代码丢失
git reset --hard COMMIT
# git reset 操作失误解救方法
# 查看回退之前的版本号
git reflog
# 撤销版本回退
git reset --hard Reset之前的版本号

# 撤销修改（没有 add 之前）
git checkout -- filename

# 保存工作区修改内容
git stash

# 查看已隐藏工作区内容
git stash list

# 恢復工作区
git stash apply 

# 删除已隐藏的工作区内容
git stash drop

# 恢復已隐藏工作区同时删除已隐藏工作区
git stash pop

# 创建并切换分支
git checkout -b branch-name

# 创建分支
git branch branch-name

# 切换分支
git checkout branch-name

# 查看分支
git branch

# 查看所有分支（包括远程分支）
git branch -a

# 删除分支
git branch -d branch-name

# 合并分支
git merge branch-name

# 拉取远程仓库代码并自动合并
git pull 

# 拉取远程仓库的代码但是不自动合并
git fetch

# 对比本地代码与新拉取的远程分支的代码
git diff FETCH_HEAD

# 合并新拉取的远程分支的代码
git merge FETCH_HEAD
```

## 其他常用方法

1. 如果想要提交空目录到 git 仓库，可以在当前目录下新建一个 .gitkeep 文件，不需要写入任何内容

2. 使用 .gitattributes 对特定目录或文件来设置，使 git 可以对指定目录或文件子集来应用这些规则

下面是一份 .gitattributes 文件样例：
```code
*.js    eol=lf
*.jsx   eol=lf
*.json  eol=lf
*.php   eol=lf
*.gif   binary
*.png   binary
*.jpg   binary
```

把 .gitattributes 提交并推送到远程服务器
```bash
git add .gitattributes
git commit -m"Add .gitattributes"
git push
```
之后，任何人从 Git 仓库获取代码后，创建以及修改文件时，Git 都会按照配置文件中指定的文件类型自动地设置好正确的换行符。

## 常见问题及处理方法

1. 未提交冲突
```bash
# 本地修改完文件但并未执行 git commit 时，执行 git pull 去拉取远程仓库的代码，会报下面的错：
error: Your local changes to the following files would be overwritten by merge:
        bootstrap/route.php
Please, commit your changes or stash them before you can merge.
Aborting

# 解决方案1，如果修改内容无关紧要或仅仅为了测试可以先使用 git checkout 抹掉本地修改内容，之后再去拉取远程仓库的代码
git checkout bootstrap/route.php
git pull origin master

# 解决方案2，如果修改的内容很多又比较重要，可以使用 git commit 提交本地修改，之后再去拉取远程仓库的代码
git add bootstrap/route.php
git commit -m"路由文件修改"
git pull origin master # 这时 git 会自动合并文件，如果我们修改的是同一行代码，git 会报文件冲突，然后需要我们手动解决冲突内容后再次提交。
```

2. 已提交冲突
```bash
# 接上面，我们提交代码后再次拉取代码，报自动合并失败
# 当看到下面内容时，则表示自动合并失败，需要我们手动处理冲突
Auto-merging bootstrap/route.php
CONFLICT (content): Merge conflict in bootstrap/route.php
Automatic merge failed; fix conflicts and then commit the result.

# 使用 git status 查看文件状态，可以看到下面这行
Unmerged paths:
  (use "git add <file>..." to mark resolution)
  both modified:      bootstrap/route.php
```

3. 手动解决冲突

使用编辑器打开冲突文件可以看到具体的冲突内容：
```code
//健管
<<<<<<< HEAD
// 测试冲突
=======
require __DIR__.'/../routes/frontapi/jianguan.php';
>>>>>>> 1f372cd78bbdb3473338e2b7a447230305bca44a
```

<<<<<<< HEAD 到 ======= 中间的内容表示是当前你修改的内容，======= 到 >>>>>>> 的内容表示是从远程仓库拉取下来的内容，这里就需要根据自己的业务需要删除掉冲突部分的代码。

因为这里只是演示如何解决冲突，不牵扯具体业务代码，修改完后的文件内容如下：
```code
//健管
require __DIR__.'/../routes/frontapi/jianguan.php';
```

冲突解决完成后，重新提交代码并推送到远程仓库：
```bash
git add bootstrap/route.php
git commit -m"手动解决文件冲突"
git push origin master
```


## 参考文章
* [Git 工作流程](https://www.ruanyifeng.com/blog/2015/12/git-workflow.html)
* [Git最佳实践文档教程](https://mp.weixin.qq.com/s/D8Ds_CDdWtSE_k8Av49qng)