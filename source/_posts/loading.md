---
title: java类加载
date: 2018-09-17 16:23:44
tags: java
categories: java
---
## 类加载过程
    1. 加载（loading）
    2. 链接（linking）
        2.1. 验证（Verification）
        2.2. 准备（Preparation）
        2.3 解析（Resolution）
    3. 初始化（initialization）
## 双亲委派
    - 目的 ：避免重复加载java类型
## java 8 以前的类加载器
    1.启动类加载器（BootStrap Class-Loader）,加载jre/lib,如rt.jar
    2.扩展类加载器（Extension or Ext Class-Loader）/jre/lib/ext（java9重命名为平台类加载器Platform Class-Loader）
    3.应用类加载器（Application or App Class-Loader）
## 自定义类加载过程
    1. 通过指定名称，并加载二进制
    2. 创建class对象，二进制到class的转化
## 备注
    - 当一个类或一个资源文件存在多个jar中，就会出现jar hell问题，解决办法就是自己加载手动通过cassloader加载类