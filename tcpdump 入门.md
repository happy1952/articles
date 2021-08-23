# tcpdump 入门

今天使用 tcpdump 定位了一些线上接口超时问题，觉得这个东西真的好用。简单记录一些使用方法，方便自己以后参考。毕竟我们也不是天天使用它，时间久了难免也会忘记。

## 命令行格式
tcpdump [ -adeflnNOpqStvx ] [ -c 数量 ] [ -F 文件名 ][ -i 网络接口 ] [ -r 文件名] [ -s snaplen ][ -T 类型 ] [ -w 文件名 ] [表达式 ]

## 常用参数

* -i 选择要捕获的接口，通常是以太网或无线网卡，可以是 vlan 或其他特殊接口。如果系统只有一个网络接口，则无需指定
* -l 使标准输出变为缓冲形式
* -nn 单个 n 表示不解析域名，直接显示 IP 地址；两个 n 表示不解析域名和端口
* -c 在收到指定数量的包后，tcpdump 就会停止
* -w 直接将包写入文件中，并不分析和打印出来（之后可以将抓包文件下载到本地，使用 Wireshark 进行分析）
* -s 指定记录 package 的大小，常见-s 0，代表最大值 65535
* -X 直接输出 package data 数据，默认不设置，只能通过 -w 指定文件进行输出
* -p 不让网络进入混杂模式
* -e 显示链路层信息，tcpdump 默认情况下不会显示链路层信息，使用 -e 选项可以显示源和目的 MAC 地址，以及 VLAN tag 信息
* -A 表示使用 ASCII 字符串打印报文的全部数据，这样可以使读取更加简单，方便使用 grep 等工具解析输出内容
* -v 使用 -v, -vv, -vvv 来显示更多的详细信息

## 常用表达式

* 关于类型的关键词：
1. host
2. net
3. port

* 传输方向的关键词
1. src
2. dst
3. dst or src
4. dst and src

* 协议的关键词
1. fddi
2. ip
3. rarp
4. tcp
5. udp

* 逻辑预算
1. 'not', '!' 非运算
2. 'and', '&&' 与运算
3. 'or', '||' 或运算

* 其他重要的关键词
1. gateway
2. broadcast
3. less
4. greater

## 常用示例

1. http 数据包抓取（直接在终端输出 package data）

```bash
tcpdump tcp port 80 -n -X -s 0 # 指定 80 端口进行输出
```

2. http 数据包抓取（向指定文件输出 package data）

```bash
tcpdump tcp port 80 -n -s 0 -w /tmp/tcp.cap # 可以将数据包文件下载到本地后使用 Wireshark 进行分析
```

3. 结合管道流

```bash
tcpdump tcp port 80 -n -s 0 -X | grep xxx
```

4. mod_proxy 反向代理抓包

线上服务器我们使用 nginx 做了反向代理。（80 ==> 8080）

```bash
# 抓取 80 端口的数据：
tcpdump -i eth0 tcp port 80 -n -s 0 -w /tmp/eth0_80.cap

# 抓取 8080 端口的数据：
tcpdump -i lo tcp port 8080 -n -s 0 -w /tmp/lo_8080.cap
```

5. 只监控特定 IP 的主机

```bash
tcpdump tcp host 10.0.2.2 and port 8083 -s 0 -X # 这里 host 指示只监听该 IP
```

6. 只抓取 HTTP GET 请求

```bash
# 0x474554 对应 ASCII 码的 GET
# 0x20 对应 ASCII 码的空格
tcpdump -s 0 -A -vv 'tcp[((tcp[12:1] & 0xf0) >> 2):4] = 0x47455420'
```

7. 只抓取 HTTP POST 请求

```bash
tcpdump -s 0 -A -vv 'tcp[((tcp[12:1] & 0xf0) >> 2):4] = 0x504f5354'
```

## 参考

[linux tcpdump抓取HTTP包的详细解释](https://blog.51cto.com/u_12295205/3153163)

[Tcpdump 示例教程](https://fuckcloudnative.io/posts/tcpdump-examples/)