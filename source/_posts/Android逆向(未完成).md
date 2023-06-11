---
title: Android逆向(未完成)
tags: [Android,逆向]
---

[原文链接《安卓逆向这档事》一、模拟器环境搭建 - 『移动安全区』 - 吾爱破解 - LCG - LSG |安卓破解|病毒分析|www.52pojie.cn](https://www.52pojie.cn/thread-1695141-1-1.html)

## 环境搭建

准备：

- 雷电模拟器9.0
- [Magisk面具模块](https://yzhqvq.lanzoux.com/b02vfzqmh)：密码：52pj
- [LSPosed模块](https://[github](http://search.bilibili.com/all?from_source=webcommentline_search&keyword=github).com/LSPosed/LSPosed/releases/download/v1.8.6/LSPosed-v1.8.6-6712-zygisk-release.zip)

首先下载安装雷电模拟器，在设置中勾选**System.vmdk**可写入，然后把模拟器的Root权限打开

<img src="https://muouren7hexo-1305003515.cos.ap-chengdu.myqcloud.com/HexoPic/202304271559460.png"  />

![](https://muouren7hexo-1305003515.cos.ap-chengdu.myqcloud.com/HexoPic/202304271600506.png)

然后安装Magisk模块，直接拖进模拟器双击即可。

![](https://muouren7hexo-1305003515.cos.ap-chengdu.myqcloud.com/HexoPic/202304271603942.png)

![](https://muouren7hexo-1305003515.cos.ap-chengdu.myqcloud.com/HexoPic/202304271604743.png)

需要注意的是，在进入到以下界面后暂时先不要动。先把Magisk关闭再重启。

![](C:\Users\HP\AppData\Roaming\Typora\typora-user-images\image-20230427160605891.png)

然后重新进入安装界面。把两项都选上。

![](C:\Users\HP\AppData\Roaming\Typora\typora-user-images\image-20230427160733807.png)



方式选择**系统分区**

![](https://muouren7hexo-1305003515.cos.ap-chengdu.myqcloud.com/HexoPic/202304271607654.png)

出现了以下信息就说明Magisk安装成功了。成功后需要再次重启模拟器。

![](https://muouren7hexo-1305003515.cos.ap-chengdu.myqcloud.com/HexoPic/202304271609651.png)



重启后进入到Magisk，在设置中把以下这一项勾选上。

![](https://muouren7hexo-1305003515.cos.ap-chengdu.myqcloud.com/HexoPic/202304271610086.png)

然后准备安装LSPosed模块，在模拟器旁边有一个共享文件的选项，选择，将真机中下载好的LSPosed模拟导入到模拟器中（默认导入到模拟器的Picture文件夹下）。

然后进入Magisk，在模块一项中选择安装LSPosed模块，安装完成后选择重启

如果能看见以下信息，就说明我们的环境搭建完成了。

![](https://muouren7hexo-1305003515.cos.ap-chengdu.myqcloud.com/HexoPic/202304271613345.png)

## Apk文件结构、双开、汉化

### apk结构

apk 全称 Android Package，它相当于一个压缩文件，只要在电脑上将apk后缀改为zip即可解压。

| 文件                    | 注释                                                         |
| ----------------------- | ------------------------------------------------------------ |
| assets目录              | 存放APK的静态资源文件，比如视频，音频，图片等                |
| lib 目录                | armeabi-v7a基本通用所有android设备，arm64-v8a只适用于64位的android设备，x86常见用于android模拟器，其目录下的.so文件是c或c++编译的动态链接库文件 |
| META-INF目录            | 保存应用的签名信息，签名信息可以验证APK文件的完整性，相当于APK的身份证(验证文件是否又被修改) |
| res目录                 | res目录存放资源文件，包括图片，字符串等等，APK的脸蛋由他的layout文件设计 |
| AndroidMainfest.xml文件 | APK的应用清单信息，它描述了应用的名字，版本，权限，引用的库文件等等信息 |
| classes.dex文件         | classes.dex是java源码编译后生成的java字节码文件，APK运行的主要逻辑 |
| resources.arsc文件      | resources.arsc是编译后的二进制资源文件，它是一个映射表，映射着资源和id，通过R文件中的id就可以找到对应的资源 |























