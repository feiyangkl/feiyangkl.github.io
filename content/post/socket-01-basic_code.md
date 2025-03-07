
---
title: "Socket 基本演练"

date: "2016-03-21 10:29:32"

tags: 
    - iOS
---
* 导入头文件

```objc
#import <sys/socket.h>
#import <netinet/in.h>
#import <arpa/inet.h>
```

* 定义属性

```objc
@interface ViewController ()
/// 客户端 socket
@property (nonatomic, assign) int clientSocket;
@end
```

<!-- more --> 

* 建立 socket

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

NSLog(@"%d", self.clientSocket);
```

* 连接到主机

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

* 调用连接到主机

```objc
if (![self connectToHost]) {
    NSLog(@"失败");
    return;
}
NSLog(@"成功");
```

* 发送消息

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
NSString *msg = @"约?";
ssize_t sendLen = send(clientSocket, msg.UTF8String, strlen(msg.UTF8String), 0);
NSLog(@"发送 %ld %tu %ld", sendLen, msg.length, strlen(msg.UTF8String));
```

* 接收消息

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
NSLog(@"接收了 %ld %ld", recvLen, sizeof(buffer));

NSString *result = [[NSString alloc] initWithBytes:buffer length:recvLen encoding:NSUTF8StringEncoding];

return result;
```

* 断开连接

```objc
- (void)disconnect {
    close(self.clientSocket);
}
```

