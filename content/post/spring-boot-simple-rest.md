---
title: "Spring Boot 简单的Rest服务创建"
date: 2020-03-21T12:53:04+08:00
draft: true
---

## 生成Spring Boot项目


### 如何生成一个Spring Boot项目呢？

有以下两种办法：
- 第一种是去[Spring Initializr](https://start.spring.io/)
- 第二种是在idea中生成（针对的是收费版）

我着重讲解一下第一种方法,在进入Spring Initializr之后，需要填写以下信息：
- Project，使用Maven还是Gradle进行构建，这里我选择Maven
- Language, Spring Boot支持三种语言(Java,Kotlin和Groovy),这里我们选择Java
- Spring Boot 版本号，我们选择默认的就好
- Project Metadata
    - Group, 这个就是填写你Maven中的组信息
    - Artifact,填写你的服务名
- Dependencies, 构建你的服务的时候你所依赖的包，这里我们只需要Spring Web就ok了。

![image1](/spring-boot-simple-rest-image-1.png)

## 构建你的第一个RestAPI

在下载下来你的Spring Boot项目之后，将其解压，选择你喜欢的idea将它打开，如果以上信息你都是按照我的来填写的话，你会看见一个类，名字叫做**SimpleRestServiceApplication.java**,这个是Spring Boot的启动类。
```java
@SpringBootApplication
public class SimpleRestServiceApplication {

	public static void main(String[] args) {
		SpringApplication.run(SimpleRestServiceApplication.class, args);
	}

}
```

接下来，让我们来构建我们第一个RestFul API，分为以下几个步骤：
- 创建一个名叫FirstController.java的类，放在controller这个包底下
- 将@RestController注解到类上。
- 声明一个叫做hello，返回值为String的方法，并用@GetMapping("/hello")注解它。
- OK，大功告成，接下来，我们进行一下测试

```java
package com.xuzhang.simplerestservice.controller;

import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RestController;

@RestController
public class FirstController {
    @GetMapping("/hello")
    public String hello() {
        return "hello";
    }
}
```

## 利用PostMan测试

- 在idea中启动刚刚的项目
- 在postman中地址栏中输入localhost:8080/hello
- 如果你看见返回的hello，你就成功了

![image1](/spring-boot-simple-rest-image-2.png)

## 总结
到了这里，已经顺利地完成了非常简单的Spring Boot Rest服务的创建，在这里，简单总结下步骤：

- 进入[Spring Initializr](https://start.spring.io/)下载Spring Booot模版项目。
- 加入你写的RestController。
- 启动并利用postman进行测试

[代码仓库地址](https://github.com/xuzhang0636/spring-boot-simple-rest)