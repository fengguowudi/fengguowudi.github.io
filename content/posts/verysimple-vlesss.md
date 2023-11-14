---
title: "使用verysimple搭建一个vless代理"
date: 2023-11-14T21:28:18+08:00
tags: [奇技淫巧,技术教程]
draft: false
description: VerySimple近期不再更新，酌情使用哦~
---

<!--more-->

## 1.准备工作

系统：debian11
软件包：`wget` ` sudo`  `nano`

## 2.安装

```
wget https://github.com/hahahrfool/v2ray_simple/releases/download/v1.1.0.3/verysimple_linux_amd64.tgz
tar -xzf verysimple_linux_amd64.tgz
sudo mkdir -p /usr/local/etc/verysimple
sudo mv verysimple /usr/local/etc/verysimple/
sudo cp examples/grpc.server.toml /usr/local/etc/verysimple/server.toml
```

若不是amd64 cpu请手动更改amd64为自己核心架构。
然后修改 /usr/local/etc/verysimple/server.toml ,使配置达到你想要的效果
**注：软件版本会更新，请注意更新版本号！**

## 3.配置

### 3.1 配置文件
```
[[listen]]
protocol = "vlesss"
uuid = "a684455c-b14f-11ea-bf0d-42010aaa0003" #注意修改
host = "0.0.0.0"
port = 4434
version = 0
insecure = true #若不是自签名证书记得关闭
fallback = ":80"
cert = "cert.pem"
key = "cert.key"
advancedLayer = "grpc"
path = "ohmygod_verysimple_is_very_simple"  #正常来说不宜前面再加斜杠,不过我也没试过，也许加了也能用(两端都加的情况下)注意修改,一定要够长

# 如需使用 Nginx、Caddy 等软件进行分流，设置的分流路径应为 /${path}/Tun

[[dial]]
protocol = "direct"
```
### 3.2 关于证书

最好是用 自己真实拥有的域名，使用acme.sh等脚本申请免费证书，特别是建站等情况。

而且用了真证书后，别忘了把配置文件中的 insecure=true 给删掉.

使用自签名证书是会被中间人攻击的，再次特地提醒。如果被中间人攻击，就能直接获取你的uuid，然后你的服务器 攻击者就也能用了。

要想申请真实证书，仅有ip是不够的，要拥有一个域名。本项目提供的自签名证书仅供快速测试使用，切勿用于实际场合。

### 3.3 生成自签名证书

先cd到安装目录 ex: `cd /usr/local/etc/verysimple`

**注：运行第二行命令时会要求你输入一些信息。确保至少有一行不是空白即可，比如打个1**

```
openssl ecparam -genkey -name prime256v1 -out cert.key
openssl req -new -x509 -days 7305 -key cert.key -out cert.pem
```
此命令会生成ecc证书，这个证书比rsa证书 速度更快, 有利于网速加速（加速tls握手）。

## 4.测试

`手动命令行`

```
#服务端，标准模式
/usr/local/etc/verysimple/verysimple -c server.toml
```

## 5.运行

编辑服务文件 `sudo nano /etc/systemd/system/verysimple.service`
```
[Unit]
After=network.service

[Service]
ExecStart=/usr/local/etc/verysimple/verysimple -c server.toml

[Install]
WantedBy=default.target
```

然后运行以下命令

```
sudo chmod 664 /etc/systemd/system/verysimple.service

sudo systemctl daemon-reload
sudo systemctl enable verysimple
sudo systemctl start verysimple
```

最后使用`sudo systemctl status verysimple`
查看运行效果，要是是绿绿的running那么恭喜你！成功了！

### 6.更新程序

```
wget https://github.com/hahahrfool/v2ray_simple/releases/download/v1.1.0.3/verysimple_linux_amd64.tgz
tar -xzf verysimple_linux_amd64.tgz
mv verysimple /usr/local/etc/verysimple/
systemctl restart verysimple
systemctl status verysimple
```
注：请更新`v1.1.0.3`为最新版本号，`amd64`为自己cpu核心架构

### 7.结语

感谢大佬们的辛勤付出！  
参考文章：  
`https://github.com/hahahrfool/v2ray_simple/blob/main/install.md`  
`https://github.com/hahahrfool/v2ray_simple/blob/main/README.md `  
帮助列表：  
`https://t.me/v2raysimple`