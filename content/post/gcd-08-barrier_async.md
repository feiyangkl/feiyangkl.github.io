
---
title: "Barrier 异步"
date: "2016-09-22 10:29:32"

tags: 
    - iOS
---
* 主要用于在多个异步操作完成之后，统一对`非线程安全`的对象进行更新
* 适合于`大规模的 I/O` 操作

<!-- more -->

## 代码演练

* 准备工作

```objc
@interface ViewController () {
    // 加载照片队列
    dispatch_queue_t _photoQueue;
}

@property (nonatomic, strong) NSMutableArray *photoList;
@end

- (NSMutableArray *)photoList {
    if (_photoList == nil) {
        _photoList = [[NSMutableArray alloc] init];
    }
    return _photoList;
}
```

> `NSMutableArray` 是非线程安全的

* viewDidLoad

```objc
- (void)viewDidLoad {
    [super viewDidLoad];

    _photoQueue = dispatch_queue_create("com.itheima.com", DISPATCH_QUEUE_CONCURRENT);

    for (int i = 0; i < 20; ++i) {
        [self loadPhotos:i];
    }
}
```

* 模拟下载照片并在完成后添加到数组

```objc
- (void)loadPhotos:(int)index {

    dispatch_async(_photoQueue, ^{
        [NSThread sleepForTimeInterval:1.0];

        NSString *fileName = [NSString stringWithFormat:@"%02d.jpg", index % 10 + 1];
        NSString *path = [[NSBundle mainBundle] pathForResource:fileName ofType:nil];
        UIImage *image = [UIImage imageWithContentsOfFile:path];

        [self.photoList addObject:image];
        NSLog(@"添加照片 %@", fileName);
    });
}
```

> 运行测试

* 由于 `NSMutableArray` 是非线程安全的，如果出现两个线程在同一时间向数组中添加对象，会出现程序崩溃的情况

* 解决办法

```objc
NSLog(@"添加照片 %@", fileName);
dispatch_barrier_async(_photoQueue, ^{
    [self.photoList addObject:image];
    NSLog(@"OK %@", [NSThread currentThread]);

});
```

> 使用 `dispatch_barrier_async` 添加的 block 会在之前添加的 block 全部运行结束之后，才在同一个线程顺序执行，从而保证对非线程安全的对象进行正确的操作！

## Barrier 工作示意图

![](./images/gcd/Dispatch-Barrier.png)

> 注意：`dispatch_barrier_async` 必须使用自定义队列，否则执行效果和全局队列一致


