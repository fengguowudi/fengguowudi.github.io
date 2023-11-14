---
title: 'OpenClash搭配AdGuard Home使用教程'
date: 2021-09-12 08:21:24
tags: [openclash,技术教程]
published: true
hideInList: false
feature: 
isTop: false
---
### 前言



--





openclash这个插件，我之前有出过视频怎么使用，它的功能无疑是我用过所有科学插件中最为强大的一个，无论是ssrplus+亦或者是passwall和最近的bypass，体验起来都不如openclash好用~





为啥这样说，





1、因为openclash的分流功能非常强大和精准，可以说是指哪打哪，这点可以从奈飞分流就可以体现出来，其他插件虽然也可以分流，但精准度不如openclash！





2、具有更加强大的广告过滤规则，写个rule-provider规则放进去，拦截广告也非常精准，其他插件虽然也有此功能，但并不怎么好用~





3、无论是自动切换节点还是手动切换，都要简单的多，切换速度这点来说，其他插件也是无法比拟的





那基于以上三点，openclash也就成为了我最为喜欢的一款插件之一~





但同时也有缺点，打开配置页面的时候，会让很多小白朋友一脸懵逼，一看设置太复杂，甚至还有很多小伙伴都无法正确配置把插件运行起来~所以很快就放弃使用了！





**其实这个插件在配置起来并不是太麻烦，下面先说一下基本启动流程，方便小白朋友能顺利运行起来这个插件**





**1、首先进入openwrt后台在服务里找到openclash点击进入**





![](https://fg666.coding.net/p/blog/d/pic1/git/raw/master/66d913f8ee12e30f73e61c4ed1630284506ae79705b7588f716b45e96874fdcb.png) **2、进来后应该是这个样子，然后点击全局设置，下面有个版本更新点进去，检查一下机构是否和你机器架构一样，一般默认就是你机器的架构，直接点击一键更新就行了，如图**





![](https://fg666.coding.net/p/blog/d/pic1/git/raw/master/fc116e0f1675ef89769d00f3fcc38f73ee4ec1adb33487da7d92672cf9e8c45e.png)





**3、导入配置文件，可直接用插件的在线订阅转换模板快速在线配置，直接输入你的机场订阅链接和模板，最后点击保存配置即可**![](https://fg666.coding.net/p/blog/d/pic1/git/raw/master/6c95b4bf4186822da097702769bd991f1378b5e72c74d223687f46725024cbcb.png)





也就说，只需要三步操作就可以正常使用openclash了，下面开始今天的主题教程，用adguardhome配合openclash达到广告过滤以及DNS的设置





ADG+OPenclash配置教程





adgurdhome插件的配置安装，这里就不说了，很简单，不会的可以谷歌一下，下面直接说配置过程





1、打开openclash，找到全局设置，DNS设置，如图





![](https://fg666.coding.net/p/blog/d/pic1/git/raw/master/bef08d03ff7a6cd88c2c0c5e1171a569c8274879b8b6be6d1fbbd86ececdd6a2.png)





下面手动填入DNS的IP，建议用默认就可以，也就是说不需要做其他修改~如图  



![](https://fg666.coding.net/p/blog/d/pic1/git/raw/master/f1aefef12d693dcfc2a26e723f50ff0b8687d7bff4aaf671fbb437a6ae21ea9e.png)





**到此openclash的设置到这里就完成了，下面配置adguardhome**





adguardhome的重定向需要改为dnsmasq作为上游服务器  



![](https://fg666.coding.net/p/blog/d/pic1/git/raw/master/69c77e326d630abdfb05c4eadb52d078e4aa403a7521c167ec9bf873799c0ab9.png)





启动插件后点击![](https://fg666.coding.net/p/blog/d/pic1/git/raw/master/fc55409bfebb2a3be1edde747b8e7382f32dd7d7c66c2999770cfa40988469f6.png)进入配置页面，点击设置–DNS设置–上游dns服务器填写 **127.0.0.1:7874 如图** ![](https://fg666.coding.net/p/blog/d/pic1/git/raw/master/65930931d7306e504277976616572b4d63f45af26cf9922ddddef9668f7822fb.png)





**Bootstrap DNS 服务器就填写你本地运营商的DNS即可** ![](https://fg666.coding.net/p/blog/d/pic1/git/raw/master/75531e3bf48dc0ce8e1273af2b053fc1c523caa2b26238b8ab12b10afb790ed0.png) ![](https://fg666.coding.net/p/blog/d/pic1/git/raw/master/a3bc70db5f198eeb1e5e09b52039bc6b0a0155825fd8cea41c762b7d488f81fa.png)





**这个地方可能会提示指定DNS服务错误，老版本的adg可能会出现，我这个新版则没有问题，但无论新版老版，报错无视即可，经测试是可以正常使用的**





### 添加广告过滤规则





![](https://fg666.coding.net/p/blog/d/pic1/git/raw/master/a08b8254fd5656c88cf6553baecac3751f20872de1e291d97f39c92d3c3241dd.png)







```
#1，anti-AD





https://cdn.jsdelivr.net/gh/privacy-protection-tools/anti-AD@master/anti-ad-easylist.txt





#2，halflife合并乘风





https://gitee.com/halflife/list/raw/master/ad.txt



```







配置完毕完成，广告规则如果有好的可以继续添加，目前我只用这2个加默认基本满足我的需求





### 过滤效果





![](https://fg666.coding.net/p/blog/d/pic1/git/raw/master/c946155e319aa9a204c3fbb5f71a6a3106795801df238bb8e8d86212937d097d.png)





### 后记



--





**双ADG对接openclash**





**1.如果想实现精准分流，那么还需要在DNS设置选项下面打开fallback选项，将规则导入进去，保存应用配置即可~配置文件：[点击查看](https://raw.githubusercontent.com/wxfyes/cf/main/openclashfallback.txt) 讲内容直接粘贴复制即可**





![](https://fg666.coding.net/p/blog/d/pic1/git/raw/master/8108f2c186999cedfc0b718a09777c75b509ddca1b109b235fa3dc31291e0d86.png)





**2.将运行模式改为redir-tun模式**





**3.dns组启用，nameserver组和fallback组输入对应ADG的DNS地址**





**4.全局–GEOIP订阅更新打勾**





**最后重新启动openclash，分流效果将会更好！（如打不开网页，请重新尝试启动openclash）**





**测试DNS泄露的几个网站–国内：**[网易dns检测](https://nstool.netease.com/) **国外：** [ipleak](https://ipleak.net)  [browserleaks](https://browserleaks.com/dns)  [whoer.net](https://whoer.net/zh)





**教程结束~**