# X for Okteto

* * *

# 目录

- [项目特点](README.md#项目特点)
- [部署](README.md#部署)
- [Argo Json 的获取](README.md#argo-json-的获取)
- [Argo Token 的获取](README.md#argo-token-的获取)
- [TTYD webssh 的部署](README.md#ttyd-webssh-的部署)
- [鸣谢下列作者的文章和项目](README.md#鸣谢下列作者的文章和项目)
- [免责声明](README.md#免责声明)

* * *

## 项目特点:
* 使用 CloudFlare 的 Argo 隧道，同时兼容 Json / token / 临时 三种方式认证，使用TLS加密通信，可以将应用程序流量安全地传输到Cloudflare网络，提高了应用程序的安全性和可靠性。此外，Argo Tunnel也可以防止IP泄露和DDoS攻击等网络威胁
* 解锁 chatGPT
* 在浏览器查看系统各项信息，方便直观
* 集成哪吒探针，可以自由选择是否安装，支持 SSL/TLS 模式，适配 Nezha over Argo 项目: https://github.com/fscarmen2/Argo-Nezha-Service-Container
* uuid，WS 路径既可以自定义，又或者使用默认值
* 前端 js 定时和 pm2 配合保活，务求让恢复时间减到最小
* 节点信息以 V2rayN / Clash / 小火箭 链接方式输出
* 可以使用浏览器访问，使用 ttyd，ssh over http2
* 项目路径 `https://github.com/fscarmen2/X-for-Okteto`

## 部署:
* 注册 [Okteto](https://www.okteto.com/) ，需要绑定 Github 账户，同时 Github 也要多加个企业邮箱，可以使用 CF 的域名邮箱，具体方法请谷歌

* PaaS 平台设置的环境变量
  | 变量名        | 是否必须 | 默认值 | 备注 |
  | ------------ | ------ | ------ | ------ |
  | UUID         | 否 | de04add9-5c68-8bab-950c-08cd5320df18 | 可在线生成 https://www.zxgj.cn/g/uuid |
  | WSPATH       | 否 | argo | 勿以 / 开头，各协议路径为 `/WSPATH-协议`，如 `/argo-vless`,`/argo-vmess`,`/argo-trojan`,`/argo-shadowsocks` |
  | NEZHA_SERVER | 否 |        | 哪吒探针与面板服务端数据通信的IP或域名 |
  | NEZHA_PORT   | 否 |        | 哪吒探针服务端的端口 |
  | NEZHA_KEY    | 否 |        | 哪吒探针客户端专用 Key |
  | NEZHA_TLS    | 否 |        | 哪吒探针是否启用 SSL/TLS 加密 ，如不启用不要该变量，如要启用填"1" |
  | ARGO_AUTH    | 否 |        | Argo 的 Token 或者 json 值 |
  | ARGO_DOMAIN  | 否 |        | Argo 的域名，须与 ARGO_DOMAIN 必需一起填了才能生效 |
  | WEB_USERNAME | 否 | admin  | 网页和 webssh 的用户名 |
  | WEB_PASSWORD | 否 | password | 网页和 webssh 的密码 |
  | SSH_DOMAIN   | 否 |        | webssh 的域名，用户名和密码就是 <WEB_USERNAME> 和 <WEB_PASSWORD> |

* 路径（path）
  | 命令 | 说明 |
  | ---- |------ |
  | <URL>/list | 查看节点数据 |
  | <URL>/status | 查看后台进程 |
  | <URL>/listen | 查看后台监听端口 |

<img width="1377" alt="image" src="https://user-images.githubusercontent.com/62703343/234729789-0246a726-78c9-47e6-bc2f-68203e33c33d.png">

<img width="902" alt="image" src="https://user-images.githubusercontent.com/62703343/234730676-d780c3d2-92fd-4b3e-b250-b25ce8568476.png">

<img width="660" alt="image" src="https://user-images.githubusercontent.com/62703343/234731674-a881d94d-fbe9-4d33-8e79-148d8095b757.png">

<img width="1364" alt="image" src="https://user-images.githubusercontent.com/62703343/234732553-d16b1de0-fe27-4db8-90ae-48248f7e4c38.png">

<img width="747" alt="image" src="https://user-images.githubusercontent.com/62703343/234732747-41c7842e-e16a-459e-804e-bbb065fd70e4.png">

## Argo Json 的获取

用户可以通过 Cloudflare Json 生成网轻松获取: https://fscarmen.cloudflare.now.cc

<img width="842" alt="image" src="https://user-images.githubusercontent.com/62703343/234733074-397bad30-266b-4719-898a-a760a3f0331a.png">

如想手动，可以参考，以 Debian 为例，需要用到的命令，[Deron Cheng - CloudFlare Argo Tunnel 试用](https://zhengweidong.com/try-cloudflare-argo-tunnel)


## Argo Token 的获取

详细教程: [群晖套件：Cloudflare Tunnel 内网穿透中文教程 支持DSM6、7](https://imnks.com/5984.html)

<img width="1409" alt="image" src="https://user-images.githubusercontent.com/92626977/218253461-c079cddd-3f4c-4278-a109-95229f1eb299.png">

<img width="1619" alt="image" src="https://user-images.githubusercontent.com/92626977/218253838-aa73b63d-1e8a-430e-b601-0b88730d03b0.png">

<img width="1155" alt="image" src="https://user-images.githubusercontent.com/92626977/218253971-60f11bbf-9de9-4082-9e46-12cd2aad79a1.png">


## TTYD webssh 的部署

* 原理
```
+---------+     argo     +---------+     http     +--------+    ssh    +-----------+
| browser | <==========> | CF edge | <==========> |  ttyd  | <=======> | ssh server|
+---------+     argo     +---------+   websocket  +--------+    ssh    +-----------+
```

* 只能使用 Json 方式建的隧道，不能使用 Token

<img width="1643" alt="image" src="https://user-images.githubusercontent.com/92626977/235453084-a8c55417-18b4-4a47-9eef-ee3053564bff.png">

<img width="1347" alt="image" src="https://user-images.githubusercontent.com/92626977/235453394-2d8fd1e9-02d0-4fa6-8c20-dda903fd06ae.png">

<img width="983" alt="image" src="https://user-images.githubusercontent.com/92626977/235453962-1001bcb8-e21d-4c1b-9b8f-6161706f5ccd.png">

<img width="1540" alt="image" src="https://user-images.githubusercontent.com/92626977/235454653-3ac83b16-b6f4-477b-bccf-2cce8bcfbabe.png">


## 鸣谢下列作者的文章和项目:
大佬 Nike Jeff 的 trojan 项目，https://github.com/hrzyang/glitch-trojan ，在此基础上作修改。

## 免责声明:
* 本程序仅供学习了解, 非盈利目的，请于下载后 24 小时内删除, 不得用作任何商业用途, 文字、数据及图片均有所属版权, 如转载须注明来源。
* 使用本程序必循遵守部署免责声明。使用本程序必循遵守部署服务器所在地、所在国家和用户所在国家的法律法规, 程序作者不对使用者任何不当行为负责。
