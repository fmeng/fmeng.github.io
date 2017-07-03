---
layout: post
title: "Spring 复习"
categories:
- java
---

1. Inversion of Control / Dependency Injection

依赖注入事实现反转控制的一种方式
由使用new Service 到 从IOC容器中获得服务的转变

2. 服务注入容器的方式
    1. 构造器注入
    2. Set方法注入
    3. 接口注入（Spring入侵业务）
3. Spring注入对象的注解的区别
    1. @resource //JSR-250
        1.by name
        2.by type
        3.by qualifier
    2. @autowired //Spring framework<br/>
       @inject //JSR-330
        1.by type
        2.by qualifier
        3.by name
   



