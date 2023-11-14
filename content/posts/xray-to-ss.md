---
title: "使用xray-core搭建一个ss代理"
date: 2022-05-13T18:03:18+08:00
tags: [奇技淫巧,技术教程]
draft: false
description: xray是个很方便的网络代理工具
---

## 一、安装Xray
---

首先，Xray的官方载体，就是 [xray-core](https://github.com/XTLS/Xray-core) 开源项目（基于 `MPL 2.0` 开源协议）生成的二进制程序。你把这个二进制放在服务器运行，它就是服务器端；你把它下载到本地电脑运行，它就是客户端。主要区别来源于【配置】。

安装时，直接使用官方安装脚本就很简单直接。它提供了多种安装选项，有兴趣的可以去官方的[安装脚本仓库](https://github.com/XTLS/Xray-install)中看看脚本的说明，**本文使用的是【非root用户】安装模式**。

写本文时，安装脚本在使用非root账户时有一些小bug，所以我决定正好把这几步分开操作，可以顺便说明一下Linux下的删除命令。


1. 小小白白Linux基础命令：
    |   编号   | 命令名称 | 命令说明 |
    | :------: | :------: | :------: |
    | `cmd-14` |   `rm`   | 删除命令 |

2. 将安装脚本下载至本地：
    ```
    $ wget https://github.com/XTLS/Xray-install/raw/main/install-release.sh
    ```

3. 执行安装命令
    ```
    $ sudo bash install-release.sh
    ```

4. 使用完成之后可以删除该脚本
    ```
    $ rm ~/install-release.sh
    ```

**注意：** 使用 `rm` 命令删除文件的时候，默认其实就是删除现在所在的文件夹下的文件。但是，**我依然写了完整的路径**： `~/install-release.sh`，这是我使用 `rm` 时的一个安全习惯、也是我把安装分成几步之后想强调一下的内容。如果你听过一些“程序员从删库到跑路”之类的段子，大概就知道为什么了。


## 二、配置Xray
---

首先，各种配置都可以参考[官方SS配置示例](https://github.com/XTLS/Xray-examples)。本文会基于官方示例，配置一个最简单的方式。

1. 建立日志文件及文件夹备用
    1. 小小白白Linux基础命令：
        |   编号   | 命令名称 |   命令说明   |
        | :------: | :------: | :----------: |
        | `cmd-16` | `touch`  | 建立空白文件 |
        
    2. 在`vpsadmin`的文件夹内建立一个【日志专用文件夹】
        ```
        $ mkdir ~/xray_log
        ```

    3. 生成所需的两个日志文件（访问日志、错误日志）
        ```
        $ touch ~/xray_log/access.log && touch ~/xray_log/error.log
        ```
        

}
**注意：** 这个位置不是`Xray`标准的日志文件位置，放在这里是避免权限问题对新人的操作带来困扰。当你熟悉之后，建议回归默认位置： `/var/log/xray/access.log` 和 `/var/log/xray/error.log` 。


    4. 因为Xray默认是nobody用户使用，所以我们需要让其他用户也有“写”的权限（`*.log` 就是所有文件后缀是`log`的文件，此时`CLI`界面的效率优势就逐渐出现了）
        ```
        $ chmod a+w ~/xray_log/*.log
        ```

3. 使用`nano`创建`Xray`的配置文件
    ```
    $ sudo nano /usr/local/etc/xray/config.json
    ```

4. 将下面的文件全部复制进去，并更改ss中的密码及端口部分，本文的这个配置文件中增加了我的各种啰嗦注解，以方便你理解每一个配置模块的功能是什么。

    ```
    // REFERENCE:
    // https://github.com/XTLS/Xray-examples
    // https://xtls.github.io/config/
        
    // 常用的config文件，不论服务器端还是客户端，都有5个部分。外加小小白解读：
    // ┌─ 1_log          日志设置 - 日志写什么，写哪里（出错时有据可查）
    // ├─ 2_dns          DNS-设置 - DNS怎么查（防DNS污染、防偷窥、避免国内外站匹配到国外服务器等）
    // ├─ 3_routing      分流设置 - 流量怎么分类处理（是否过滤广告、是否国内外分流）
    // ├─ 4_inbounds     入站设置 - 什么流量可以流入Xray
    // └─ 5_outbounds    出站设置 - 流出Xray的流量往哪里去
        
        
    {
        // 1_日志设置
        "log": {
            "loglevel": "warning",    // 内容从少到多: "none", "error", "warning", "info", "debug" 
            "access": "/var/log/xray/access.log",    // 访问记录
            "error": "/var/log/xray/error.log"    // 错误记录
        },
        
        // 2_DNS设置
        "dns": {
            "servers": [
                "https+local://1.1.1.1/dns-query",    // 首选1.1.1.1的DoH查询，牺牲速度但可防止ISP偷窥
                "localhost"
            ]
        },
        
        // 3_分流设置
        "routing": {
            "domainStrategy": "AsIs",
            "rules": [
                // 3.1 防止服务器本地流转问题：如内网被攻击或滥用、错误的本地回环等
                {
                    "type": "field",
                    "ip": [
                        "geoip:private"    // 分流条件：geoip文件内，名为"private"的规则（本地）
                    ],
                    "outboundTag": "block"    // 分流策略：交给出站"block"处理（黑洞屏蔽）
                },
                // 3.2 屏蔽广告
                {
                    "type": "field",
                    "domain": [
                        "geosite:category-ads-all"    // 分流条件：geosite文件内，名为"category-ads-all"的规则（各种广告域名）
                    ],
                    "outboundTag": "block"    // 分流策略：交给出站"block"处理（黑洞屏蔽）
                }
            ]
        },
    
        // 4_入站设置
    "inbounds": [
        {
            "port": 12345,
            "protocol": "shadowsocks",
            "settings": {
                "clients": [
                    {
                        "password": "密码",
                        "method": "chacha20-poly1305",
                        "level": 0,
                        "ivCheck": true
                    }
                ],
                "network": "tcp,udp"
            }
        }
    ],
    
        // 5_出站设置
        "outbounds": [
            // 5.1 第一个出站是默认规则，freedom就是对外直连（vps已经是外网，所以直连）
            {
                "tag": "direct",
                "protocol": "freedom"
            },
            // 5.2 屏蔽规则，blackhole协议就是把流量导入到黑洞里（屏蔽）
            {
                "tag": "block",
                "protocol": "blackhole"
            }
        ]
    }
    ```



## 三、启动Xray服务！！（并查看服务状态）
---

如果你是跟随本文一步步设置过来，其实就已经避开了最常见**日志文件权限不足**、**证书文件权限不足** 这两个大坑。那么现在运行`Xray`自然应该无比顺利。

1. 输入下面的命令，享受启动`Xray`的历史性时刻吧！！！
    ```
    $ sudo systemctl start xray
    ```

2. 仅仅`start`我们并不能确定是否成功的开启了Xray的服务，要确定它的状态，就要用到下面的命令。
    ```
    $ sudo systemctl status xray
    ```

    看到那个绿色的、令人愉悦的 `active (running)` 了吗？它就是说 `Xray` 已经在正确的运行了



## 四、回顾 `systemd` 进行基本的服务管理
---

到现在为止，我们已经使用过了`systemctl`相关的`start`, `status`, `reload` 等命令，这些都是基于`systmed`管理模块对Linux系统中各种服务进行管理的通用命令。现在正好熟悉一下相关的其他几个命令。

1. 若你需要暂时关闭 `Xray` 的服务，那就用`stop`命令
    ```
    $ sudo systemctl stop xray
    ```

2. 若你需要重启`Xray`的服务，那就用`restart`命令
    ```
    $ sudo systemctl restart xray
    ```

3. 若你需要禁用`Xray`的服务（电脑重启后禁止Xray自动运行），那就用`disable`命令
    ```
    $ sudo systemctl disable xray
    ```

4. 若你需要启用`Xray`的服务（电脑重启后确保Xray自动运行），那就用`enable`命令
    ```
    $ sudo systemctl enable xray
    ```

## 六、服务器优化一：防火墙增强安全性
---

我们使用`ufw`来管理Shadowsocks服务器的防火墙。

在基于Debian的服务器上，可以通过如下命令安装`ufw`：

```
sudo apt update && sudo apt install -y ufw
```

然后开放有关`ssh`和`Shadowsocks`的端口。 请注意，以下命令假设你在`/usr/local/etc/xray/config.json`中的`server_port`的值为`8388`。 如果你的`server_port`用了其他的值，请对以下命令作相应的修改：

```
sudo ufw allow ssh
sudo ufw allow 8388
```

现在我们启动`ufw`:

```
sudo ufw enable
```

启动时如果弹出`Command may disrupt existing ssh connections. Proceed with operation (y|n)?`，请输入`y`并按回车键。

最后，请用`sudo ufw status`检查一下你的配置是否和下面的一样：

```
Status: active

To                         Action      From
--                         ------      ----
22/tcp                     ALLOW       Anywhere
8388                       ALLOW       Anywhere
22/tcp (v6)                ALLOW       Anywhere (v6)
8388 (v6)                  ALLOW       Anywhere (v6)
```


-------------------

## 五、服务器优化二：开启BBR
---

1. 传说中的`BBR`
   
    我相信，你在搜索各种科学上网技术的时候，肯定不止一次的听过`bbr`这个东西，在各种博客添油加醋之下，让人觉得它神乎其神。更有`bbrplus`, `bbr2`, `魔改bbr` 等一大堆衍生品。仿佛神油一般，用了就能野鸡线路变专线。
    
    那么，这东西究竟是什么？它有没有用？又该用哪一个版本呢？

2. 实际的`BBR`

    **BBR** = **B**ottleneck **B**andwidth and **R**ound-trip propagation time，是一种TCP的**拥塞控制算法**。简单粗暴的理解就是**数据流量的交通管理**：当公路不再塞车的时候，每辆车自然就能保持较快的车速了。

    那么它有没有用呢？一般而言，`有BBR` 和 `没有BBR` 会有可以感知的差别（速度、稳定性、延迟方面都会有一些改善），所以 **【非常建议开启 `BBR`】**。
    
    但开启之后，`BBR` 在 `4.x` 和 `5.x` 之间的差异往往比较微妙、见仁见智，造成体验差别的决定性因素仍然是线路质量。所以 **【不必纠结版本、不必盲目追新、跟随你的发行版更新内核即可】** 

3. `bbrplus`, `bbr2`, `魔改bbr` 和其他各种听起来就酷炫的版本是不是更好？

    一句话：**不是！不要用这些！这些都为了吸引眼球乱起的名字！**

    `BBR` 的更新和发布，都是跟随Linux的内核（`Kernel`）进行的。换言之，只要你用的是比较新的内核，就自然会使用到新版`BBR`。

    而这些名字看起来很酷炫的东西，说白了就是仍未正式发布的、尚在测试阶段的内核及其对应的`BBR`版本。这些脚本也仅仅就是通过下载预览版的内核（甚至第三方魔改内核）来率先开启而已。

    内核的稳定是一台服务器稳定运行的基石。**【BBR测试版带来的细微性能差异绝对不值得更换不稳定的内核。】** 请选择你所在的Linux发行版所支持的最新内核，这样可以最大限度的保持服务器的长期稳定和兼容。



**注意：** 所谓魔改`bbr`的【领先】是有非常强的时效性的。比如很多 `bbrplus` 脚本，因为几年来都没有更新，到现在还会把你的内核换成 `4.19`，要知道现在稳定如 Debian 已经是 `5.9` 的时代了，那么这个脚本放在2018年1月也许领先了一点，到2018年10月4.19正发布时就已经失去了意义，放在现在甚至可以说是完完全全的【降级】和【劣化】



4. `fq`, `fq_codel`, `fq_pie`, `cake`和其他算法哪个好？

    一句话：**看不懂的话，请保持`fq`，足够、且不会劣化你的线路** 

5. 锐速、Finalspeed、LotServer和其他“加速工具”

    一句话：**不要用这些！把他们丢进历史的垃圾桶吧！** 

    它能解决的也只有丢包率的问题。不太准确的比喻，就是本来你用一辆车送你的货，有时候车半路就坏了（丢包），用了这些以后，你直接派出3份一样的货，让三辆车同时送，只要有一辆没坏就能送到。马路上都是你的车，自然就能把别人挤下去。但可想而知，你挤别人的时候，别人也会来挤你，而整个机房的出口道路一共就那么宽，最终势必就变成集体大堵车了。


**说明：** 它们的原理不是算法优化、不是提速、大多数是简单粗暴的**多倍发包**。对于【丢包率非常高】的差线路可能有一点作用，但【对丢包率低的好线路没有任何优化作用，反而会成倍的消耗你的流量】，进而造成服务器和你的邻居不必要的压力。

如果你的线路真的丢包率奇高，真正靠谱的解决方案是【换线路】。


6. 啰嗦了这么多，就是因为围绕 `BBR` 忽悠小白的错误概念和坑人脚本实在是太多了。我希望你们现在对 `BBR` 有了相对清晰的理解。接下来，我们就动手安装最新的Debian内核并开启`BBR` 吧！（真的很简单）
    1. 给 Debian 10 添加官方 `backports` 源，获取更新的软件库
        ```
        $ sudo nano /etc/apt/sources.list
        ```
    

**说明：** 本文以 Debian 10 为例，所以使用 `/etc/apt/sources.list` 仍无问题，但如果你并不是根据本文从头开始，或者使用了其他Linux发行版，那么建议你建立 `/etc/apt/sources.list.d/` 文件夹，并在这个文件夹内建立自己的配置文件，形如 `/etc/apt/sources.list.d/vpsadmin.list`，以此保证兼容性，也可避免默认文件在不可预见的情况下被覆盖而导致配置丢失。


​    
​    2. 然后把下面这一条加在最后，并保存退出。
​        ```
​        deb http://deb.debian.org/debian buster-backports main
​        ```
​        
​    3. 刷新软件库并查询 Debian 官方的最新版内核并安装。请务必安装你的VPS对应的版本（本文以比较常见的【amd64】为例）。
​        ```
​        $ sudo apt update && sudo apt -t buster-backports install linux-image-amd64
​        ```


**注意：** 如果你的VPS支持，可以尝试【云服务器专用内核】`linux-image-cloud-amd64`，优点就是精简、资源占用低，缺点嘛是有同学反馈不支持的系统强行安装会导致无法开机（Kernel无法识别）。

为了避免无法识别的悲剧，请确保：
- 尝试前做一个系统快照，或者
- 你有 `vnc` 可以救场（并且你知道怎么用）



    4. 修改 `kernel` 参数配置文件 `sysctl.conf` 并指定开启 `BBR`
        ```
        $ sudo nano /etc/sysctl.conf
        ```


**说明：** 本文以 Debian 10 为例，所以使用 `/etc/sysctl.conf` 仍无问题，但如果你并不是跟着本文从头开始，或者使用了其他Linux发行版，那么建议你建立 `/etc/sysctl.d/` 文件夹，并在这个文件夹内建立自己的配置文件，形如 `/etc/sysctl.d/vpsadmin.conf`，以此保证兼容性，因为部分发行版在 `systemd` 207 版本之后便不再从 `/etc/sysctl.conf` 读取参数。使用自定义配置文件也可避免默认文件在不可预见的情况下被覆盖而导致配置丢失。



    5. 把下面的内容添加进去
        ```
        net.core.default_qdisc=fq
        net.ipv4.tcp_congestion_control=bbr
        ```
    
    6. 重启VPS、使内核更新和`BBR`设置都生效
        ```
        $ sudo reboot
        ```




    8. 确认`BBR`开启
    
        如果你想确认 `BBR` 是否正确开启，可以使用下面的命令：
    
        ```
        $ lsmod | grep bbr
        ```
    
        此时应该返回这样的结果：
    
        ```
        tcp_bbr
        ```
    
        如果你想确认 `fq` 算法是否正确开启，可以使用下面的命令：
    
        ```
        $ lsmod | grep fq
        ```
    
        此时应该返回这样的结果：
    
        ```
        sch_fq
        ```




## 六、感谢
这篇文章改编自 [小小白话文][1]


[1]: https://xtls.github.io/document/level-0/ch07-xray-server.html

```111```