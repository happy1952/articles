# Linux 下 history 命令显示用户和执行时间

编辑配置文件：

```bash
sudo vim /etc/profile
```

将如下内容写入文件：

```shell
export HISTTIMEFORMAT="%F %T $(whoami) "
```

执行以下命令使配置生效

```bash
source /etc/profile
```

验证

```bash
history
```

```
  997  2023-04-26 15:58:31 root cd /data/
  998  2023-04-26 15:58:39 root npm run dev
  999  2023-04-26 16:01:37 root cd ~
 1000  2023-04-26 16:01:41 root pwd -P
 1001  2023-04-26 18:00:37 root ls -al
```