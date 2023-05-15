---
title: SpringCloud不读取boostrap.yml的解决办法
tags: 
  - 技术
  - 学习笔记
  - Java
  - springboot
category: java
date: 2023-05-15 13:32:47
---

SpringCloud **2.4版本** 之后不再优先读取 **bootstrap** 文件，我们只需要加入下面的坐标即可


```xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-bootstrap</artifactId>
</dependency>
```
