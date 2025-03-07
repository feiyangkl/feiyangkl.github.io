---
title: "Swift 数组中 Map,FlatMap,Filter,Reduce的使用"

date: "2018-10-22 10:29:32"

tags: 
    - iOS
---



本文章转载自 [Melody_Zhy](http://www.cocoachina.com/swift/20160210/15068.html) 
##Map
> map函数能够被数组调用，它接受一个闭包作为参数，作用于数组中的每个元素。闭包返回一个变换后的元素，接着将所有这些变换后的元素组成一个新的数组

###1.比如我们有一个这样的需求遍历一个数组中所有的元素，将每个元素自身与自身相加，最后返回一个保存相加后元素的数组(-_-原谅我这表达能力,下面用代码阐述)	
如果我们不使用map函数,那么代码如下
```Swift
let numbers = [1,2,3]
var sumNumbers = [Int]()
for var number in numbers {
	number += number
	sumNumbers.append(number)
	}
// [2,4,6]
print(sumNumbers)	
```
可以看到上面的代码正是我们经常用到的代码，但通过数组的map函数可以帮我们简化上面的代码
<!-- more --> 
```Swift
// 可以看到我们甚至可以不再定义可变的数组直接用不可变的就可以
let numbers = [1,2,3]
let sumNumbers = numbers.map { (number: Int) -> Int in
    return number + number
}
// 下面介绍简便写法 因为map闭包里面的类型可以自动推断所以可以省略
let sumNumbers1 = numbers.map { number in
    return number + number
}
// 可以省了return 但是循环次数多了一次 目前不知道这是什么原因(循环次数是3次这是4次) 结果是一样的 
let sumNumbers2 = numbers.map { number in number + number }
print(sumNumbers2) // [2,4,6]
// 最终简化写法
let sumNumbers3 = numbers.map { $0 + $0 }
```

###2. Map函数返回数组的元素类型不一定要与原数组相同

```
let fruits = ["apple", "banana", "orange", ""]
// 这里数组中存在一个""的字符串 为了后面来比较 map 和 flatMap
let counts = fruits.map { fruit -> Int? in
    let length = fruit.characters.count
    guard length > 0 else {
        return nil
    }
    return length
}
// [Optional(5), Optional(6), Optional(6), nil]
print(counts)
```

###3. Map还能返回判断数组中的元素是否满足某种条件的Bool值数组
```
let array = [1,2,3,4,5,6]
// 最洁简的写法
let isEven = array.map { $0 % 2 == 0 }
// 这里在写下完成的写法 下面的例子 将都采用最洁简的写法^_^ 同时也要养成习惯看见上面那种洁简的写法 就要懂它做了些什么 会有什么样的结果
let isEven1 = array.map { num in
    // 写上retrun在Playground中的循环次数是6次 不写是7次 Xcode版本是7.2(7C68) 
    // 不知道这是不是bug 有知道的提醒下我～
    return num % 2 == 0
}
// [false, true, false, true, false, true]
print(isEven)
```

##FlatMap
>flatMap 与 map 不同之处是flatMap返回后的数组中不存在 nil 同时它会把Optional解包;flatMap 还能把数组中存有数组的数组 一同打开变成一个新的数组 ;flatMap也能把两个不同的数组合并成一个数组 这个合并的数组元素个数是前面两个数组元素个数的乘积

###1. flatMap返回后的数组中不存在nil 同时它会把Optional解包

```
let fruits = ["apple", "banana", "orange", ""]
let counts = fruits.flatMap { fruit -> Int? in
    let length = fruit.characters.count
    guard length > 0 else {
        return nil
    }
    return length
}
// [5,6,6]
print(counts)
```

###2. flatMap 还能把数组中存有数组的数组 一同打开变成一个新的数组(看代码秒懂~_~)

```
let array = [[1,2,3], [4,5,6], [7,8,9]]
// 如果用map来获取新的数组
let arrayMap = array.map { $0 }
// [[1, 2, 3], [4, 5, 6], [7, 8, 9]]
print(arrayMap)
// 用flatMap
let arrayFlatMap = array.flatMap { $0 }
// [1, 2, 3, 4, 5, 6, 7, 8, 9]
print(arrayFlatMap)
```

###3. flatMap也能把两个不同的数组合并成一个数组 这个合并的数组元素个数是前面两个数组元素个数的乘积

```
// 这种情况是把两个不同的数组合并成一个数组 这个合并的数组元素个数是前面两个数组元素个数的乘积
let fruits = ["apple", "banana", "orange"]
let counts = [1, 2, 3]
let fruitCounts = counts.flatMap { count in
    fruits.map { fruit in
//        title + "(index)"
        // 也可以返回元组
        (fruit, count)
    }
}
// [("apple", 1), ("banana", 1), ("orange", 1), ("apple", 2), ("banana", 2), ("orange", 2), ("apple", 3), ("banana", 3), ("orange", 3)]
print(fruitCounts)
// 这种方法估计用的很少 可以算是一个 flatMap 和 map 的结合使用吧
```

##Filter
>filter 可以取出数组中符合条件的元素 重新组成一个新的数组

###filter可以很好的帮我们把数组中不需要的值都去掉 这个很赞！

```
let numbers = [1,2,3,4,5,6]
let evens = numbers.filter { $0 % 2 == 0 }
// [2, 4, 6]
print(evens)
```

##Reduce
>map,flatMap和filter方法都是通过一个已存在的数组，生成一个新的、经过修改的数组。然而有时候我们需要把所有元素的值合并成一个新的值 那么就用到了Reduce

###1. 比如我们要获得一个数组中所有元素的和

```
let numbers = [1,2,3,4,5]
// reduce 函数第一个参数是返回值的初始化值
let sum = numbers.reduce(0) { $0 + $1 }
// 这里我写下完整的格式
let sum1 = numbers.reduce(0) { total, num in
    // 这里写不写return在Playground都循环5次 但上面用最洁简的方法显示循环6次。。。 What The Fuck 这是什么鬼！！！
    return total + num
}
// 15
print(sum)
```

###2. 合并成的新值不一定跟原数组中元素的类型相同

```
let numbers = [1,5,1,8,8,8,8,8,8,8,8]
// reduce 函数第一个参数是返回值的初始化值
let tel = numbers.reduce("") { "($0)" + "($1)" }
// 15188888888
print(tel)
```

###3. ruduce 还可以实现 map 和 filter 并且时间复杂度变为O(n) 原来 map 和 filter 的时间复杂度是O(n*n)

```

extension Array {
    func mMap (transform: Element -> U) -> [U] {
        return reduce([], combine: { $0 + [transform($1)] })
    }
    func mFilter (includeElement: Element -> Bool) -> [Element] {
        return reduce([]) { includeElement($1) ? $0 + [$1] : $0 }
    }
}
```