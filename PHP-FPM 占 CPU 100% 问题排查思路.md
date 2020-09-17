# PHP-FPM 占 CPU 100% 问题排查思路

## top命令

```bash
# 使用 top 命令查看服务器负载，找出当前 CPU 占用率高的进程ID
top
# 进入命令后输入大写 C 按 CPU 使用率由高到低排序，输入大写 M 按内存使用率排序
```

## 查看进程详情 /proc/PID

```bash
# 查看当前进程在哪个文件进行操作
ls -al /proc/PID/fd 
```

## strace 命令

```bash
# 查看当前进程所使用的系统调用及它所接收的系统信息
strace -p PID
```

## 几个比较重要的PHP-FPM参数

```
# 设置 PHP-FPM 进程管理器如何管理子进程。可用值：static，ondemand，dynamic。必须设置。
static # 子进程的数量是固定的（pm.max_children）。
ondemand # 进程在有需求时才产生（当请求时才启动。与 dynamic 相反，在服务启动时 pm.start_servers 就启动了。
dynamic # 子进程的数量在下面配置的基础上动态设置：pm.max_children，pm.start_servers，pm.min_spare_servers，pm.max_spare_servers。

# 502 Bad Gateway 服务器端 502 时，可能的一种情况就是 PHP-FPM 子进程因为执行超时而退出后，Nginx没有得到正常的返回值导致的。
request_terminate_timeout # 设置单个请求的超时中止时间。该选项可能会对 php.ini 设置中的 'max_execution_time' 因为某些特殊原因没有中止运行的脚本有用。设置为 '0' 表示 'Off'。可用单位：s（秒），m（分），h（小时）或者 d（天）。默认单位：s（秒）。默认值：0（关闭）。

# 设置 PHP-FPM 慢日志，当子进程执行超过该时间后将被记录到慢日志中
request_slowlog_timeout  # 当一个请求该设置的超时时间后，就会将对应的 PHP 调用堆栈信息完整写入到慢日志中。设置为 '0' 表示 'Off'。可用单位：s（秒），m（分），h（小时）或者 d（天）。默认单位：s（秒）。默认值：0（关闭）。

# 慢日志文件路径
slowlog # 慢请求的记录日志。默认值：#INSTALL_PREFIX#/log/php-fpm.log.slow。

# 最大可创建子进程数量，受限于内存限制，假设一个子进程占用 20MB，若服务器内存为 1G，则最大可设置的子进程数量为 (1GB x 0.7)/20 = 35。此值只是示例，具体设置应已自己服务器环境而定。不可照搬。
max_children # pm 设置为 static 时表示创建的子进程的数量，pm 设置为 dynamic 时表示最大可创建的子进程的数量。必须设置。该选项设置可以同时提供服务的请求数限制。类似 Apache 的 mpm_prefork 中 MaxClients 的设置和 普通PHP FastCGI中的 PHP_FCGI_CHILDREN 环境变量。

pm.min_spare_servers # 设置空闲服务进程的最小数目。仅在 pm 设置为 dynamic 时使用。必须设置。
pm.max_spare_servers # 设置空闲服务进程的最大数目。仅在 pm 设置为 dynamic 时使用。必须设置。

# PHP-FPM启动时默认启动子进程数量
pm.start_servers # 设置启动时创建的子进程数目。仅在 pm 设置为 dynamic 时使用。默认值：min_spare_servers + (max_spare_servers - min_spare_servers) / 2。

# 子进程可以处理请求的次数，若达到请求数，子进程将被销毁，然后PHP-FPM重新开启一个新的子进程。进程的销毁与创建都是需要销毁CPU资源的，太频繁的创建、销毁可能会导致CPU使用率上升。可以根据情况适当调大该值。
pm.max_requests # 设置每个子进程重生之前服务的请求数。对于可能存在内存泄漏的第三方模块来说是非常有用的。如果设置为 '0' 则一直接受请求，等同于 PHP_FCGI_MAX_REQUESTS 环境变量。默认值：0。
```

```
# PHP-FPM 试验配置
[www]
listen = /tmp/php-cgi-72.sock
listen.backlog = -1
listen.allowed_clients = 127.0.0.1
listen.owner = www
listen.group = www
listen.mode = 0666
user = www
group = www
pm = dynamic
pm.status_path = /phpfpm_72_status
; pm.max_children = 30
; pm.start_servers = 5
; pm.min_spare_servers = 5
; pm.max_spare_servers = 10
pm.max_children = 2 # 调低子进程数，方便使用strace追踪
pm.start_servers = 1
pm.min_spare_servers = 1
pm.max_spare_servers = 2
pm.max_requests = 5
; request_terminate_timeout = 100
; request_slowlog_timeout = 30
request_terminate_timeout = 5 # 调低子进程超时时间，手动创造 502 Bad Gateway
request_slowlog_timeout = 2
slowlog = var/log/slow.log
```