
---
title: "block"

date: "2016-09-24 10:29:32"

tags: 
    - iOS
---
## 概念

* block 是 C 语言的
* 是一种数据类型，可以当作参数传递
* 是一组预先准备好的代码，在需要的时候执行

<!-- more -->

### 动画 block 回顾

```objc
self.demoView.center = CGPointMake(self.view.center.x, 0);
// 此方法会立即执行动画 block
[UIView animateWithDuration:2.0 delay:0 usingSpringWithDamping:0.3 initialSpringVelocity:10 options:0 animations:^{
    NSLog(@"动画开始");
    self.demoView.center = self.view.center;
} completion:^(BOOL finished) {
    // 会在动画结束后执行
    NSLog(@"动画完成");
}];
NSLog(@"come here");
```

## block 基本演练

* 最简单的 `block`

```objc
- (void)blockDemo1 {

    // 定义block
    // 类型 变量名 = 值
    void (^block)() = ^ {
        NSLog(@"Hello block");
    };

    // 执行
    block();
}
```

> 使用 `inlineBlock` 可以快速定义 `block`，不过 `block` 一定要过关

* 当作参数传递

```objc
- (void)blockDemo2 {
    void (^block)() = ^ {
        NSLog(@"Hello block");
    };

    [self demoBlock:block];
}

///  演示 block 当作参数传递
- (void)demoBlock:(void (^)())completion {
    NSLog(@"干点什么");

    completion();
}
```

* 使用局部变量

```objc
- (void)blockDemo3 {
    // 栈区变量
    int i = 10;
    NSLog(@"%p", &i);

    void (^block)() = ^ {
        // 定义 block 的时候会对栈区变量进行一次 copy
        NSLog(@"Hello block %d %p", i, &i);
    };

    [self demoBlock:block];
}
```

> 如果 block 中使用了外部变量，会对外部变量做一次 `copy`

* 在 block 中修改外部变量

```objc
- (void)blockDemo4 {
    // 栈区变量
    __block int i = 10;
    NSLog(@"%p", &i);

    void (^block)() = ^ {
        // 定义 block 的时候会对栈区变量进行一次 copy
        NSLog(@"Hello block %d %p", i, &i);
        i = 20;
    };

    NSLog(@"block 定义完成 %p %d", &i, i);

    [self demoBlock:block];

    NSLog(@"===>%d", i);
}
```

> 如果要在 block 内部修改栈区变量，需要使用 `__block` 修饰符，并且定义 block 之后，栈区变量的地址会变化为堆区地址

### block 的内存位置

* 全局区：如果block中没有使用任何全局变量
* 栈区：如果 block 中使用了外部变量
    * MRC 模式可以看到
    * ARC 模式，系统会自动将 Block 复制到堆中
* 堆区：将 block 设置给 copy 属性

```objc
@property (nonatomic, copy) void (^myBlock)();
```

```objc
- (void)blockDemo5 {
    int i = 10;
    void (^block)() = ^ {
        NSLog(@"i --- %d", i);
    };

    NSLog(@"%@", block);

    self.myBlock = block;
}

- (void)touchesBegan:(NSSet *)touches withEvent:(UIEvent *)event {

    NSLog(@"%@", self.myBlock);
}
```

> 注意：虽然目前 ARC 编译器在设置属性时，已经替程序员复制了 block，但是定义 `block`时，仍然建议使用 `copy` 属性








