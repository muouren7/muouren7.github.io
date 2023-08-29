---
title: Pandro本地搭建chatgpt
tags: [教程]
---



1、下载并安装Docker [Docker官网](https://www.docker.com)

2、使用潘多拉 (Pandora) [pengzhile/pandora](https://github.com/pengzhile/pandora)

```shell
docker pull pengzhile/pandora
```

```shell
docker run  -e PANDORA_CLOUD=cloud -e PANDORA_SERVER=0.0.0.0:8899 -p 8899:8899 -d pengzhile/pandora
```

