### 原因1： pom文件中缺少依赖

+ spring-cloud-starter-config

```xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-config</artifactId>
</dependency>
```

### 原因2：yml中eureka的地址配置错误,例如ip没错，端口写错

```yml

spring:
  profiles: local
eureka:
  client:
    serviceUrl:
      defaultZone: http://127.0.0.1:11120/eureka/ # 可配置多个地址
```

### 原因3：从config-server拉取配置的config name张冠李戴，配置错误

```yml
spring:
  application:
    name: ANT-SRV
  profiles:
    active: local
  cloud:
    config:
      name: common-2.0
```

### 原因4：该应用部署服务器与配置服务器网络不通，无法顺利拉取配置

### 原因5：config-server中的配置文件命名有误
