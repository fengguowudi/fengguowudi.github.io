---
title: '我的世界基岩版BDS服务器搭建与使用'
date: 2021-09-10 18:54:59
tags: [我的世界]
published: true
hideInList: false
feature: 
isTop: false
---
## 前期需要软件

1.  远程SSH控制台软件（本教程使用 Remote Terminal 此为win10商店软件）[\[点我下载(此软件为Xshell)\]](https://www.lanzous.com/i88wyvi)
2.  支持Sftp的FTP软件（本教程使用 FileZilla）[\[点我下载\]](https://www.lanzous.com/i88wyqd)
3.  服务端（本教程使用 官方bds服务端）
4.  Centos或Ubuntu主机一台 （教程使用 centos7.7）

## 注意事项

1.  极力推荐Ubuntu系统，Centos需要另需环境。
2.  不支持 **Windows Server 2012** 及以下版本，要使用 **Windows 10** （可行）或者 **Windows Server 2016** 及以上版本运行
3.  我的世界基岩版端口为 **UDP协议的19132** 端口，如果使用其他端口请在配置文件中设置成其他端口
4.  请开放端口为 **UDP协议的19132** 端口，基岩版需要他进行通信(**列如阿里云、腾讯云需去安全组进行开放端口**) \[若是虚拟机请改成其他端口，VPS运营商一般是不会给你常用端口的\]

## Windows开服

1.  首先我们去Minecraft官方下载最新服务端

-   [\[前往官网\]](https://www.minecraft.net/zh-hans/download/server/bedrock/)
-   点击左边的**Windows版**并**同意协议**下载  
    ![下载服务端](https://fg666.coding.net/p/blog/d/pic1/git/raw/master/1fb458a63ea44f23546858fb6c5ee0194844771db338cfdab5c531175700ec6c.png)

2.  解压后双击「bedrock\_server.exe」文件即可
3.  下面就是需要内网穿透软件等来实现了(需要UDP协议)，但这个最好去找ECS或VPS租用服务器比较好

## Centos开服

1.  首先我们去Minecraft官方下载最新服务端

-   [\[前往官网\]](https://www.minecraft.net/zh-hans/download/server/bedrock/)
-   点击右边的**Ubuntu版**并**同意协议**下载  
    ![image.png](https://fg666.coding.net/p/blog/d/pic1/git/raw/master/c242ec626a37c75c95f40f2c3247f90c1b8ab1716dd7cfa36ab2c7d219dca8ed.png)

2.  我们需要安装一些环境解压和下载所需程序

    yum install wget unzip -y
    
3.  在 GitHub 上有 Pro 做的一个整合包，把运行环境就整合在一起了！

    wget https://github.com/Sysca11/autopack/archive/bdsm.zip
    unzip -q bdsm.zip
    cd autopack-bdsm
    unzip -q pack.zip
    cd packed
    # packed文件夹内的文件是我们需要的其他的可自行删除
    chmod +x *
    
    # 给予当前目录下所有文件最高权限

4.  将我们**第一步**下载的整合包下载下来并解压

    wget -c https://minecraft.azureedge.net/bin-linux/bedrock-server-1.14.32.1.zip
    # 注意这可能不是最新的，具体怎么获得链接可以查看我第一步
    unzip -q bedrock-server-1.14.32.1.zip
    
    # 解压文件名请注意刚才的下载链接后缀

5.  启动服务器

    LD_PRELOAD= ./ld-linux-x86-64.so.2 --inhibit-cache --library-path . ./bedrock_server
    

### 优化拓展说明(可忽略)

1.  我们首先将packed移出至一个地方

    # 此步骤在上有基础上执行以下指令
    cd ../
    cp -r packed $HOME
    # 复制一份到自己的用户目录
    cd
    # 进入自己的用户目录
    mv packed bds
    # 将文件重名为 bds (这里名字可自行更改)
    cd bds
    
    # 进入服务器目录(目录名为上面设置的)

2.  写一键启动脚本

    vi bds.sh
    # 新建一个名为 bds 的脚本
    
    LD_PRELOAD= ./ld-linux-x86-64.so.2 --inhibit-cache --library-path . ./bedrock_server
    
    # 写入以上指令
    

按“i”键进行编辑，退出先按“ESC”然后“:”

-   :q 是直接退出
-   :wq 保存后退出
-   :q! – 不保存内容， 强制退出

3.  给予脚本权限并启动他

    chmod +x bds.sh
    # 给予脚本最高权限
    sh bds.sh 或者 ./bds.sh
    
    # 启动脚本
    

### 保持后台运行

先`stop`关闭服务器，我们使用screen软件来让服务器保持后台运行。

    yum install screen
    # 创建一个名为 bds 的窗口
    screen -S bds
    # 在此新终端窗口内启动服务器
    sh bds.sh


## Ubuntu开服

1.  首先我们去Minecraft官方下载最新服务端

-   [\[前往官网\]](https://www.minecraft.net/zh-hans/download/server/bedrock/)
-   点击右边的**Ubuntu版**并**同意协议**下载  
    ![image.png](https://fg666.coding.net/p/blog/d/pic1/git/raw/master/c242ec626a37c75c95f40f2c3247f90c1b8ab1716dd7cfa36ab2c7d219dca8ed.png)

2.  我们需要安装一些环境解压和下载所需程序

    sudo apt-get update #先更新系统(如果出现问题安装失败的话)
    apt-get install zip unzip wget -y
    
3.  将我们**第一步**下载的整合包下载下来并解压

    wget -c https://minecraft.azureedge.net/bin-linux/bedrock-server-1.14.32.1.zip
    # 注意这可能不是最新的，具体怎么获得链接可以查看我第一步
    unzip -q bedrock-server-1.14.32.1.zip
    
    # 解压文件名请注意刚才的下载链接后缀

4.  启动服务器

    ./bedrock_server
    

### 保持后台运行

先`stop`关闭服务器，我们使用screen软件来让服务器保持后台运行。

    apt install screen
    # 创建一个名为 bds 的窗口
    screen -S bds
    # 在此新终端窗口内启动服务器
    ./bedrock_server