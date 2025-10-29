# Dubbo 笔记

作者：YanShije

---

## 一、Dubbo 介绍

**Dubbo** 是阿里巴巴开源的一款 **高性能、轻量级的 Java RPC（远程过程调用）框架**，用于构建分布式服务架构。它通过高效的远程通信机制，实现了服务之间的调用与治理，是微服务架构早期的重要技术之一。

Dubbo 的核心目标是解决 **服务之间的调用、注册、发现、负载均衡与容错** 等问题，使得分布式系统中的各个模块能够像本地方法一样进行远程调用。

## 二、SpingBoot 原生方式

### 1、示例

```java
@Service
public class OrderService {

    @Resource
    private RestTemplate restTemplate;

    public String getOrder() {
        return "订单服务" + restTemplate.getForObject("http://localhost:8081/user", String.class);
    }

}
```

```java
@Service
public class UserService {

    public String getUser() {
        return "BananaPeach";
    }

}
```

### 2、痛点

| 维度     | 痛点                           |
| -------- | ------------------------------ |
| 性能     | 序列化慢、延迟高               |
| 服务治理 | 无注册发现、无负载均衡、无熔断 |
| 类型安全 | 接口易失配、调试困难           |
| 可观测性 | Trace 需手动传递、日志关联性差 |
| 容错性   | 无自动重试、超时控制弱         |
| 维护性   | URL 写死、版本升级成本高       |

## 三、Dubbo 方式

### 1、使用步骤

1. 添加`dubbo`核心依赖。

```xml
<!--Dubbo 依赖-->
<dependency>
    <groupId>org.apache.dubbo</groupId>
    <artifactId>dubbo-spring-boot-starter</artifactId>
</dependency>
```

2. 添加要使用的注册中心依赖。

```xml
<!--dubbo 注册中心-->
<dependency>
    <groupId>org.apache.dubbo</groupId>
    <artifactId>dubbo-registry-nacos</artifactId>
</dependency>
```

3. 添加要使用的协议依赖。

```xml
<!--dubbo 协议-->
<dependency>
    <groupId>org.apache.dubbo</groupId>
    <artifactId>dubbo-rpc-dubbo</artifactId>
</dependency>
```

4. 配置dubbo相关的基本信息。

```yml
spring:
  application:
    name: dubbo-provider
dubbo:
  application:
    name: ${spring.application.name}
```

5. 配置注册中心地址。

```yml
dubbo:
  registry:
    address: nacos://8.130.147.75:8848 # nacos地址
    parameters:
      namespace: a64b3045-c171-4423-b633-871e5979933c # nacos命名空间 通过namespace把它隔离开了 防止其他服务访问
      group: dubbo # nacos分组
```

6. 配置所使用的协议。

```yml
dubbo:
  protocol:
    name: dubbo
    port: -1 # 默认端口 20880
```

### 2、示例

1. 启动 Dubbo

```java
@EnableDubbo // 开启Dubbo
@SpringBootApplication
public class ProviderApplication {
    public static void main(String[] args) {
        SpringApplication.run(ProviderApplication.class, args);
    }
}
@EnableDubbo // 开启Dubbo
@SpringBootApplication
public class ConsumerApplication {
    public static void main(String[] args) {
        SpringApplication.run(ConsumerApplication.class, args);
    }
}
```

2. 写 Dubbo 接口

```java
public interface UserService {
    public String getUser();
}
@DubboService(version = "1.0") // 添加DubboService注解 并且可以声明版本号
public class UserServiceImpl implements UserService { // 实现 Dubbo 接口
    public String getUser() {
        return "BananaPeach --- version 1.0";
    }
}
@DubboService(version = "2.0")
public class UserServiceImpl2 implements UserService {
    public String getUser() {
        return "BananaPeach --- version 2.0";
    }
}
```

3. 进行调用

```java
@Service
public class OrderServiceImpl {

    @DubboReference(version = "1.0") // 表示要引入一个服务,并可以指定版本号
    private UserService userService;

    public String getOrder() {
        return "订单服务" + userService.getUser();
    }
}
```

## 四、Dubbo 新特性

### 1、注册模型的改变



### 2、新一代RPC协议

