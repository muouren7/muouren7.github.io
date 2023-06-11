---
title: Vue-cli本地项目搭建https环境
tags: [Vue,https]
---

[让Vue CLI支持HTTPS_口袋の的天空的博客-CSDN博客](https://blog.csdn.net/qq_39928481/article/details/118896033)【搬运的】

## 1.打开git黑窗口

命令行定位到这个目录，然后敲3个命令，其中第二个命令会问你N个问题，不用回答，一律回车即可：

```shell
openssl genrsa -out privatekey.pem 1024
openssl req -new -key privatekey.pem -out certrequest.csr
openssl x509 -req -in certrequest.csr -signkey privatekey.pem -out certificate.pem
--- 然后build文件夹下就会增加这个cert文件夹，cert文件夹会有3个文件
```

![](https://img-blog.csdnimg.cn/20210719145322995.png)

## 2.打开webpack.dev.conf.js文件，配置vue.config.js

![](https://img-blog.csdnimg.cn/20210719145445398.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM5OTI4NDgx,size_16,color_FFFFFF,t_70)

## 1)添加https和fs

![](https://img-blog.csdnimg.cn/20210719145638211.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM5OTI4NDgx,size_16,color_FFFFFF,t_70)

## 2)添加https配置

![](https://img-blog.csdnimg.cn/20210719145659216.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM5OTI4NDgx,size_16,color_FFFFFF,t_70)

```shell
/*---------添加以下2句，引入https和fs---------*/
const https = require('https')
const fs = require('fs')
/*---------添加以上2句，引入https和fs---------*/
```

```shell
 /*---------添加https配置---------*/
    https: {
      key: fs.readFileSync(path.join(__dirname, './cert/privatekey.pem')),
      cert: fs.readFileSync(path.join(__dirname, './cert/certificate.pem'))
    }
    /*---------添加https配置---------*/
————————————————
```

### 注

vue3项目中,devServer配置在**vue-config.js**文件中，同时还要引入**path**模块。

