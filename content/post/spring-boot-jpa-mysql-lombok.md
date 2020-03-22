---
title: "Spring Boot, JPA, Mysql, Lombok整合"
date: 2020-03-22T16:08:21+08:00
draft: true
---

## 创建Spring Boot项目，并添加相应的依赖

创建该项目分为如下几步：

- 进入[Spring Initializr](https://start.spring.io/),填写相应信息，具体怎么填写，可以参照我的[上一篇博文](../spring-boot-simple-rest)
- 添加相应的Dependencies
    - Spring Web
    - Lombok
    - MySQL Driver
    - Spring Data JPA
- 点击Generate下载

![image](/spring-boot-jpa-mysql-lombok-1.png)


## 利用Docker创建MySQL数据库

### 如何安装Docker

关于如何安装Docker，我这里就不详述了，参考Docker官网的[安装教程](https://docs.docker.com/install/)

### 在Docker中创建MySQL数据库
步骤如下：

- docker pull mysql:latest
- 利用docker images | grep mysql查看镜像是否下载成功
![image](/spring-boot-mybatis-mysql-lombok-2.png)
- 启动mysql
```shell
$ docker run -itd --name mysql -p 3306:3306 -e MYSQL_ROOT_PASSWORD=[your password] mysql
```

## 配置JPA相关的依赖(注解版本)
- 第一步，进入application.properties配置数据源，配置信息如下
```
# mysql url
spring.datasource.url=jdbc:mysql://localhost:3306/robert 
# mysql username
spring.datasource.username=root
# mysql password
spring.datasource.password=robert
# mysql Driver
spring.datasource.driver-class-name=com.mysql.cj.jdbc.Driver
```
- 第二步，创建User.java类,放入model包中,这里，利用了Lombok注解，我随后会写一篇文章对这个插件进行详细的描述
```java
@Data
@AllArgsConstructor
@NoArgsConstructor
@Entity
@Table(name = "users")
public class User {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;
    @Column
    private String username;
    @Column
    private String password;
    @Column
    private String phone;
}
```
- 第三步，创建UserRepository,放入repository包中
```java
public interface UserRepository extends JpaRepository<User, Long> {
}
```

## 测试

编写测试类来测试代码的正确性
```java
@SpringBootTest
public class TestUserRepository {
    @Autowired
    private UserRepository userRepository;

    @Test
    public void smokeTest() {
        Assertions.assertNotNull(userRepository);
    }

    @Test
    public void insertTest() {
        User user = new User();
        user.setPassword("robert");
        user.setPhone("134xxxxxxxx");
        user.setUsername("robert");
        userRepository.save(user);

    }

    @Test
    public void selectTest() {
        Assertions.assertEquals(2, userRepository.findAll().size());
    }
}}
```

## 总结
OK，到了这里，我们对SpringBoot,Mybatis,Lombok,Mysql的整合已经完成，代码奉上，[代码地址](https://github.com/xuzhang0636/spring-boot-jpa-mysql-lombok)