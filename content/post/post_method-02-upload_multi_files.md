
---
title: "上传多个文件"

date: "2016-04-25 10:29:32"

tags: 
    - iOS
---
## 需求

* 有些服务器可以在上传文件的同时，提交一些文本内容给服务器
* 典型应用：新浪微博，上传图片的同时，发送一个微博！

## 上传原理

* 用 firebug 演示 upload-m.php
* 注意修改 abc 文件夹的访问权限

<!-- more --> 

## 上传多个文件

* 请求格式

```
Content-Type: multipart/form-data; boundary=boundary
```

* 数据格式

```
--boundary\r\n
Content-Disposition: form-data; name="userfile[]"; filename="aaa.txt"\r\n
Content-Type: application/octet-stream\r\n\r\n

二进制数据
\r\n
--boundary\r\n
Content-Disposition: form-data; name="userfile[]"; filename="aaa副本.txt"\r\n
Content-Type: application/octet-stream\r\n\r\n

二进制数据
\r\n
--boundary\r\n
// status 是脚本文件接收参数的名称
Content-Disposition: form-data; name="status"\r\n\r\n

二进制数据
\r\n
--boundary--
```

## 代码实现

### 准备数据确认参数

```objc
- (void)touchesBegan:(NSSet *)touches withEvent:(UIEvent *)event {

    // 1. 上传文件数据
    NSURL *fileURL1 = [[NSBundle mainBundle] URLForResource:@"001.png" withExtension:nil];
    NSData *data1 = [NSData dataWithContentsOfURL:fileURL1];

    NSURL *fileURL2 = [[NSBundle mainBundle] URLForResource:@"demo.jpg" withExtension:nil];
    NSData *data2 = [NSData dataWithContentsOfURL:fileURL2];

    // 如何传递参数 － 用字典传递参数
    NSDictionary *dataDict = @{@"001.png": data1, @"002.jpg": data2};

    // 2. 字符串参数
    NSDictionary *params = @{@"status": @"how are you"};

    // 3. 上传文件并且提交参数
    [self uploadFile:@"userfile[]" dataDict:dataDict params:params];
}
```

### 上传文件

```objc
#define boundary @"itcast-upload"

///  上传文件
///
///  @param fieldName 服务器字段名
///  @param dataDict  上传文件数据字典 "保存在服务器文件名": 二进制数据
///  @param params    提交参数字典
- (void)uploadFile:(NSString *)fieldName dataDict:(NSDictionary *)dataDict params:(NSDictionary *)params {

    // 1. url
    NSURL *url = [NSURL URLWithString:@"http://10.0.1.8/post/upload-m.php"];

    // 2. request
    NSMutableURLRequest *request = [NSMutableURLRequest requestWithURL:url];
    request.HTTPMethod = @"POST";

    NSString *typeValue = [NSString stringWithFormat:@"multipart/form-data; boundary=%@", boundary];
    [request setValue:typeValue forHTTPHeaderField:@"Content-Type"];

    request.HTTPBody = [self formData:fieldName dataDict:dataDict params:params];

    [NSURLConnection sendAsynchronousRequest:request queue:[NSOperationQueue mainQueue] completionHandler:^(NSURLResponse *response, NSData *data, NSError *connectionError) {

        NSLog(@"%@", [NSJSONSerialization JSONObjectWithData:data options:0 error:NULL]);
    }];
}
```

### 生成 formData 数据

```objc
///  生成 formData 二进制数据
///
///  @param fieldName 服务器字段名
///  @param dataDict  上传文件数据字典 "保存在服务器文件名": 二进制数据
///  @param params    提交参数字典
///
///  @return formData 二进制数据
- (NSData *)formData:(NSString *)fieldName dataDict:(NSDictionary *)dataDict params:(NSDictionary *)params {

    NSMutableData *dataM = [NSMutableData data];

    // 1. 生成文件数据
    [dataDict enumerateKeysAndObjectsUsingBlock:^(NSString *fileName, NSData *fileData, BOOL *stop) {
        NSMutableString *strM = [NSMutableString string];

        [strM appendFormat:@"--%@\r\n", boundary];
        [strM appendFormat:@"Content-Disposition: form-data; name=\"%@\"; filename=\"%@\"\r\n", fieldName, fileName];
        [strM appendString:@"Content-Type: application/octet-stream\r\n\r\n"];

        [dataM appendData:[strM dataUsingEncoding:NSUTF8StringEncoding]];
        [dataM appendData:fileData];

        [dataM appendData:[@"\r\n" dataUsingEncoding:NSUTF8StringEncoding]];
    }];

    // 2. 生成参数数据
    [params enumerateKeysAndObjectsUsingBlock:^(NSString *key, NSString *value, BOOL *stop) {

        NSMutableString *strM = [NSMutableString string];

        [strM appendFormat:@"--%@\r\n", boundary];
        [strM appendFormat:@"Content-Disposition: form-data; name=\"%@\"\r\n\r\n", key];
        [strM appendString:value];
        [strM appendString:@"\r\n"];

        [dataM appendData:[strM dataUsingEncoding:NSUTF8StringEncoding]];
    }];

    return dataM.copy;
}
```
