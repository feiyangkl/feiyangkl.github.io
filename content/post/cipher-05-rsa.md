
---
title: "非对称加密算法"

date: "2016-10-01 10:29:59"

tags: 
    - iOS
---
> 提示：加密内容属于高级程序员的话题！有些内容会很枯燥！注意掌握加密的思路和操作步骤即可！代码不要求会写，只要会用就行！

## 介绍

* 非对称加密算法需要两个密钥：`公开密钥（publickey）` 和 `私有密钥（privatekey）`
* 公开密钥与私有密钥是一对，如果用公开密钥对数据进行加密，只有用对应的私有密钥才能解密；如果用私有密钥对数据进行加密，那么只有用对应的公开密钥才能解密
<!-- more --> 
## 特点

* 算法强度复杂、安全性依赖于算法与密钥
* 加密解密速度慢

## 与对称加密算法的对比

* 对称加密只有一种密钥，并且是非公开的，如果要解密就得让对方知道密钥
* 非对称密钥体制有两种密钥，其中一个是公开的

## RSA 算法原理

1. 找出两个“很大”的质数：P & Q
    * N = P * Q
    * M = (P – 1) * (Q – 1)
2. 找出整数E，E与M互质，即除了1之外，没有其他公约数
3. 找出整数D，使得 E*D 除以 M 余 1，即 (E * D) % M = 1
4. 经过上述准备工作之后，可以得到：
    * E是公钥，负责加密
    * D是私钥，负责解密
    * N负责公钥和私钥之间的联系
5. 加密算法，假定对X进行加密
    * (X ^ E) % N = Y
6. 解密算法，根据费尔马小定义，可以使用以下公式完成解密
    * (Y ^ D) % N = X

## 代码演练

```objc
CryptorTools *tools = [[CryptorTools alloc] init];

// 1. 加载公钥
NSString *pubPath = [[NSBundle mainBundle] pathForResource:@"rsacert.der" ofType:nil];
[tools loadPublicKeyWithFilePath:pubPath];

// 2. 使用公钥加密，加密内容最大长度 117
NSString *result = [tools RSAEncryptString:@"abcdefghijklmnopqrstuvwxyz1234567890abcdefghijklmnopqrstuvwxyz1234567890abcdefghijklmnopqrstuvwxyz1234567890abcdefghi"];
NSLog(@"RSA 加密 %@", result);

// 3. 加载私钥，并指定导出 p12 时设置的密码
NSString *privatePath = [[NSBundle mainBundle] pathForResource:@"p.p12" ofType:nil];
[tools loadPrivateKey:privatePath password:@"123"];

// 4. 使用私钥解密
NSLog(@"解密结果 %@", [tools RSADecryptString:result]);

```

### iOS 相关函数

| 函数 | 说明 |
| -- | -- |
| SecKeyEncrypt | 使用公钥对数据加密 |
| SecKeyDecrypt | 使用私钥对数据解密 |
| SecKeyRawVerify | 使用公钥对数字签名进行验证 |
| SecKeyRawSign | 使用私钥生成数字签名 |

## 应用场景

由于 `RSA` 算法的加密解密速度要比对称算法的速度慢很多，在实际应用中，通常采取：

* 数据本身的加密解密使用对称加密算法(AES)
* 用 `RSA` 算法加密并传输对称算法所需的密钥

## OpenSSL 终端测试命令

### 程序开发证书生成

```bash
# 生成私钥
$ openssl genrsa -out private.pem 1024
# 创建证书请求
$ openssl req -new -key private.pem -out rsacert.csr
# 生成证书并签名，有效期10年
$ openssl x509 -req -days 3650 -in rsacert.csr -signkey private.pem -out rsacert.crt
# 转换格式-将 PEM 格式文件转换成 DER 格式
$ openssl x509 -outform der -in rsacert.crt -out rsacert.der
# 导出P12文件
$ openssl pkcs12 -export -out p.p12 -inkey private.pem -in rsacert.crt
```

> 在苹果开发中，不能直接使用 PEM 格式的证书

* `DER` 文件是 `CRT` 文件的 `BASE 64` 解码前的二进制数据文件
* `OpenSSL` 默认生成的都是 `PEM` 格式的证书（BASE64 编码后的文本文件）

### 数字签名

* 发送方
    * `报文` HASH 得到 `报文摘要`
    * `报文摘要` 用公钥加密(数字签名) + 报文
    * 发送给接收方
* 接收方
    * `报文` HASH 得到 `报文摘要`
    * `数字签名` 用私钥解密 判断是否与 `报文摘要` 相同

### OpenSSL 生成密钥演练

#### 私钥 & 公钥

```bash
# 生成强度是 512 的 RSA 私钥
$ openssl genrsa -out private.pem 512
# 以明文输出私钥内容
$ openssl rsa -in private.pem -text -out private.txt
# 校验私钥文件
$ openssl rsa -in private.pem -check
# 从私钥中提取公钥
$ openssl rsa -in private.pem -out public.pem -outform PEM -pubout
# 以明文输出公钥内容
$ openssl rsa -in public.pem -out public.txt -pubin -pubout -text
```

#### 加密 & 解密

```bash
# 使用公钥加密小文件
$ openssl rsautl -encrypt -pubin -inkey public.pem -in msg.txt -out msg.bin
# 使用私钥解密小文件
$ openssl rsautl -decrypt -inkey private.pem -in msg.bin -out a.txt
```

#### 格式转换

```bash
# 将私钥转换成 DER 格式
$ openssl rsa -in private.pem -out private.der -outform der
# 将公钥转换成 DER 格式
$ openssl rsa -in public.pem -out public.der -pubin -outform der
```

### PKCS

* Public-Key Cryptography Standards (PKCS)是由美国 RSA 数据安全公司及其合作伙伴制定的一组公钥密码学标准，其中包括证书申请、证书更新、证书作废表发布、扩展证书内容以及数字签名、数字信封的格式等方面的一系列相关协议

#### PKCS协议

| 协议 | 说明 |
| -- | -- |
| PKCS#1 | 定义 RSA 公开密钥算法加密和签名机制，主要用于组织 PKCS#7 中所描述的数字签名和数字信封（专门用来加密/解密） |
| PKCS#8 | 描述私有密钥信息格式，该信息包括公开密钥算法的私有密钥以及可选的属性集等（JAVA使用） |
| PKCS#12 | 描述个人信息交换语法标准。描述了将用户公钥、私钥、证书和其他相关信息打包的语法（苹果使用） |
