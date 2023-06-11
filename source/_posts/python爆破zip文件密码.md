---
title: python爆破zip文件密码
tags: [python,zip,爆破]
---

# 准备

## 环境

python 版本 3.11.0

第三方库：zipfile

安装:

```shell
pip install zipfile36
```

## 密码生成

这里的word表示密码只有数字

可以在后面加入大小写字母或其他字符以进行遍历

```python
import itertools

words = "0123456789"
### 生成指定长度的密码集合
def CreatePwd(length):
    r = itertools.product(words,repeat=length)
    return r
```

## 文件读取

*zip文件与python代码保存在同一级目录下

```python
import zipfile

def uncompress(filename,password):
    try:
        with zipfile.ZipFile(filename) as z:
            z.extractall("./",pwd=password.encode('utf-8'))
            z.close()
            print("成功")
        return True
    except BaseException as e:
        #print(str(e))
        return False
```

## 主逻辑

这里的**5**，**7**表示密码的长度只在五位与六位数中进行爆破，可以改为其他长度范围

```python
for i in range(5,7):
    r = CreatePwd(i)
    jishuqi = 0
    for j in r:
        if jishuqi == 500:
            os.system("cls")
            jishuqi = 0
            #time.sleep(5)
        jishuqi = jishuqi + 1
        if not uncompress("test.zip","".join(j)):
            print("密码不是："+"".join(j))
        else:
            print("密码是："+"".join(j))
            exit(0)
```

## 完整代码

```python
import zipfile
import itertools
import os

words = "0123456789"



### 生成指定长度的密码集合
def CreatePwd(length):
    r = itertools.product(words,repeat=length)
    return r

def uncompress(filename,password):
    try:
        with zipfile.ZipFile(filename) as z:
            z.extractall("./",pwd=password.encode('utf-8'))
            z.close()
            print("成功")
        return True
    except BaseException as e:
        #print(str(e))
        return False

for i in range(5,7):
    r = CreatePwd(i)
    jishuqi = 0
    for j in r:
        if jishuqi == 500:
            os.system("cls")
            jishuqi = 0
            #time.sleep(5)
        jishuqi = jishuqi + 1
        if not uncompress("test.zip","".join(j)):
            print("密码不是："+"".join(j))
        else:
            print("密码是："+"".join(j))
            exit(0)

```

## 测试

这里设置zip文件的密码为64887

![密码](https://muouren7hexo-1305003515.cos.ap-chengdu.myqcloud.com/HexoPic/202301282235627.png)

将密码的范围设置为4-8(实际上代码执行时的密码范围是4-7)

![修改代码](https://muouren7hexo-1305003515.cos.ap-chengdu.myqcloud.com/HexoPic/202301282237280.png)

然后执行，时间长度按密码的长度增加而增加，即代码中word的字符越多，穷举的时间呈指数上升。

**可以根据猜测在word变量中添加密码可能存在的字符**

![执行结果](https://muouren7hexo-1305003515.cos.ap-chengdu.myqcloud.com/HexoPic/202301292207558.png)

