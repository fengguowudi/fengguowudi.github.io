---
title: '搭建自己私人网盘-Nextcloud'
date: 2021-09-10 18:49:36
tags: [技术教程]
published: true
hideInList: false
feature: 
isTop: false
---
## 前期需要软件

1.  远程SSH控制台软件（本教程使用 Remote Terminal 此为win10商店软件）[\[点我下载(此软件为Xshell)\]](https://www.lanzous.com/i88wyvi)
2.  支持Sftp的FTP软件（本教程使用 FileZilla）[\[点我下载\]](https://www.lanzous.com/i88wyqd)
3.  Centos或Ubuntu主机一台 （教程使用 centos7.7）

## 注意事项

1.  推荐Ubuntu系统（由于个人偏向，本教程使用Centos）
2.  未标注哪个系统指令则两者都可适用
3.  若标题注释了哪个系统那只能适用那个系统或另一个系统不需要输此指令

## 环境部署

### 系统更新

-   首先你要确保你的系统是最新的

    yum update # (Centos)
    //Ubuntu需要两条指令来升级
    apt-get update -y # (Ubuntu)
    apt-get upgrade -y # (Ubuntu)
    

### 宝塔安装

1.  使用指令安装

    //Centos指令
    yum install -y wget && wget -O install.sh http://download.bt.cn/install/install_6.0.sh && sh install.sh
    //Ubuntu指令
    wget -O install.sh http://download.bt.cn/install/install-ubuntu_6.0.sh && sudo bash install.sh
    
2.  安装所需环境

-   首次进入会让你安装基础程序【这里选**LAMP**个人喜欢这个，并且不需要自己添加伪静态】
    -   这里选择版本为：
    -   Apache 2.4
    -   MySQL 5.7
    -   Pure-Ftpd 1.0.49
    -   PHP 7.3(并安装扩展名: fileinfo)
    -   phpMyAdmin 4.7
-   这里最好选**编译安装**，极速安装虽说快，但他不稳定，很不推荐.
-   下载速度日常很慢（根据服务器性能，我这里就是晚上弄然后睡觉去了）

![安装LAMP环境包](https://fg666.coding.net/p/blog/d/pic1/git/raw/master/4fc678175d30f4a6c0de8dad7d37e51efe0e4b144dba04f5f4f21bb5533a390b.jpg)  
![安装PHP扩展名: fileinfo](https://fg666.coding.net/p/blog/d/pic1/git/raw/master/298c8b7f24be4d0f4935d2d52b915446422d4113a4f4df39acbe5cff64d868ed.jpg)  
![](https://fg666.coding.net/p/blog/d/pic1/git/raw/master/dbaaa020f6505f06e50509715c3d9e1a9e41d03c5738b91a974f8f61f6edc5a8.jpg)

3.  删除PHP中的禁用函数(现在的PHP和以前相比已经不需要禁用函数了，反而导致程序错误)

-   按下键盘**Ctrl+F**开启搜索功能搜索`disable_functions`，并删除后面的禁用函数，随后重启PHP。

![image.png](https://fg666.coding.net/p/blog/d/pic1/git/raw/master/49ba0a5016636e5159de1e4845ac734d8b1269646dbaaeff76f2e9df2a218b51.jpg)

4.  新建网站+数据库，并将数据库账户密码记录，在下文会使用到

![创建网站](https://fg666.coding.net/p/blog/d/pic1/git/raw/master/6d01d0b108e5e0fe80fd2f06544f75011a62144e14da8832f25a8303f5d5952a.jpg)

5.  在SSH部署时需手动`cd`到网站目录并保证**目录为空**。(都面板化了，手动删除4个文件应该不费事吧)

## 安装Nextcloud

1.  克隆Nextcloud文件到本地

-   对于网盘系统有seafile、可道云，国外NextCloud，在自我衡量之后发现NextCloud更适合做网盘系统
-   [\[前往Nextcloud下载页\]](https://nextcloud.com/install/#instructions-server)下载**NextCloud**安装包，并上传至服务器或`cd`到你要部署的文件夹自行下方命令
-   **注意！** 链接非统一性，在官方随时更新版本链接随时变更，链接最好前往官方获取链接

    wget -c https://download.nextcloud.com/server/releases/nextcloud-18.0.1.zip
    

![下载nextcloud](https://fg666.coding.net/p/blog/d/pic1/git/raw/master/da61c698c60e3263e37b28bb5d788440bc4eda517e4df96cc61f9f8f6ff60e6c.jpg)

2.  解压压缩包`nextcloud-18.0.1.zip`

![](https://fg666.coding.net/p/blog/d/pic1/git/raw/master/6deeae61b0b99d74b93483f4f8353e1dbb33dfcb1e96333e953c777f90dee44e.jpg)

3.  移动`nextcloud`内安装文件，迁移至网站目录

![image.png](https://fg666.coding.net/p/blog/d/pic1/git/raw/master/98d654c482969a402e7e1a6a970af4c87556b813b11a9f25fa2e569ff5ffd04b.jpg)

4.  删除无用文件的`nextcloud``nextcloud-18.0.1.zip`，不删除容易出错

    rm -rf nextcloud
    rm -rf nextcloud-18.0.1.zip
    
5.  变更权限组合权限，权限选择「755」+用户组选择「www」，最后确定即可

![权限755+www](https://fg666.coding.net/p/blog/d/pic1/git/raw/master/771423606207685241c87b8989299fc92ad993637180d4edda4fd623ff81a439.jpg)

6.  直接打开自己绑定的域名，将上面用记下来的数据库用户名和密码写入相应的位置

![](https://fg666.coding.net/p/blog/d/pic1/git/raw/master/a2d323925a8e946304ee788b1da08fad475200e5b47bfdb296ea82ea05f8f525.jpg)

7.  点击安装后就安装好啦

## 维护模式指令

    sudo -u www php occ maintenance:mode --on
    sudo -u www php occ maintenance:mode --off


## 解决后续问题

-   在「设置」中「管理栏」的选项中有「安全与设置警告」，实际上没多大事，如果有强迫症，下方可以解决

![错误](https://fg666.coding.net/p/blog/d/pic1/git/raw/master/c09c1518786163daca001fbb81480234a8ae0a1a4d22ce71bfcb22adc216a13a.jpg)

### PHP 内存限制低于建议值 512MB

> 宝塔php点击**配置修改**，编辑`脚本内存限制`,给**512M**即可

### PHP 的 OPcache 模块未载入。推荐开启获得更好的性能。

> 按照提示在php配置文件中搜索Zend ZendGuard Loader，添加：

    zend_extension=opcache.so


### PHP 的组件 OPcache 没有正确配置。 为了提供更好的性能，我们建议在 php.ini中使用下列设置：

> 按照提示在php配置文件中搜索opcache.enable=1，对应修改成：

    opcache.enable=1
    opcache.enable_cli=1
    opcache.interned_strings_buffer=8
    opcache.max_accelerated_files=10000
    opcache.memory_consumption=128
    opcache.save_comments=1
    opcache.revalidate_freq=1


### 该实例缺失了一些推荐的 PHP 模块。为提高性能和兼容性，我们强烈建议安装它们。`imagick`

> 宝塔php点击安装`imagemagick`。  
> ![安装imagemagick](https://fg666.coding.net/p/blog/d/pic1/git/raw/master/594f9b8ede41a041a1cc5fdfbb33fb5107cc9867aa3f08beb31a7fbd3ef90c60.jpg)

### 内存缓存未配置，为了提升使用体验，请尽量配置内存缓存。

> 宝塔php点击安装`redis` ，编辑`/config`下的`config.php`在`);`前加入以下代码：  
> ![安装redis](https://fg666.coding.net/p/blog/d/pic1/git/raw/master/583f72442168629f894e297f2749fbd6250cbc7d6f716e40e2c85b84e6ab7c07.jpg)

      'memcache.local' => '\OC\Memcache\Redis',
      'redis' => array(
      'host' => 'localhost',
      'port' => 6379,
    ),
    ### 数据库中的一些列由于进行长整型转换而缺失。
    > cd到nextcloud目录，执行
    ```sh
    sudo -u www php occ db:convert-filecache-bigint


### 数据库丢失了一些索引。

> cd到nextcloud目录，执行

    sudo -u www php occ db:add-missing-indices


### 美化URL，去除入口文件index.php

> 使用config.php+OCC命令生成`.htaccess`。编辑`/config/config.php`添加规则：

    'htaccess.RewriteBase' => '/',


> 然后使用occ命令生成.htaccess文件：

    sudo -u www php occ maintenance:update:htaccess


### 通过 HTTP 访问网站不安全。强烈建议您将服务器设置成要求使用HTTPS 协议

> 去证书网站申请证书即可。

### HTTP的请求头 “Strict-Transport-Security” 未设置为至少 “15552000” 秒。为了提高安全性，建议参照security tips 中的说明启用HSTS。

> 在站点配置文件里面添加如下代码，然后重启Apache即可。

    Header always set Strict-Transport-Security "max-age=63072000; includeSubdomains; preload"


## 安装后无法挂载外部共享目录

1.  安装PHP smbclient模块
2.  在php.ini里面启用smb模块

    vim /usr/local/php/etc/php.ini
    //文件最后添加如下内容：
    [smb]
    extension=/usr/local/php/lib/php/extensions/no-debug-non-zts-20160303/smbclient.so
    

## 性能优化

### 定时任务配置为 ‘Cron’

    sudo -u www crontab -e
    # 添加
    */5 * * * * php -f /站点的绝对路径/cron.php
    # 这是我添加的，你们可以进行参考
    */5 * * * * php -f /www/wwwroot/cloud.vlssu.com/cron.php
    lnmp restart