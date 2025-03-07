
---
title: "POST上传单个文件"

date: "2016-05-01 10:29:32"

tags: 
    - iOS
---

## 上传原理

* 用 firebug 演示 `upload.php`
* 注意修改 `abc` 文件夹的访问权限

## 上传单个文件

* 请求格式

```
Content-Type: multipart/form-data; boundary(分隔线)=(可以随便写，ASCII，字母和数字)
```
<!-- more --> 
* 数据格式

```
--boundary\r\n
Content-Disposition: form-data; name="userfile"; filename="aaa.txt"\r\n
Content-Type: application/octet-stream\r\n\r\n

要上传文件的二进制数据

\r\n--boundary--
```

* 说明

    * `userfile`：负责上传文件脚本中的 字段名，开发的时候，可以咨询后端程序员
    * `filename`：将文件保存在服务器上的文件名称
    * `Content-Type`：客户端告诉服务器上传文件的文件类型
        - `text/plain`
        - `image/jpg`
        - `image/png`
        - `image/gif`
        - `text/html`
        - `application/json`
        - `application/octet-stream`(8进制流)，如果不想告诉服务器具体的文件类型，可以使用这个 Content-Type

    * 注意：每一行末尾需要有一定的 \r\n
    * 提示：有些服务器可以直接使用 \n，但是新浪微博如果使用 \n 上传文件，服务器会返回“没有权限”的错误！

## 代码实现

### 生成 formData 二进制数据

```objc
#define boundary    @"itheima-upload"

///  生成 formData 二进制数据
///
///  @param fieldName 服务器字段名
///  @param fileName  文件名
///  @param fileData  上传文件二进制数据
///
///  @return formData 二进制数据
- (NSData *)formData:(NSString *)fieldName fileName:(NSString *)fileName fileData:(NSData *)fileData {

    NSMutableData *dataM = [NSMutableData data];

    // 拼接数据
    NSMutableString *strM = [NSMutableString string];

    [strM appendFormat:@"--%@\r\n", boundary];
    [strM appendFormat:@"Content-Disposition: form-data; name=\"%@\"; filename=\"%@\"\r\n", fieldName, fileName];
    [strM appendString:@"Content-Type: application/octet-stream\r\n\r\n"];

    [dataM appendData:[strM dataUsingEncoding:NSUTF8StringEncoding]];
    [dataM appendData:fileData];

    NSString *tail = [NSString stringWithFormat:@"\r\n--%@--", boundary];
    [dataM appendData:[tail dataUsingEncoding:NSUTF8StringEncoding]];

    return dataM.copy;
}
```

### 上传单个文件

```objc
///  上传单个文件
///
///  @param fieldName 服务器自短命
///  @param fileName  文件名
///  @param fileData  上传文件二进制数据
- (void)uploadFile:(NSString *)fieldName fileName:(NSString *)fileName fileData:(NSData *)fileData {
    // 1. url - 负责上传的脚本
    NSURL *url = [NSURL URLWithString:@"http://localhost/post/upload.php"];

    // 2. request
    NSMutableURLRequest *request = [NSMutableURLRequest requestWithURL:url];
    request.HTTPMethod = @"POST";

    NSString *typeValue = [NSString stringWithFormat:@"multipart/form-data; boundary=%@", boundary];
    [request setValue:typeValue forHTTPHeaderField:@"Content-Type"];

    request.HTTPBody = [self formData:fieldName fileName:fileName fileData:fileData];

    // 3. connection
    [NSURLConnection sendAsynchronousRequest:request queue:[NSOperationQueue mainQueue] completionHandler:^(NSURLResponse *response, NSData *data, NSError *connectionError) {

        NSLog(@"%@", [NSJSONSerialization JSONObjectWithData:data options:0 error:NULL]);
    }];
}
```

### 测试代码

```objc
- (void)touchesBegan:(NSSet *)touches withEvent:(UIEvent *)event {

    NSURL *fileURL = [[NSBundle mainBundle] URLForResource:@"001.png" withExtension:nil];
    NSData *data = [NSData dataWithContentsOfURL:fileURL];

    [self uploadFile:@"userfile" fileName:@"abc" fileData:data];
}
```
