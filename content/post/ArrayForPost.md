
---
title: "iOS网络请求传入数组"
date: "2018-12-25 10:29:32"

tags: 
    - iOS
---
之前做项目的时候，后台要求传入数组，用AFN传入字典，代码入下：
```object-c


          NSDictionary *parameter = [ NSDictionary dictionary];
            
          NSArray *arr = @[parameter];
            
          NSError *parseError = nil;

          NSData *jsonData = [NSJSONSerialization dataWithJSONObject:arr options:NSJSONWritingPrettyPrinted  error:&parseError];
            
          NSString *jsonstr =[[NSString alloc] initWithData:jsonData encoding:NSUTF8StringEncoding];
            
          NSData *objectData = [jsonstr dataUsingEncoding:NSUTF8StringEncoding];
            
          NSDictionary *jsonDic = [NSJSONSerialization JSONObjectWithData:objectData
                                     
                                                                    options:NSJSONReadingMutableContainers
                                     
                                                                      error:&parseError];
```