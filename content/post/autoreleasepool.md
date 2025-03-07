
---
title: "自动释放池"

date: "2018-12-25 10:29:32"

tags: 
    - iOS
---
* 作用

    * 自动释放对象的
    * 所有 `autorelease` 的对象，在出了作用域之后，会被自动添加到`最近创建的`自动释放池中
    * 自动释放池被销毁或者耗尽时，会向池中所有对象发送 `release` 消息，释放池中对象
    * 自动释放池，在 `ARC` & `MRC` 程序中，同样有效

## 高级面试题：

- 自动释放池是什么时候创建的？什么时候销毁的？

* 创建，运行循环检测到事件并启动后，就会创建自动释放池
* 销毁：一次完整的运行循环结束之前，会被销毁

![](https://s2.ax1x.com/2019/08/22/maw6Cn.png)
<!-- more --> 



* 常见面试题

```objc
for (long i = 0; i < largeNumber; ++i) {
    NSString *str = [NSString stringWithFormat:@"hello - %ld", i];
    str = [str uppercaseString];
    str = [str stringByAppendingString:@" - world"];
}
```

* 提问：以上代码是否有问题？如果有，如何解决？

* 解决方法：引入自动释放池
    * 1> 外面加自动释放池（快）：能够保证for循环结束后，内部产生的自动释放对象，都会被销毁
需要等到 for 结束后，才会释放内存
    * 2> 里面加自动释放池（慢）：能够每一次 for 都释放产生的自动释放对象！

```objc
- (void)touchesBegan:(NSSet *)touches withEvent:(UIEvent *)event {

    NSLog(@"start");
    CFAbsoluteTime start = CFAbsoluteTimeGetCurrent();
    [self answer1];
    NSLog(@"外 %f", CFAbsoluteTimeGetCurrent() - start);

    start = CFAbsoluteTimeGetCurrent();
    [self answer2];
    NSLog(@"内 %f", CFAbsoluteTimeGetCurrent() - start);
}

- (void)answer1 {
    @autoreleasepool {
        for (long i = 0; i < largeNumber; ++i) {
            NSString *str = [NSString stringWithFormat:@"hello - %ld", i];
            str = [str uppercaseString];
            str = [str stringByAppendingString:@" - world"];
        }
    }
}

- (void)answer2 {
    for (long i = 0; i < largeNumber; ++i) {
        @autoreleasepool {
            NSString *str = [NSString stringWithFormat:@"hello - %ld", i];
            str = [str uppercaseString];
            str = [str stringByAppendingString:@" - world"];
        }
    }
}
```

* 实际测试结果，是运行循环放在内部的速度更快！
* 日常开发中，如果遇到局部代码内存峰值很高，可以引入运行循环及时释放延迟释放对象
