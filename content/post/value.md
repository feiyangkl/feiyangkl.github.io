---
title: "iOS类与类之间的三种传值方式"

date: "2016-01-11 10:29:32"

tags: 
    - iOS
---




##一代理方式
### 什么是代理模式

传入的对象,代替当前类完成了某一个功能,称为代理模式.	

实现代理有以下方法
    
```
在要传值的类中

1> 声明代理方法

2> 定义代理属性

3> 在适当的时候调用代理方法

在要接受值的类中

1> 设置代理对象

2> 遵守代理协议

3> 实现代理方法

```

<!-- more --> 
图片如下:
![image](https://imgconvert.csdnimg.cn/aHR0cDovL3VwbG9hZC1pbWFnZXMuamlhbnNodS5pby91cGxvYWRfaW1hZ2VzLzE4OTk5MzQtMjBlZGFjMmJlODc3M2MzZi5wbmc?x-oss-process=image/format,png)
代码如下

```
B.H类  类名  name
///声明代理方法
@protocol  nameDelegate <NSObject>

- (void)name:(B *)VC ;

@end

///定义代理属性
@property (nonatomic, weak) id<nameDelegate> delegate;

B.M
///在合适的时候调用代理方法
 if ([self.delegate respondsToSelector:@selector(a:)]) {
        [self.delegate a:self];
    }
    
A.M
///设置代理对象,获取B
B *b = [[B alloc]init];

b.delegate = self;

///遵守代理协议
@interface contactController ()<nameDelegate>

///实现代理方法
- (void)name:(B *)VC { }
```

##BLOCK传值
###什么是BLOCK

苹果推荐的类型，效率高，在运行中保存代码。用来封装和保存代码，有点像函数，BLOCK可以在任何时候执行。

BOLCK和函数的相似性：
	
（1）可以保存代码（2）有返回值（3）有形参（4）调用方式一样

```
///block如何写
block数据类型
定义格式:
返回值类型（^block变量名）(形参列表)＝^(形参列表){
}

调用block
block变量名（实参）;
默认情况下，Block内部不能修改外面局部变量
Block内部可以修改使用__block修饰的局部变量

使用typedef定义Block类型
typedef 返回值类型(^block类型名称)(形参列表)

```
图片如下

![image](https://imgconvert.csdnimg.cn/aHR0cDovL3VwbG9hZC1pbWFnZXMuamlhbnNodS5pby91cGxvYWRfaW1hZ2VzLzE4OTk5MzQtZjhmNGQxY2E2ZTVmMmQ1Yi5wbmc?x-oss-process=image/format,png)
代码如下

```
B.H
///声明一个block属性
typedef void(^MyBlock) (NSString *);

///定义一个block属性
@property (copy ,nonatomic)MyBlock Block;

@property (copy, nonatomic) NSString *name;

B.M
///在适当的时候调用Block
    if (self.MyBlock) {
        self.MyBlock(name);
    }
A.M
///保存一个代码块
/ / /获取B
 B.MyBlock = ^(NSString *name){
             ///值就有了
        } ;
```

##通知传值
  一个完整的通知一般包含3个属性：
 
```
     - (NSString*)name; // 通知的名称
     - (id)object; // 通知发布者(是谁要发布通知)
     - (NSDictionary*)userInfo; // 一些额外的信息(通知发布者传递给通知接收者的信息内容)
  
```

 图片如下
 ![image](https://imgconvert.csdnimg.cn/aHR0cDovL3VwbG9hZC1pbWFnZXMuamlhbnNodS5pby91cGxvYWRfaW1hZ2VzLzE4OTk5MzQtNWViNDRhZTc5YTg4YmE3OC5wbmc?x-oss-process=image/format,png)
 
 
 代码如下
 
```
   ///获取通知中心,点击按钮发送通知
    [[NSNotificationCenter defaultCenter] postNotificationName:@"noti" object:nil userInfo:@{@"key":name}];
    
     ///获取通知中心,接收通知,调用方法
    [[NSNotificationCenter defaultCenter] addObserver:self selector:@selector(text:) name:@"noti" object:nil];
    
///实现通知方法
- (void)text:(NSNotification *)noti {
///noti中的key,就可以取出值
     NSString * name =  noti.userInfo[@"key"];

///在调用结束的时候,要移除通知
 - (void)dealloc {
          //[super dealloc];  非ARC中需要调用此句
          
          [[NSNotificationCenter defaultCenter] removeObserver:self];
   }
 
}
```
 