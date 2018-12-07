---
title: jhipster初探
date: 2018-07-03 10:35:42
tags:   编程语言
        jhipster
categories: 编程语言
---
### 安装
#### 本地生成

[官网介绍](https://www.jhipster.tech/)

- 先决：安装 Java， Git， Node.js， Yarn和 Yeoman
1. 安装JHipster `yarn global add generator-jhipster`
1. 创建一个新目录并进入它 `mkdir myApp && cd myApp`
1. 运行JHipster并按照屏幕上的说明操作 `jhipster`
1. 具体参数见下图（生成单体应用）：
    ![](jhipster1.png) 

    生成spring boot + augular5的单体架构应用
1. 等下载完成后输入`mvnw(linux下为./mvnw)`即可在8080提供服务
    ![](jhipster2.png)
1. 自带一些监控运维相关应用
    ![](jhipster3.png)
1. 下载[jdl-studio](https://start.jhipster.tech/jdl-studio/)
1. 生成代码`jhipster import-jdl ./my-jdl-file.jdl --json-only`
#### 在线生成
在线生成的好处就是不需要本次安装那么多软件  [地址](https://start.jhipster.tech/#/generate-application)
    [](!jhipster4.png)
#### 微服务
##### Consul 服务发现与注册

    jhipster 微服务注册与发现可以采用consul,网关代理可以采用Traefik
![](consul.png)
![](consul2.png)
![](consul3.png)

##### jhipster 服务发现与注册
    
    1. 创建顺序
        1. JHipster UAA server
        1. 一个微服务
        1. 一个 JHipster 网关
        1. `./mvnw verify -Pprod dockerfile:build dockerfile:tag@version dockerfile:tag@commit`生成各个服务的docker镜像
        1. 生成docker-compose管理各个服务

![](uaa.png)
![](app2.png)
![](getway.png)
![](docker-compose.png)
     

####备注
```
    dto A, B with mapstruct
    paginate A, C with infinite-scroll
    paginate Job with pagination
    paginate B with pager
    service A with serviceClass
    service C with serviceImpl
```
