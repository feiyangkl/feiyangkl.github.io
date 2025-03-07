
---
title: "SDWebImage常见面试题"

date: "2016-08-07 10:29:32"

tags: 
    - iOS
---
1> 图片文件缓存的时间有多长：`1周`

`_maxCacheAge ＝ kDefaultCacheMaxCacheAge`

2> SDWebImage 的内存缓存是用什么实现的？

`NSCache`

3> SDWebImage 的最大并发数是多少？

`maxConcurrentDownloads ＝ 6`
* 是程序固定死了，可以通过属性进行调整！

<!-- more -->

4> SDWebImage 支持动图吗？GIF

```objc
#import <ImageIO/ImageIO.h>
[UIImage animatedImageWithImages:images duration:duration];
```

5> SDWebImage是如何区分不同格式的图像的

* 根据图像数据第一个字节来判断的！

    * PNG：压缩比没有JPG高，但是无损压缩，解压缩性能高，苹果推荐的图像格式！
    * JPG：压缩比最高的一种图片格式，有损压缩！最多使用的场景，照相机！解压缩的性能不好！
	* GIF：序列桢动图，特点：只支持256种颜色！最流行的时候在1998～1999，有专利的！

6> SDWebImage 缓存图片的名称是怎么确定的！

* `md5`

    * 如果单纯使用 文件名保存，重名的几率很高！
	* 使用 MD5 的散列函数！对完整的 URL 进行 md5，结果是一个 32 个字符长度的字符串！

7> SDWebImage 的内存警告是如何处理的！

* 利用通知中心观察
* `- UIApplicationDidReceiveMemoryWarningNotification` 接收到内存警告的通知
    * 执行 `clearMemory` 方法，清理内存缓存！
* `- UIApplicationWillTerminateNotification` 接收到应用程序将要终止通知
    * 执行 `cleanDisk` 方法，清理磁盘缓存！
* `- UIApplicationDidEnterBackgroundNotification` 接收到应用程序进入后台通知
    * 执行 `backgroundCleanDisk` 方法，后台清理磁盘！
    * 通过以上通知监听，能够保证缓存文件的大小始终在控制范围之内！
    * `clearDisk` 清空磁盘缓存，将所有缓存目录中的文件，全部删除！
实际工作，将缓存目录直接删除，再次创建一个同名空目录！
