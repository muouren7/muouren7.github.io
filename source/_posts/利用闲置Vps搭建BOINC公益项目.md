---
title: 利用闲置Vps搭建BOINC公益项目
tags: [Vps,Boinc]
---

# 什么是Boinc ?

BOINC 是 Berkeley Open Infrastructure for Network Computing 的首字母缩写，即伯克利开放式网络计算平台，是用于志愿计算和网格计算的开放的中间件系统。

BOINC 最早是为了支持 SETI@home 项目而开发的，之后逐渐成了最为主流的分布式计算平台，为众多的数学、物理、化学、生命科学、地球科学等学科类别的项目所使用。BOINC 的开发目的是为了帮助研究人员更方便地获得分布在世界各地的志愿者的计算资源。

![Logo](https://yinli.me/wp-content/uploads/2020/03/2020032614262345-1024x428.png)

# 云服务器安装BOINC客户端

​	使用 xshell 连接服务器（也可以使用Putty） 执行安装命令

```shell
yum install boinc-client
```

​	或者

```shell
apt-get install boinc-client
```

# 配置远程管理

​	安装完成后开始配置远程管理，方便我们在电脑本地连接服务器查看项目进度 在root目录下新建文件 `cc_config.xml`

```shell
vi /root/cc_config.xml 
```

​	按 i 进入编辑模式，输入以下内容后按 esc 退出，按： 输入wq 保存文本

```xml
<cc_config>
   <options>
      <ncpus>-1</ncpus>
         <allow_remote_gui_rpc>1</allow_remote_gui_rpc>
   </options>
</cc_config>
```

​	新建 `gui_rpc_auth.cfg` 文件，设置远程管理密码

​	设置密码，内容为空则密码为空，保存。

```
123456
```

​	开放TCP31416端口（腾讯云或阿里云直接在安全组上将此端口开放）

测试运行 BIONC 项目

```shell
boinc
```

执行后会有一堆运行信息。如果显示有“Config: GUI RPC allowed from any host”，那么就应该配置成功了。 ctrl + c 退出 ，输入reboot重启服务器。

以后运行的时候用命令（现在先执行一遍，服务器重启后需要重新执行）

```shell
boinc --daemon
```

# 本地安装BOINC客户端

​	[下载地址](https://boinc.berkeley.edu/download.php)：https://boinc.berkeley.edu/download.php

然后打开运行 BOINC Manager ，切换到高级视图

![高级视图](https://www.yinli.me/wp-content/uploads/2020/03/2020032615184484.png)

点击菜单的文件－选择计算机 输入服务器 IP ，然后输入刚才设置的密码，等待连接

![输入](https://yinli.me/wp-content/uploads/2020/03/2020032615204230.png)

连接成功后，就可以选择要参加的项目

# 建议

​	强烈建议修改计算机参数设置，cpu 占用不要太高，不然有可能被 vps 厂商封号

![修改cpu限制](https://www.yinli.me/wp-content/uploads/2020/03/2020032615274714.png)





















