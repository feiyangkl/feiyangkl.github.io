---
title: "制作 macOS High Sierra 正式版 USB 启动盘的方法 （亲测可用）"

date: "2016-07-22 10:29:32"

tags: 
    - iOS
---

制作 **macOS High Sierra** 正式版 [USB](https://www.iplaysoft.com/tag/usb) 启动盘的方法有很多，用户可以选择使用命令行来创建，也可以选择第三方U盘制作工具来制作，大家可以根据自己的喜好选择。
本教程首先介绍命令行的方式，因为这是苹果官方系统内置的命令，优点是稳妥而且没有兼容性问题，只是需要通过命令行操作，对新手来说可能看似有点复杂，但其实步骤还是非常简单的。
<!-- more --> 

### 方法一：使用命令行创建制作 macOS High Sierra 正式版 USB 安装盘
1.  首先，准备一个 [8GB 或更大容量的 U盘](https://www.iplaysoft.com/go/upan)，并[备份](https://www.iplaysoft.com/tag/%E5%A4%87%E4%BB%BD)好里面的所有资料。

2.  [下载好 macOS High Sierra 正式版的安装程序]
3. 打开 “应用程序 → 实用工具 → 磁盘工具”，将U盘「抹掉」(格式化) 成「Mac OS X 扩展（日志式）」格式、GUID 分区图，并将U盘命名为「Sierra」。(注意：这个盘符名称将会与后面的命令一一对应，如果你改了这盘符的名字，必须保证后面的命令里的名称也要一致。)

![image.png](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy8xODk5OTM0LTRjMzgxYTc1NTkxYWZhYTcucG5n)

4. 打开 “应用程序→实用工具→终端”，将下面的一段命令复制并粘贴进去：
如果你要制作 High Sierra，拷贝这段命令：

```
sudo /Applications/Install\ macOS\ High\ Sierra.app/Contents/Resources/createinstallmedia --volume /Volumes/Sierra --applicationpath /Applications/Install\ macOS\ High\ Sierra.app --nointeraction
```

* 请注意红色的「Sierra」要对应你的 U 盘命名 (上图3号处)
---------------------------------------
如果你要制作「旧版本的 macOS Sierra」，则拷贝这段命令：

```
sudo /Applications/Install\ macOS\ Sierra.app/Contents/Resources/createinstallmedia --volume /Volumes/Sierra --applicationpath /Applications/Install\ macOS\ Sierra.app --nointeraction
```
5. 回车并执行该命令，这时会提示让你输入管理员密码，便会开始制作过程了：![image.png](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy8xODk5OTM0LTFkOGEwNTA2NDNhYTc3YTEucG5n)

6.  如上图，这时系统已经在制作中了，请耐心等待直到屏幕最后出现 Done. 字样即表示大功告成了！然后，就带着[U盘](https://www.iplaysoft.com/tag/U%E7%9B%98)出去浪吧……

### 方法二：使用 DiskMaker X 启动盘制作工具：
如果你不喜欢任何[代码](https://www.iplaysoft.com/tag/%E4%BB%A3%E7%A0%81)、命令之类的操作，那么除了上面使用命令行来制作 macOS 的启动/安装盘的方法外，我们也有更加傻瓜直观一点的方法，那就是通过 **DiskMaker X** 工具来制作 macOS 安装U盘。

[Diskmaker X](https://www.iplaysoft.com/macos-usb-install-drive.html) 是一款免费的 **macOS USB 启动盘制作软件**，当然前提也是要先下载好 macOS Sierra 正式版的安装程序。最新的 **DiskMaker X 7 已更新支持制作 macOS High Sierra** / Sierra / OS X Yosemite / El Capitan 等不同版本的系统安装盘，启动后会让你选择，按照你的需要选择即可
![image.png](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy8xODk5OTM0LTU5ZjAyOTcxNmVlMTUwMzkucG5n)

如果你已经下载好 macOS 的安装程序，那么正常情况下，Diskmaker X 会自动帮你找到其路径的，点击 Use This Copy 继续下一步
![image.png](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy8xODk5OTM0LWFjZjU4MDI4NzJjYWM4ZDEucG5n)

之后，DiskMaker X 会提示你需要一个至少 [8GB 容量的U盘](https://www.iplaysoft.com/go/upan)，将 U 盘插入 Mac 之后，点击 “An 8 GB USB thumb drive” 按钮下一步
![image.png](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy8xODk5OTM0LWZkOTg2OTQyYmQyMzM4ZjcucG5n)

这时会出现选择 U 盘盘符的窗口（请注意千万不要选错盘符哦！！）：![image.png](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy8xODk5OTM0LWM4NDdiYzNiZGNhZDFmNWIucG5n)

确认格式化并抹除 U盘 (Erase then create the disk)，然后就开始一条龙制作过程了。

### 通过 U 盘安装 macOS Sierra / 格式化重装 (抹盘全新安装系统) 方法

当你制作好 macOS Sierra 的安装盘 U 盘之后，你就可以利用它来给 [Mac 电脑](https://www.iplaysoft.com/go/mac)格式化重装 (抹盘安装)了。操作的方法非常简单

1.  当然还是要想办法[备份](https://www.iplaysoft.com/tag/%E5%A4%87%E4%BB%BD)好 Mac 里所有的重要数据了。
2.  插上制作好的安装U盘，如果系统能识别出来即可，这时我们先关机了。
3.  按下电源键开机，当听到“噹”的一声时，按住 Option 键不放，直到出现启动菜单选项：
![image.png](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy8xODk5OTM0LTY4MTJlYjZmMDVlMjE2MmMucG5n)

4. 这时选择安装U盘 (黄色图标) 并回车，就可以开始安装了，在过程中你可以通过“磁盘工具”对 Mac 的磁盘式化或者重新分区等操作。
5. 之后就是一步一步的安装直到完成了。


转载自 <https://www.iplaysoft.com/macos-usb-install-drive.html>

