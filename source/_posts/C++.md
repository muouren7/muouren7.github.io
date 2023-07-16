---
title: C++
tag: [C++,gcc]
---









### GCC命令

```powershell
gcc -fexec-charset=GBK -I head test.c func.c newfunc.c -o aaa
```

**-fexec-charset=GBK** 指定输出编译后的二进制文件编码格式，GBK和windows系统默认的编码格式保持一致，不带参数默认是utf-8，会乱码，

**-I** 指定头文件所在的文件夹

**-o** 指定链接后的可执行文件
