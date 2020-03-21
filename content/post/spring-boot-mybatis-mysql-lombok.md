---
title: "Spring Boot, Mybatis, Mysql, Lombok整合"
date: 2020-03-21T18:44:11+08:00
draft: true
---

## 创建Spring Boot项目，并添加相应的依赖

创建该项目分为如下几步：

- 进入[Spring Initializr](https://start.spring.io/),填写相应信息，具体怎么填写，可以参照我的[上一篇博文](../spring-boot-simple-rest)
- 添加相应的Dependencies
    - Spring Web
    - Lombok
    - MySQL Driver
    - MyBatis Framework
- 点击Generate下载

![image](/spring-boot-mybatis-mysql-lombok-1.png)


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

## 配置Mybatis相关的依赖(注解版本)
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
public class User {
    private Long id;
    private String username;
    private String password;
    private String phone;
}
```
- 第三步，创建UserMapper,放入mapper包中
```java
public interface UserMapper {
    @Insert({"insert into users(username,password,phone) values(#{user.username},#{user.password},#{user.phone})"})
    int insertUser(@Param("user") User user);

    @Select({"select * from users where id = #{id}"})
    User selectUserByUserId(long id);
}
```
- 第四步，在你的SpringBoot启动类中添加扫描你的Mapper的注解@MapperScan
```java
@SpringBootApplication
@MapperScan("com.xuzhang.springbootmybatismysqllombok.mapper")
public class SpringBootMybatisMysqlLombokApplication {

    public static void main(String[] args) {
        SpringApplication.run(SpringBootMybatisMysqlLombokApplication.class, args);
    }

}
```

## 测试

编写测试类来测试代码的正确性
```java
@SpringBootTest
public class TestUserMapper {
    @Autowired
    private UserMapper userMapper;

    @Test
    public void smokeTest() {
        Assertions.assertNotNull(userMapper);
    }

    @Test
    public void testInsert() {
        User user = new User();
        user.setPassword("robert");
        user.setUsername("robert");
        user.setPhone("134xxxxxxxx");
        Assertions.assertEquals(1, userMapper.insertUser(user));
    }

    @Test
    public void testSelect() {
        Assertions.assertNotNull(userMapper.selectUserByUserId(1L));
    }

}
```

## 总结
OK，到了这里，我们对SpringBoot,Mybatis,Lombok,Mysql的整合已经完成，代码奉上，[代码地址](https://github.com/xuzhang0636/springboot-mybatis-lombok-mysql)