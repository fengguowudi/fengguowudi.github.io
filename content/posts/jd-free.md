---
title: 'jd的艺术'
date: 2021-09-10 18:36:12
tags: [奇技淫巧]
published: true
hideInList: false
feature: 
isTop: false
---
我看最近的狗东的ldz很火哈。所以我也来凑个热闹发个教程。    

## 准备工作    
1.一台openwrt系统设备
2.一个脑子
3.一双手    

话不多说，开始吧！

## 步骤

### 一、链接N1（你的设备）

这里需要一款ssh工具，我是比较推荐finalshell的。   

[windows版下载地址](http://www.hostbuf.com/downloads/finalshell_install.exe)    

[macos版下载地址](http://www.hostbuf.com/downloads/finalshell_install.pkg)

链接方式我懒得说了，请[百度](www.baidu.com)

### 二、安装

因为现在的大部分openwrt固件都自带docker，所以我就不过多赘述了。  

这里使用的项目叫做青龙，感谢作者的贡献。    

接下来需要在ssh里依次输入下列指令：    
```
青龙项目指南:

1. 拉取镜像/更新镜像

docker pull whyour/qinglong:latest

2. 删除镜像

docker rmi whyour/qinglong:latest

3. 启动容器 


docker run -dit \
   -v /root/ql/config:/ql/config \
   -v /root/ql/log:/ql/log \
   -v /root/ql/db:/ql/db \
   -p 5700:5700 \
   -e ENABLE_HANGUP=true \
   -e ENABLE_WEB_PANEL=true \
   --name qinglong \
   --hostname qinglong \
   --restart always \
   whyour/qinglong:latest
```

### 三、初次登陆

初次访问 `http://<自己ip>:5700`    

![](https://fg666.coding.net/p/blog/d/pic1/git/raw/master/be6ef08983167b454170fc01f80893b956e1a76082296bf66d5156d41b6bdc9e.jpg)

使用 admin/adminadmin 登陆，提示已初始化密码    

去自己映射目录config下找 auth.json，查看里面的 password    

docker exec -it qinglong cat /ql/config/auth.json   

登陆    

### 四、配置

#### 第一步

登录后，主界面应该类似于这样

![](https://fg666.coding.net/p/blog/d/pic1/git/raw/master/bf68b4cad745b388b6ad7160d1e528113a8d99b99adb3ec4cbca9338b9743c5f.jpg)

你们可能会注意到，你们登陆之后没有这么多的脚本，不要慌，你的安装没有出问题 

接着你需要点这个

![](https://fg666.coding.net/p/blog/d/pic1/git/raw/master/40aa052635ec47d40ba9d1f26b84324b4265423c79a916b97fd535bfca0a819a.jpg)

然后等待一会

刷新

就会发现多出来很多脚本

#### 第二步

点到这里

![](https://fg666.coding.net/p/blog/d/pic1/git/raw/master/273aeace0994ae08365370881b166c85075151ea1a59d142ad23533634273f07.jpg)

你会发现没有cookie，这时我们需要回到ssh，输入这个

`docker exec -it qinglong js getJDCookie.js now`

他会提示没有cookie，你先随便填一个，在运行这个就没事了

获取cookie再填入就基本完成了。

注：可以填入多个cookie同时进行任务~~只要你机器撑得住~~

最后再输入

`docker exec -it qinglong js hangup`

重新加载一下脚本

教程就到这里啦，谢谢观看！

~~期待后续的进阶教程吧。虽然可能有人早就会了QAQ~~
