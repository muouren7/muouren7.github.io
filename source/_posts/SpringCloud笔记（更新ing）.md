---
title: SpringCloud笔记（更新ing）
tags: [笔记,SpringCloud]
---

# SpringCloud

Eureka	-	用于服务的注册于发现，对每个服务进行集中管理

~~Ribbon~~（已经凉了，现已被LoadBalancer取代）	-	为服务之间相互调用提供负载均衡算法

Hystrix	-	断路器，系统出现故障时防止故障进一步扩张

~~Zuul~~	(已凉，现被Gateway取代)	-	api网关，决定将请求转发给哪一个微服务。

Config	-	配置管理，可以实现配置文件的集中管理	

[SpringCloud官网](https://spring.io/projects/spring-cloud)

## Eureka注册中心

[Eureka官网文档](https://docs.spring.io/spring-cloud-netflix/docs/current/reference/html)



## LoadBalancer负载均衡

为服务之间的调用提供负载均衡算法