# Cloudflare Free Argo tunnel 白嫖

## 文前闲扯
![](https://fg666.coding.net/p/blog/d/pic1/git/raw/master/f30a3877dd573a345dd0911a6c76e28351eb9ff2bc6983d75cc380e9847d5aa7.jpg)

这东西是蘑菇群里某企业级前端大佬186教短命的然后又教会了我。

## 介绍
&gt;Argo隧道提供了一种简便的方法，可将Web服务器安全地公开到Internet，而无需打开防火墙端口和配置ACL。 Argo隧道还可以确保请求在到达网络服务器之前先通过Cloudflare进行路由，因此可以确保通过Cloudflare的WAF和Unmetered DDoS缓解功能停止了攻击流量，并且如果为帐户启用了这些功能，则可以通过Access进行身份验证。
&gt;–翻译自 Argo Tunnel -- Cloudflare Docs

## 安装
[点击这里安装](https://developers.cloudflare.com/cloudflare-one/connections/connect-apps/install-and-setup)
## 登陆
1.linux与macos可直接用`cloudflared login`登录，
windows&#39;打开cmd，将文件拖入窗口在加login登录，
会自动打开浏览器登录cloudflare，用ssh登录的linux可以复制连接到电脑浏览器登录。
2.然后选择你要使用的域名。
## 运行
运行以下命令（在cmd里运行）
`cloudflared tunnel --hostname &lt;tunnelDomain&gt; --url localhost:端口`

到这里就完成了，如果你的域名ns在cloudflare的话，cloudflare会帮你解析好记录；如果是cname接入的话，要自己去添加记录。
快去做些~~有意思~~的事情吧。

---

> 作者:   
> URL: /posts/cloudflare-argo-tunnel/  

