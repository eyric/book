# 计算机网络思维导图

![](../.gitbook/assets/image%20%28120%29.png)

## 网络

### 基础

#### 体系结构

* 物理层（通信方式、带通调制）、数据链路层（封装成帧、透明传输、差错检测）、网络层、传输层、会话层、表示层、应用层

#### 以太网

* 一种星形拓扑结构的局域网

#### 网络硬件设备

* 集线器，交换机（一种链路层设备，它不会发生碰撞，能根据 MAC 地址进行存储转发），路由器

#### IP 数据报

*     版本 : 有 4（IPv4）和 6（IPv6）两个值；

      首部长度 : 占 4 位，因此最大值为 15。值为 1 表示的是 1 个 32 位字的长度，也就是 4 字节。因为首部固定长度为 20 字节，因此该值最小为 5。如果可选字段的长度不是 4 字节的整数倍，就用尾部的填充部分来填充。

      区分服务 : 用来获得更好的服务，一般情况下不使用。

      总长度 : 包括首部长度和数据部分长度。

      生存时间 ：TTL，它的存在是为了防止无法交付的数据报在互联网中不断兜圈子。以路由器跳数为单位，当 TTL 为 0 时就丢弃数据报。

      协议 ：指出携带的数据应该上交给哪个协议进行处理，例如 ICMP、TCP、UDP 等。

      首部检验和 ：因为数据报每经过一个路由器，都要重新计算检验和，因此检验和不包含数据部分可以减少计算的工作量。

      标识 : 在数据报长度过长从而发生分片的情况下，相同数据报的不同分片具有相同的标识符。

      片偏移 : 和标识符一起，用于发生分片的情况。片偏移的单位为 8 字节。

#### ARP 协议

* 实现由 IP 地址得到 MAC 地址

#### ICMP 协议（差错报告报文、询问报文）

* ping
* Traceroute

#### UDP 与 TCP

*     用户数据报协议 UDP（User Datagram Protocol）是无连接的，尽最大可能交付，没有拥塞控制，面向报文（对于应用程序传下来的报文不合并也不拆分，只是添加 UDP 首部），支持一对一、一对多、多对一和多对多的交互通信。

      传输控制协议 TCP（Transmission Control Protocol）是面向连接的，提供可靠交付，有流量控制，拥塞控制，提供全双工通信，面向字节流（把应用层传下来的报文看成字节流，把字节流组织成大小不等的数据块），每一条 TCP 连接只能是点对点的（一对一）。

#### TCP 连接

* TCP首部
  *     序号 ：用于对字节流进行编号，例如序号为 301，表示第一个字节的编号为 301，如果携带的数据长度为 100 字节，那么下一个报文段的序号应为 401。

        确认号 ：期望收到的下一个报文段的序号。例如 B 正确收到 A 发送来的一个报文段，序号为 501，携带的数据长度为 200 字节，因此 B 期望下一个报文段的序号为 701，B 发送给 A 的确认报文段中确认号就为 701。

        数据偏移 ：指的是数据部分距离报文段起始处的偏移量，实际上指的是首部的长度。

        确认 ACK ：当 ACK=1 时确认号字段有效，否则无效。TCP 规定，在连接建立后所有传送的报文段都必须把 ACK 置 1。

        同步 SYN ：在连接建立时用来同步序号。当 SYN=1，ACK=0 时表示这是一个连接请求报文段。若对方同意建立连接，则响应报文中 SYN=1，ACK=1。

        终止 FIN ：用来释放一个连接，当 FIN=1 时，表示此报文段的发送方的数据已发送完毕，并要求释放连接。

        窗口 ：窗口值作为接收方让发送方设置其发送窗口的依据。之所以要有这个限制，是因为接收方的数据缓存空间是有限的。

#### TCP 可靠传输

* 超时重传来实现可靠传输
* 滑动窗口实现流量控制

#### TCP 拥塞控制

* 慢开始、拥塞避免、快重传、快恢复

#### DNS

#### 三次握手与四次挥手

* 三次握手
  * 流程
    * 假设 A 为客户端，B 为服务器端。

          首先 B 处于 LISTEN（监听）状态，等待客户的连接请求。

          A 向 B 发送连接请求报文，SYN=1，ACK=0，选择一个初始的序号 x。

          B 收到连接请求报文，如果同意建立连接，则向 A 发送连接确认报文，SYN=1，ACK=1，确认号为 x+1，同时也选择一个初始的序号 y。

          A 收到 B 的连接确认报文后，还要向 B 发出确认，确认号为 y+1，序号为 x+1。

          B 收到 A 的确认后，连接建立。
