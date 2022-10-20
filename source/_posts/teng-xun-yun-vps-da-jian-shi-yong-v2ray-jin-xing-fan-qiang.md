---
title: '腾讯云vps搭建（使用V2ray进行翻墙）'
date: 2022-10-14 16:04:03
tags: [v2ray,vpn,翻墙,vps]
published: true
hideInList: false
feature: 
isTop: false
---
## 1、准备工作
购买一台海外线路的虚拟服务器，轻量级即可、V2ray客户端

## 2、开始搭建
购买好vps后，选择安装Debian系统（以debian为例），使用ssh进入到终端，（腾讯云自带）。注：腾讯云的vps需要在防火墙中设置端口的放行，不然搭建好了也连不上外网。

进入终端后先执行以下命令安装curl

```shell
apt-get update -y && apt-get install curl -y
```
再执行以下命令安装V2ray
```shell
bash <(curl -s -L https://git.io/v2ray.sh)	#V2ray一键安装脚本
```
安装好后根据提示配置，（基本上都是默认）

安装好后再输入以下命令获取连接地址，将获取的地址进行复制

```shell
V2ray -url		##记不清了，大概是这个
```
回到V2ray桌面端，注意:V2ray客户端一定要包含v2ray核心

把复制的地址加入到V2ray客户端即可。