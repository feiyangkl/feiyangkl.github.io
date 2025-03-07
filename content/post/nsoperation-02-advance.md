
---
title: "高级演练"

date: "2016-07-16 10:29:32"

tags: 
    - iOS
---
## 全局队列

```objc
/// 全局操作队列，统一管理所有的异步操作
@property (nonatomic, strong) NSOperationQueue *queue;

- (NSOperationQueue *)queue {
    if (_queue == nil) {
        _queue = [[NSOperationQueue alloc] init];
    }
    return _queue;
}
```
<!-- more --> 
## 最大并发操作数

```objc
/// MARK: - 最大并发操作数
- (void)opDemo1 {

    // 设置同时并发操作数
    self.queue.maxConcurrentOperationCount = 2;

    NSLog(@"start");

    for (int i = 0; i < 10; ++i) {
        NSBlockOperation *op = [NSBlockOperation blockOperationWithBlock:^{
            [NSThread sleepForTimeInterval:1.0];
            NSLog(@"%@ %d", [NSThread currentThread], i);
        }];

        [self.queue addOperation:op];
    }
}
```

## 暂停 & 继续

```objc
/// MARK: - 暂停 & 继续
- (IBAction)pauseAndResume {

    if (self.queue.operationCount == 0) {
        NSLog(@"没有操作");
        return;
    }

    // 暂停或者继续
    self.queue.suspended = !self.queue.isSuspended;

    if (self.queue.isSuspended) {
        NSLog(@"暂停 %tu", self.queue.operationCount);
    } else {
        NSLog(@"继续 %tu", self.queue.operationCount);
    }
}
```

* 队列挂起，当前"没有完成的操作"，是包含在队列的操作数中的
* 队列挂起，不会影响已经执行操作的执行状态
* 对列一旦被挂起，再添加的操作不会被调度

## 取消全部操作

```objc
/// MARK: - 取消所有操作
- (IBAction)cancelAll {
    if (self.queue.operationCount == 0) {
        NSLog(@"没有操作");
        return;
    }

    // 取消对列中的所有操作，同样不会影响到正在执行中的操作！
    [self.queue cancelAllOperations];

    NSLog(@"取消全部操作 %tu", self.queue.operationCount);
}
```

* 取消队列中所有的操作
* 不会取消正在执行中的操作
* 不会影响队列的挂起状态

## 依赖关系

```objc
/// MARK: - 依赖关系
- (void)dependency {

    NSBlockOperation *op1 = [NSBlockOperation blockOperationWithBlock:^{
        NSLog(@"登录 %@", [NSThread currentThread]);
    }];
    NSBlockOperation *op2 = [NSBlockOperation blockOperationWithBlock:^{
        NSLog(@"付费 %@", [NSThread currentThread]);
    }];
    NSBlockOperation *op3 = [NSBlockOperation blockOperationWithBlock:^{
        NSLog(@"下载 %@", [NSThread currentThread]);
    }];
    NSBlockOperation *op4 = [NSBlockOperation blockOperationWithBlock:^{
        NSLog(@"通知用户 %@", [NSThread currentThread]);
    }];

    [op2 addDependency:op1];
    [op3 addDependency:op2];
    [op4 addDependency:op3];
    // 注意不要循环依赖
//    [op1 addDependency:op4];

    [self.queue addOperations:@[op1, op2, op3] waitUntilFinished:NO];
    [[NSOperationQueue mainQueue] addOperation:op4];

    NSLog(@"come here");
}
```
