# Ubuntu 释放磁盘空间的几种方法

1. 清除 apt 缓存

```bash
# 查看 apt 缓存占用空间大小
sudo du -sh /var/cache/apt/archives

# 清除 apt 缓存
sudo apt clean

# 删除过时的软件包
sudo apt autoclean
```

2. 删除旧的内核

```bash
sudo apt autoremove --purge
```

3. 卸载从未使用过的应用程序

```bash
# 删除不再需要的程序包和依赖项
sudo apt autoremove

# 删除指定软件包，这里的 package-name1 package-name2 就是你要删除的软件包的名称，多个软件包之间使用空格分隔。
sudo apt remove package-name1 package-name2
```

4. 清除 systemd 日志

```bash
# 查看日志文件的大小
journalctl --disk-usage

# 清除超过指定时间的旧日志，如：1d 以天为单位；2h 以小时为单位；2m 以分钟为单位；10s 以秒为单位；等等
sudo journalctl --vacuum-time=1d

# 清除超过指定大小的旧日志，如：1G 以 GB 为单位；10M 以 MB 为单位；100K 以 KB 为单位；等等
sudo journalctl --vacuum-size=100M
```

5. 清除旧的 Snap 应用

```bash
# 查看 snap 占用空间大小
sudo du -h /var/lib/snapd/snaps
```

创建 remove_snap.sh 脚本，将下面的代码粘贴进去，然后赋予文件可执行权限 `sudo chmod a+x remove_snap.sh`，然后执行 `sudo ./remove_snap.sh`。

```shell
#!/bin/bash
# Removes old revisions of snaps
# CLOSE ALL SNAPS BEFORE RUNNING THIS
set -eu
snap list --all | awk '/disabled/{print $1, $3}' |
    while read snapname revision; do
        snap remove "$snapname" --revision="$revision"
    done
```

6. 清除缩略图缓存

```bash
# 查看缩略图缓存大小
du -sh ~/.cache/thumbnails

# 删除缩略图缓存
rm -rf ~/.cache/thumbnails/*
```

清理完成之后可以使用 `sudo df -h` 命令查看下清理效果。