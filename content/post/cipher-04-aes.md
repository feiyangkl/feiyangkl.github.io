
---
title: "对称加密算法"

date: "2016-10-01 10:29:32"

tags: 
    - iOS
---
> 提示：加密内容属于高级程序员的话题！有些内容会很枯燥！注意掌握加密的思路和操作步骤即可！代码不要求会写，只要会用就行！

* 又称传统加密算法
* 加密和解密使用同一个`密钥`

## 对称加密算法示例

* 密钥：X
* 加密算法：每个字符+X
* 明文：`Hello`
* 密钥为 `1` 时加密结果：`Ifmmp`
* 密钥为 `2` 时加密结果：`Jgnnq`
<!-- more --> 
## 优缺点

* 优点
    * 算法公开、计算量小、加密速度快、加密效率高
* 缺点
    * 双方使用相同钥匙，安全性得不到保证

## 注意事项

* 密钥的保密工作非常重要
* 密钥要求定期更换

## 经典算法

| 算法 | 说明 |
| -- | -- |
| DES | 数据加密标准（用的少，因为强度不够） |
| 3DES | 使用3个密钥，对相同的数据执行三次加密，强度增强 |
| AES | 高级加密标准，目前美国国家安全局使用的<br />苹果的钥匙串访问采用的是 AES 加密 |

## ECB & CBC

* ECB ：电子代码本，就是说每个块都是独立加密的
* CBC ：密码块链，使用一个密钥和一个初始化向量 (IV)对数据执行加密转换

### OpenSSL 终端测试命令

#### ECB

```bash
# 加密
$ openssl enc -des-ecb -K 616263 -nosalt -in msg1.txt -out msg1.bin
# 解密
$ openssl enc -des-ecb -K 616263 -nosalt -in msg1.bin -out msg1.txt -d
# 查看加密之后的二进制文件
$ xxd msg1.bin
```

#### CBC

```bash
# 加密
$ openssl enc -des-cbc -K 616263 -iv 0000000000000000 -nosalt -in a.txt -out msg1.bin
# 解密
$ openssl enc -des-cbc -K 616263 -iv 0000000000000000 -nosalt -in msg1.bin -out msg4.txt -d
# 查看加密之后的二进制文件
$ xxd msg1.bin
```

> CBC加密可以有效地保证密文的完整性，也就是说如果有一个块在传送时丢失了（或被敌人改变了），就会导致后面所有的块无法正常解密这个特性可以用来防范一些窃听技巧

## 代码演练

* AES

```objc
NSString *key = @"abc";
// ECB 加密&解密
NSString *str1 = [CryptorTools AESEncryptString:@"hello" keyString:key iv:nil];
NSLog(@"AES ECB 加密 %@", str1);
NSLog(@"AES ECB 解密 %@", [CryptorTools AESDecryptString:str1 keyString:key iv:nil]);

// CBC 加密&解密
uint8_t iv[8] = {1, 2, 3, 4, 5, 6, 7, 8};
NSData *ivData = [NSData dataWithBytes:iv length:sizeof(iv)];
NSString *str2 = [CryptorTools AESEncryptString:@"hello" keyString:key iv:ivData];
NSLog(@"AES CBC 加密 %@", str2);
NSLog(@"AES CBC 解密 %@", [CryptorTools AESDecryptString:str2 keyString:key iv:ivData]);
```

* DES

```objc
NSString *key = @"abc";
// ECB 加密&解密
NSString *str1 = [CryptorTools DESEncryptString:@"hello" keyString:key iv:nil];
NSLog(@"DES ECB 加密 %@", str1);
NSLog(@"DES ECB 解密 %@", [CryptorTools DESDecryptString:str1 keyString:key iv:nil]);

// CBC 加密&解密
uint8_t iv[8] = {1, 2, 3, 4, 5, 6, 7, 8};
NSData *ivData = [NSData dataWithBytes:iv length:sizeof(iv)];
NSString *str2 = [CryptorTools DESEncryptString:@"hello" keyString:key iv:ivData];
NSLog(@"DES CBC 加密 %@", str2);
NSLog(@"DES CBC 解密 %@", [CryptorTools DESDecryptString:str2 keyString:key iv:ivData]);
```

## OpenSSL 终端测试命令

### ECB 加密/解密

* AES

```bash
# AES(ECB)加密
$ echo -n "hello" | openssl enc -aes-128-ecb -K 616263 -nosalt | base64

# AES(ECB)解密
$ echo -n "d1QG4T2tivoi0Kiu3NEmZQ==" | base64 -D | openssl enc -aes-128-ecb -K 616263 -nosalt -d
```

* DES

```bash
# DES(ECB)加密
$ echo -n "hello" | openssl enc -des-ecb -K 616263 -nosalt | base64

# DES(ECB)解密
$ echo -n "HQr0Oij2kbo=" | base64 -D | openssl enc -des-ecb -K 616263 -nosalt -d
```

### CBC 加密/解密

* AES

```bash
# AES(CBC)加密
$ echo -n "hello" | openssl enc -aes-128-cbc -iv 0102030405060708 -K 616263 -nosalt | base64

AES(CBC)解密
$ echo -n "u3W/N816uzFpcg6pZ+kbdg==" | base64 -D | openssl enc -aes-128-cbc -iv 0102030405060708 -K 616263 -nosalt -d
```

* DES

```bash
# DES(CBC)加密
$ echo -n "hello" | openssl enc -des-cbc -K 616263 -nosalt -iv 0102030405060708 | base64

# DES(CBC)解密
$ echo -n "alvrvb3Gz88=" | base64 -D | openssl enc -des-cbc -K 616263 -nosalt -iv 0102030405060708 -d
```

终端命令说明

* 加密过程是先加密，再base64编码
* 解密过程是先base64解码，再解密
* `|` 是终端管道命令，会将前一个命令结果传递给后一个命令
* `-K` 使用的密钥需要是密钥的 ASCII 码
