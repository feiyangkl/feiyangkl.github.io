---
title: "SDWebImage获取缓存和清除"

date: "2018-12-22 10:29:32"

tags: 
    - iOS
---

##获取缓存大小
```
/// 获取缓存大小
 NSString * currentCacheSize = [NSString stringWithFormat:@"%@",[self fileSizeWithInteger:[[SDImageCache sharedImageCache] getSize]]];

// 根据数据计算出大小
- (NSString *) fileSizeWithInteger:(NSInteger)size{
    // 1K = 1024dB, 1M = 1024K,1G = 1024M
    if (size < 1024) {// 小于1k
        return [NSString stringWithFormat:@"%ldB",(long)size];
    }else if (size < 1024 * 1024){// 小于1m
        CGFloat aFloat = size/1024;
        return [NSString stringWithFormat:@"%.0fK",aFloat];
    }else if (size < 1024 * 1024 * 1024){// 小于1G
        CGFloat aFloat = size/(1024 * 1024);
        return [NSString stringWithFormat:@"%.1fM",aFloat];
    }else{
        CGFloat aFloat = size/(1024*1024*1024);
        return [NSString stringWithFormat:@"%.1fG",aFloat];
    }
}

```

## 清除SDWebImage缓存

```
/// 清除硬盘缓存-当前使用的也会清除
[[SDImageCache sharedImageCache] clearDisk];

/// 清除过期缓存,缓存时间为7天 计算当前缓存的大小和设置的最大缓存数比较,如果超出,就继续清除(___按照文件创建的先后顺序__)
[[SDImageCache sharedImageCache] cleanDisk];

///  清除内存缓存
[[[SDWebImageManager sharedManager] imageCache] clearMemory];  
```

