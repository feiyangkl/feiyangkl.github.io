
---
title: "GCD 常用代码"

date: "2016-09-23 10:29:32"

tags: 
    - iOS
---
## 体验代码

### 异步执行任务

```objc
- (void)gcdDemo1 {
    // 1. 全局队列
    dispatch_queue_t q = dispatch_get_global_queue(0, 0);

    // 2. 任务
    void (^task)() = ^ {
        NSLog(@"%@", [NSThread currentThread]);
    };

    // 3. 指定执行任务的函数
    // 异步执行任务 - 新建线程，在新线程执行 task
    dispatch_async(q, task);

    NSLog(@"come here");
}
```

> 注意：如果等待时间长一些，会发现线程的 `number` 发生变化，由此可以推断 `gcd 底层线程池`的工作

<!-- more -->

### 同步执行任务

```objc
- (void)gcdDemo1 {
    // 1. 全局队列
    dispatch_queue_t q = dispatch_get_global_queue(0, 0);

    // 2. 任务
    void (^task)() = ^ {
        NSLog(@"%@", [NSThread currentThread]);
    };

    // 3. 指定执行任务的函数
    // 同步执行任务 - 不开启线程，在当前线程执行 task
    dispatch_sync(q, task);

    NSLog(@"come here");
}
```

## 精简代码

```objc
- (void)gcdDemo2 {
    for (int i = 0; i < 10; ++i) {
        dispatch_async(dispatch_get_global_queue(0, 0), ^{
            NSLog(@"%@ %@", [NSThread currentThread], @"hello");
        });
    }
}
```

与 `NSThread` 的对比

1. 所有的代码写在一起的，让代码更加简单，易于阅读和维护
    * `NSThread` 通过 `@selector` 指定要执行的方法，代码分散
    * `GCD` 通过 `block` 指定要执行的代码，代码集中
2. 使用 `GCD` 不需要管理线程的创建/销毁/复用的过程！程序员不用关心线程的生命周期
3. 如果要开多个线程 `NSThread` 必须实例化多个线程对象
4. `NSThread` 靠 `NSObject` 的分类方法实现的线程间通讯，`GCD` 靠 `block`

## 线程间通讯

```objc
- (void)gcdDemo3 {
    dispatch_async(dispatch_get_global_queue(0, 0), ^{
        NSLog(@"耗时操作 %@", [NSThread currentThread]);

        // 耗时操作之后，更新UI
        dispatch_async(dispatch_get_main_queue(), ^{
            NSLog(@"更新 UI %@", [NSThread currentThread]);
        });
    });
}
```

> 以上代码是 `GCD` 最常用代码组合！

* 如果要在更新 UI 之后，继续做些事情，可以使用以下代码

```objc
- (void)gcdDemo4 {
    dispatch_async(dispatch_get_global_queue(0, 0), ^{
        NSLog(@"耗时操作");

        dispatch_sync(dispatch_get_main_queue(), ^{
            NSLog(@"更新UI");
        });

        NSLog(@"更新UI完毕");
    });
}
```

### 网络下载图片

```objc
- (void)viewDidLoad {
    [super viewDidLoad];

    dispatch_async(dispatch_get_global_queue(0, 0), ^{
        NSLog(@"%s %@", __FUNCTION__, [NSThread currentThread]);

        // 1. 异步下载网络图片
        NSURL *url = [NSURL URLWithString:@"http://f.hiphotos.baidu.com/image/pic/item/1f178a82b9014a901bef674aaa773912b21bee70.jpg"];
        NSData *data = [NSData dataWithContentsOfURL:url];
        UIImage *image = [UIImage imageWithData:data];

        // 2. 完成后更新 UI
        dispatch_async(dispatch_get_main_queue(), ^{
            self.imageView.image = image;
            [self.imageView sizeToFit];

            self.scrollView.contentSize = image.size;
        });
    });
}
```