* 原因
  * 第三次握手是为了防止失效的连接请求到达服务器，让服务器错误打开连接。

    客户端发送的连接请求如果在网络中滞留，那么就会隔很长一段时间才能收到服务器端发回的连接确认。客户端等待一个超时重传时间之后，就会重新请求连接。但是这个滞留的连接请求最后还是会到达服务器，如果不进行三次握手，那么服务器就会打开两个连接。如果有第三次握手，客户端会忽略服务器之后发送的对滞留连接请求的连接确认，不进行第三次握手，因此就不会再次打开连接。
* 四次挥手
  * 流程
    * 以下描述不讨论序号和确认号，因为序号和确认号的规则比较简单。并且不讨论 ACK，因为 ACK 在连接建立之后都为 1。

          A 发送连接释放报文，FIN=1。

          B 收到之后发出确认，此时 TCP 属于半关闭状态，B 能向 A 发送数据但是 A 不能向 B 发送数据。

          当 B 不再需要连接时，发送连接释放报文，FIN=1。

          A 收到后发出确认，进入 TIME-WAIT 状态，等待 2 MSL（最大报文存活时间）后释放连接。

          B 收到 A 的确认后释放连接。
* 原因
  * 客户端发送了 FIN 连接释放报文之后，服务器收到了这个报文，就进入了 CLOSE-WAIT 状态。这个状态是为了让服务器端发送还未传送完毕的数据，传送完毕之后，服务器会发送 FIN 连接释放报文。

    TIME\_WAIT 

    客户端接收到服务器端的 FIN 报文后进入此状态，此时并不是直接进入 CLOSED 状态，还需要等待一个时间计时器设置的时间 2MSL。这么做有两个理由：

        确保最后一个确认报文能够到达。如果 B 没收到 A 发送来的确认报文，那么就会重新发送连接释放请求报文，A 等待一段时间就是为了处理这种情况的发生。

        等待一段时间是为了让本连接持续时间内所产生的所有报文都从网络中消失，使得下一个新的连接不会出现旧的连接请求报文。

#### 协议

* CDMA/CD
  *     多点接入 ：说明这是总线型网络，许多主机以多点的方式连接到总线上。

        载波监听 ：每个主机都必须不停地监听信道。在发送前，如果监听到信道正在使用，就必须等待。

        碰撞检测 ：在发送中，如果监听到信道已有其它主机正在发送数据，就表示发生了碰撞。虽然每个主机在发送数据之前都已经监听到信道为空闲，但是由于电磁波的传播时延的存在，还是有可能会发生碰撞。
* PPP
  * PPP 协议是用户计算机和 ISP 进行通信时所使用的数据链路层协议
* FTP
* DHCP
* TELNET

### HTTP

#### GET 与 POST

* 作用、参数、安全、幂等、可缓存性、XmlHttpRequest

#### 状态码

* 状态码    类别    含义

  1XX    Informational（信息性状态码）    接收的请求正在处理

  2XX    Success（成功状态码）    请求正常处理完毕

  3XX    Redirection（重定向状态码）    需要进行附加操作以完成请求

  4XX    Client Error（客户端错误状态码）    服务器无法处理请求

  5XX    Server Error（服务器错误状态码）    服务器处理请求出错

#### Cookie

#### 缓存

#### 连接管理

#### HTTPs

* HTTPS 并不是新协议，而是让 HTTP 先和 SSL（Secure Sockets Layer）通信，再由 SSL 和 TCP 通信，也就是说 HTTPS 使用了隧道进行通信。 通过使用 SSL，HTTPS 具有了加密（防窃听）、认证（防伪装）和完整性保护（防篡改）。
* 流程
  * 　　（1）客户使用https的URL访问Web服务器，要求与Web服务器建立SSL连接。

（2）Web服务器收到客户端请求后，会将网站的证书信息（证书中包含公钥）传送一份给客户端。

（3）客户端的浏览器与Web服务器开始协商SSL连接的安全等级，也就是信息加密的等级。

（4）客户端的浏览器根据双方同意的安全等级，建立会话密钥，然后利用网站的公钥将会话密钥加密，并传送给网站。

（5）Web服务器利用自己的私钥解密出会话密钥。

（6）Web服务器利用会话密钥加密与客户端之间的通信。

* 加密：HTTPS 采用混合的加密机制，使用非对称密钥加密用于传输对称密钥来保证传输过程的安全性，之后使用对称密钥加密进行通信来保证通信过程的效率。
* 认证：证书
* 完整性保护：报文摘要

#### HTTP/2

* 二进制分帧
* 服务端推送
* 首部压缩

#### 版本比较

#### HTTP 与 FTP

### Socket

#### I/O 模型

#### 多路复用

* select
* poll
* epoll

#### Java NIO

\_\_

