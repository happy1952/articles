# QPS、TPS、PV、UV、IP、RPS、Throughput、并发数概念详解

## 概览

* QPS（Queries Per Second）每秒查询数
* TPS（Transactions Per Second）每秒事务处理数
* PV（Page View）页面浏览量
* UV（Unique Visitor）独立访客数
* DAU（Daily Active User）日活跃用户数量
* MAU（Month Active User）月活跃用户数量
* IP（Internet Protocol）独立IP数
* RPS（Requests Per Second）每秒处理的请求数，等效于QPS
* Throughput 吞吐量
* 并发数 指同时访问服务器站点的连接数

### QPS - Queries Per Second
QPS 是指对一个特定的查询服务器在规定时间内所处理流量多少的衡量标准，即每秒的响应请求数，也即是最大吞吐能力。

### TPS - Transactions Per Second
TPS 是指每秒事务处理数。一个事务是指一个客户机向服务器发送请求然后服务器做出反应的过程。客户机在发送请求时开始计时，收到服务器响应后结束计时，以此来计算使用的时间和完成的事务个数，最终利用这些信息作出的评估分。

TPS 的过程包括：客户端请求服务端、服务端内部处理、服务端返回客户端。

例如：访问一个 Index 页面会请求服务器3次，包括一次 html，一次 css，一次 js，那么这次访问就会产生一个 TPS，三个 QPS。

### PV - Page View
Page View 即页面浏览量。用户每次刷新页面即被记录1次。多次刷新，访问量累计。根据这个特性，刷网站PV就很好刷了。

与PV相关的还有RV，即重复访问者数量 Repeat Visitors。

### UV - Unique Visitor
Unique Visitor 即独立访客数。统计一天内访问某站点的用户数，可以统计服务器一天的访问日志并根据用户的唯一标识（COOKIE等）去重得到。

### DAU - Daily Active User
Daily Active User 是指日活跃用户数量。常用于反应网站、互联网应用或网络游戏的运营情况。DAU通常统计一天内登录或使用某个产品的用户数量（去除重复登录的用户），与UV概念相似。

### MAU - Month Active User
Month Active User 是指月活跃用户数量。通常DAU会结合MAU一起使用，这两个指标一般用来衡量服务的用户粘性及服务的衰退周期。MAU、DAU分别从宏观和微观的角度对服务的用户粘性做了权衡。MAU更像战略层面的表征，DAU更像战术层面的表征。通过这些趋势变化，可以对营销及推广提供一些数据支持或帮助。

### IP - Internet Protocol
Internet Protocol 独立IP数。是指一天内多少个独立IP浏览了页面，即统计不同的IP浏览用户数量。同一IP不管访问了几个页面，独立IP数均为1。

### RPS - Requests Per Second
Requests Per Second 每秒请求数，等效于QPS。

### Throughput
吞吐量是指系统在单位时间内处理请求的数量。对于无并发的应用系统而言，吞吐量与响应时间成严格的反比关系，实际上此时吞吐量就是响应时间的倒数。前面已经说过，对于单用户的系统，响应时间（或者系统响应时间和应用延迟时间）可以很好地度量系统的性能，但对于并发系统，通常需要用吞吐量作为性能指标。 

一个系统的吞度量（承压能力）与 Request 对CPU的消耗、外部接口、IO等等紧密关联。单个 Reqeust 对CPU消耗越高，外部系统接口、IO影响速度越慢。系统吞吐能力越低。

系统吞吐量几个重要參数：QPS（TPS）、并发数、响应时间:

* QPS（TPS）：每秒钟请求/事务处理数量
* 并发数：系统同一时间处理的请求/事务数量
* 响应时间（RT）：是指系统对请求作出响应的时间，一般取平均响应时间。 

理解了上面三个要素的意义之后，就能推算出它们之间的关系：

QPS(TPS) = 并发数 / 平均响应时间

或者

并发数 = QPS * 平均响应时间 

一个系统的吞吐量通常由QPS（TPS）、并发数两个因素决定，每套系统这两个值都有一个相对极限值。在应用场景访问压力下，仅仅要某一项达到系统最高值，系统的吞吐量就上不去了。假设压力继续增大，系统的吞吐量反而会下降。原因是系统超负荷工作，上下文切换、内存等等其他消耗导致系统性能下降。

### 并发数
并发数是指系统同时能处理的请求数量，同样反应了系统的负载能力。
