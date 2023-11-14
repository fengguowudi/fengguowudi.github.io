---
title: 'Nvjdc 1.4 安装 备份-非原创'
date: 2022-01-14 20:01:28
tags: []
published: true
hideInList: false
feature: 
isTop: false
---
前言
--

原作者NolanHzy删库了，现在网上所有的安装教程都失效了，因为拉取不到源码和镜像。下面教程中的镜像是我复刻的的nvjdc 1.4的镜像，源码拉取的是github上fzls备份2021.11.16的nvjdc 1.4版本

提示
--

测试环境：  
CentOS 7.6 64bit，arm不支持

源码拉取的是打包过的，自带config.json 配置文件

不是热更新，每次修改配置需要重启容器

安装教程（docker安装）
--------------

1.拉源码

国内机

```
git clone https://ghproxy.com/https://github.com/fzls/nvjdcdocker.git /root/nolanjdc
```

国外机

```
git clone https://github.com/fzls/nvjdcdocker.git /root/nolanjdc
```

2 .拉取基础镜像

```
docker pull shifanga/nolanhzy:latest
```

3 .执行命令

```
yum install wget unzip -y
```

4创建一个目录放配置

```
cd /root/nolanjdc
```

```
mkdir -p  Config && cd Config
```

5.将config.json文件修改为自己的配置 （拉取的源码里自带config.json文件，安装提示修改即可，下面的为原版文件）

```
{
  ///浏览器最多几个网页
  "MaxTab": "4",
  //网站标题
  "Title": "NolanJDCloud",
  //回收时间分钟 不填默认3分钟
  "Closetime": "3",
  //网站公告
  "Announcement": "为提高账户的安全性，请关闭免密支付。",
  ///开启打印等待日志卡短信验证登陆 可开启 拿到日志群里回复 默认不要填写
  "Debug": "",
  ///自动滑块次数5次 5次后手动滑块 可设置为0默认手动滑块
  "AutoCaptchaCount": "5",
  ///XDD PLUS Url  http://IP地址:端口/api/login/smslogin
  "XDDurl": "",
  ///xddToken
  "XDDToken": "",
  ///青龙配置  注意对接XDD 对接芝士 设置为"Config":[]
  "Config": [
    {
      //序号必填从1 开始
      "QLkey": 1,
      //服务器名称
      "QLName": "阿里云",
      //青龙地址
      "QLurl": "",
      //青龙2,9 OpenApi Client ID
      "QL_CLIENTID": "",
      //青龙2,9 OpenApi Client Secret
      "QL_SECRET": "",
      //CK最大数量
      "QL_CAPACITY": 40,
      "QRurl": ""
    }
  ]

}
```

6 .回到nolanjdc目录创建chromium文件夹并进入

```
cd /root/nolanjdc && mkdir -p  .local-chromium/Linux-884014 && cd .local-chromium/Linux-884014
```

7.下载 chromium

```
wget https://mirrors.huaweicloud.com/chromium-browser-snapshots/Linux_x64/884014/chrome-linux.zip && unzip chrome-linux.zip
```

8.删除刚刚下载的压缩包

```
rm  -f chrome-linux.zip
```

9.回到刚刚创建的目录

```
cd  /root/nolanjdc
```

10.启动镜像

```
sudo docker run   --name nolanjdc -p 5703:80 -d  -v  "$(pwd)":/app \
-v /etc/localtime:/etc/localtime:ro \
-it --privileged=true shifanga/nolanhzy:latest
```

11.查看 日志

```
docker logs -f nolanjdc
```

出现 NETJDC started 代表安装成功

浏览器输入 ip:5703即可访问，如果打不开检测一下有没有开启端口，确认开启了端口可以重启试试

重启命令

```
docker restart nolanjdc
```