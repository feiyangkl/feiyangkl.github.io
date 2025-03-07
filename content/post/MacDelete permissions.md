---
title: "Mac 更改用户名丢失权限操作"

date: "2018-10-22 10:29:32"

tags: 
    - Mac
---
1在开启电脑时按住 Command-S 键，以单用户模式(以新視窗開啟)(以新視窗開啟)启动。等待白色文本出现。﻿

2在提示符处，单行键入如下所示的每个命令，并在每行结束时按 Return 键。﻿

3﻿

4mount -uw /﻿

5 cd /var/db﻿

6 mv dslocal dslocal.old﻿

7 mkdir -p dslocal/nodes﻿

8 cp -Rp /System/Library/DirectoryServices/DefaultLocalDB/Default dslocal/nodes/﻿

9 rm .AppleSetupDone﻿

10 reboot﻿
<!-- more --> 
11 ﻿

12重新启动电脑后，完成“设置助理”，以再次创建管理员用户帐户。创建帐户时，输入到“帐户名称”字段中的短名称应与位于“/用户”中的用户个人文件夹的名称相匹配，以便新用户使用现有的个人文件夹。﻿

13运行“设置助理”后，打开“终端”并键入以下命令：﻿

14﻿

15sudo chown -R 用户名:staff /Users/用户名﻿

16  注：“/Users/”前有一个空格。将“用户名”替换为上一步中在“帐户名称”字段中输入的用户短名称。﻿

17如果电脑具有多个用户，请使用“系统偏好设置”的“帐户”面板来重新创建其余的任何用户。创建每个帐户时，输入到“帐户名称”字段中的短名称应与位于“/用户”中的用户个人文件夹的名称相匹配，以便新用户使用现有的个人文件夹。1在开启电脑时按住 Command-S 键，以单用户模式(以新視窗開啟)(以新視窗開啟)启动。等待白色文本出现。﻿

2在提示符处，单行键入如下所示的每个命令，并在每行结束时按 Return 键。﻿

3﻿

4mount -uw /﻿

5 cd /var/db﻿

6 mv dslocal dslocal.old﻿

7 mkdir -p dslocal/nodes﻿

8 cp -Rp /System/Library/DirectoryServices/DefaultLocalDB/Default dslocal/nodes/﻿

9 rm .AppleSetupDone﻿

10 reboot﻿

11 ﻿

12重新启动电脑后，完成“设置助理”，以再次创建管理员用户帐户。创建帐户时，输入到“帐户名称”字段中的短名称应与位于“/用户”中的用户个人文件夹的名称相匹配，以便新用户使用现有的个人文件夹。﻿

13运行“设置助理”后，打开“终端”并键入以下命令：﻿

14﻿

15sudo chown -R 用户名:staff /Users/用户名﻿

16  注：“/Users/”前有一个空格。将“用户名”替换为上一步中在“帐户名称”字段中输入的用户短名称。﻿

17如果电脑具有多个用户，请使用“系统偏好设置”的“帐户”面板来重新创建其余的任何用户。创建每个帐户时，输入到“帐户名称”字段中的短名称应与位于“/用户”中的用户个人文件夹的名称相匹配，以便新用户使用现有的个人文件夹。