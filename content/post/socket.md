
---
title: "socket"
date: "2016-03-20 10:29:32"

tags: 
    - iOS
---
## 网络通讯三要素

* IP地址(主机名)
    * 网络中设备的标示
    * 不易记忆，可以用主机名
    * 本地回环地址：`127.0.0.1` 主机名：`localhost`
        * 每台计算机都有一个 `127.0.0.1`
        * 如果 127.0.0.1 `ping` 不通，说明网卡不工作
        * 如果本机地址 `ping` 不通，说明网线坏了

* 端口号
    * 用于标示进程的逻辑地址，不同进程的标示
    * 有效端口：`0~65535`
    * 其中 `0~1024`由系统使用或者保留端口
    * 开发中不要使用 `1024` 以下的端口
* 传输协议
    * TCP(传输控制协议)
    * UDP(数据报文协议)

> 通过 `IP` 找机器，通过 `端口` 找程序，通过 `协议` 确定如何传输数据

<!-- more --> 
## 网络模型

![](/images/OSI网络模型示意图.png)

### 网络参考模型

![](/images/网络参考模型.png)

### 常见网络协议

| 协议 | 端口 | 说明 |
| -- | -- | -- |
| HTTP | 80 | 超文本传输协议 |
| HTTPS | 443 | HTTP+SSL，HTTP的安全版 |
| FTP | 20/21/990 | 文件传输协议 |
| POP | 110 | 邮局协议 |
| SMTP | 25 | 简单邮件传输协议 |
| telnet | 23 | 远程终端协议 |
| SSH | 22 | Secure Shell |


## TCP & UDP

* UDP（用户数据报协议）
    * **只管发送，不确认对方是否接收到**
    * 将数据及源和目的封装成数据包中，不需要建立连接
    * 每个数据报的`大小限制在64K之内`
    * 因为`无需连接`，因此是`不可靠协议`
    * 不需要建立连接，`速度快`
    * 应用场景：多媒体教室／网络流媒体

* TCP（传输控制协议）
    * 建立连接，**形成传输数据的通道**
    * 在连接中进行`大数据传输`（数据大小不收限制）
    * 通过三次握手完成连接，是`可靠协议，安全送达`
    * 必须建立连接，`效率会稍低`

## Socket（套接字层、插座）

* `Socket`就是为网络服务提供的一种机制
* 通信的两端都是 `Socket`
* 网络通信其实就是 `Socket` 间的通信
* 数据在两个 `Socket` 间通过 `IO` 传输
* `Socket` 是`纯C语言的，是跨平台的`

### 通讯示意图

![](/images/socket原理.jpg)

### 辅助工具 —— NetCat

* 是终端下用于调试和检查网络的工具包
* 可用于创建 TCP/IP 连接
* 进入终端，输入以下指令

```bash
# 始终监听本地计算机 12345 端口的数据
$ nc -lk 12345
```

### 函数注释

* socket

```objc
/**
 参数

 domain:    协议域，AF_INET（IPV4的网络开发）
 type:      Socket 类型，SOCK_STREAM(TCP)/SOCK_DGRAM(UDP，报文)
 protocol:  IPPROTO_TCP，协议，如果输入0，可以根据第二个参数，自动选择协议

 返回值
 socket，如果 > 0 就表示成功
 */
self.clientSocket = socket(AF_INET, SOCK_STREAM, 0);
```

* connect

```objc
/**
 参数
 1> 客户端socket
 2> 指向数据结构sockaddr的指针，其中包括目的端口和IP地址
     服务器的"结构体"地址
     提示：C 语言中没有对象
 3> 结构体数据长度

 返回值
    0 成功/其他 错误代号，非0即真
 */
struct sockaddr_in serverAddr;
serverAddr.sin_family = AF_INET;
serverAddr.sin_addr.s_addr = inet_addr("127.0.0.1");
serverAddr.sin_port = htons(12345);

return connect(self.clientSocket, (const struct sockaddr *)&serverAddr, sizeof(serverAddr)) == 0;
```

* send

```objc
/**
 参数
 1> 客户端socket
 2> 发送内容地址 void * == id
 3> 发送内容长度
 4> 发送方式标志，一般为0

 返回值
    如果成功，则返回发送的字节数，失败则返回SOCKET_ERROR
 */
NSString *msg = @"hello socket";
ssize_t sendLen = send(clientSocket, msg.UTF8String, strlen(msg.UTF8String), 0);
```

* recv

```objc
/**
 参数
 1> 客户端socket
 2> 接收内容缓冲区地址
 3> 接收内容缓存区长度
 4> 接收方式，0表示阻塞，必须等待服务器返回数据

 返回值
    如果成功，则返回读入的字节数，失败则返回SOCKET_ERROR
 */
uint8_t buffer[1024];
ssize_t recvLen = recv(self.clientSocket, buffer, sizeof(buffer), 0);
```

* close

```objc
close(self.clientSocket);
```

## 长/短连接 & 心跳包

* 短连接
    * 数据请求结束后，立即断开连接
    * 能够及时释放服务器资源
    * 让服务器能够为更多的用户提供服务

* 长连接
    * 一旦连接建立之后，始终保持连接状态
	* 后续只需发送和接收数据即可，数据响应更及时
	* 长连接对服务器资源占用比较大
	* 对交互响应要求快的应用，例如即时通讯，需要使用长连接

* 心跳包
    * 是检测长连接的重要技术手段
    * 可以由服务器发送
        * 定时向客户端发送小数据，根据回执判断客户端是否在线
    * 也可以由客户端发送
        * 定时向服务器发送小数据，报告客户端当前在线



