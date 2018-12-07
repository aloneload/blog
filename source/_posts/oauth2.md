---
title: oauth2
date: 2018-11-29 09:26:45
tags:
---
## 备注

1. /oauth/token 密码授权请求access_token
2. /oauth/token_key 获取public_key 用于验证jwt的无篡改性
3. 修改不支持swagger-ui ,暂时关闭csrf
    ```
    http.csrf().disable();
    ```
4. 解决Jhipster创建实体后，Gateway数据菜单为空
    微服务创建完成后，打开网关服务，执行`yo jhipster:entity NAME` 选择`
Do you want to generate this entity from an existing microservice? Y`