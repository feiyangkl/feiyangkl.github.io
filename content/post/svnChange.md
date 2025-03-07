---
title: "SVN服务器地址更换"

date: "2018-12-25 10:29:32"

tags: 
    - iOS
---

###更新cornerStone
打开cornerStone,点击更改URL,验证后可用

### 更改.SVN中的地址
```
cd 你的项目所在目录/

#查看一下，当前的svn地址
svn info ./

svn cleanup

# 更换服务器地址, 移除旧的地址,更新新的地址
svn sw --relocate oldsvn 地址 newsvn 地址 ./

#这里需要输入本机密码

#这里需要输入svn用户名和密码
```
原文地址：http://blog.sina.com.cn/s/blog_71715bf80101ar57.html













