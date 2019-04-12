## 升级前技术栈版本
  + spring boot
  + spring cloud
## 升级后技术栈版本
  + spring boot
  + spring cloud

## 注意事项

### 1： 父maven项目pom.xml

```xml
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
<modelVersion>4.0.0</modelVersion>
<groupId>com.xuexin</groupId>
<artifactId>xcloud-base</artifactId>
<version>2.0.5</version>
<packaging>pom</packaging>
<name>xcloud-base</name>
<parent>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-parent</artifactId>
    <version>2.0.6.RELEASE</version>
</parent>

<properties>
    <java.version>1.8</java.version>
    <xxl-job.version>1.6.2</xxl-job.version>
    <druid.version>1.1.15</druid.version>
    <mybatis-spring.version>1.3.2</mybatis-spring.version>
    <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
    <project.reporting.outputEncoding>UTF-8</project.reporting.outputEncoding>
    <fastjson.version>1.2.32</fastjson.version>
    <kisso.version>3.6.13</kisso.version>
    <springframework.cloud.version>Finchley.SR1</springframework.cloud.version>
    <redis.version>2.9.0</redis.version>
    <jackson.core.version>2.9.4</jackson.core.version>
</properties>
<dependencyManagement>
    <dependencies>
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-dependencies</artifactId>
            <version>${springframework.cloud.version}</version>
            <type>pom</type>
            <scope>import</scope>
        </dependency>
        <dependency>
            <groupId>org.mybatis.spring.boot</groupId>
            <artifactId>mybatis-spring-boot-starter</artifactId>
            <version>${mybatis-spring.version}</version>
        </dependency>
        <dependency>
            <groupId>com.alibaba</groupId>
            <artifactId>druid</artifactId>
            <version>${druid.version}</version>
        </dependency>
        <dependency>
            <groupId>com.xuxueli</groupId>
            <artifactId>xxl-job-core</artifactId>
            <version>${xxl-job.version}</version>
        </dependency>
        <dependency>
            <groupId>com.alibaba</groupId>
            <artifactId>fastjson</artifactId>
            <version>${fastjson.version}</version>
        </dependency>
        <dependency>
            <groupId>com.baomidou</groupId>
            <artifactId>kisso</artifactId>
            <version>${kisso.version}</version>
        </dependency>
        <dependency>
            <groupId>redis.clients</groupId>
            <artifactId>jedis</artifactId>
            <version>${redis.version}</version>
        </dependency>
        <dependency>
            <groupId>com.fasterxml.jackson.core</groupId>
            <artifactId>jackson-databind</artifactId>
            <version>${jackson.core.version}</version>
        </dependency>
        <dependency>
            <groupId>com.zaxxer</groupId>
            <artifactId>HikariCP</artifactId>
            <version>2.7.8</version>
        </dependency>

        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-openfeign</artifactId>
            <version>2.0.0.RELEASE</version>
        </dependency>

        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-netflix-zuul</artifactId>
            <version>2.0.1.RELEASE</version>
        </dependency>

        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
            <version>2.0.1.RELEASE</version>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-data-redis</artifactId>
            <version>2.0.2.RELEASE</version>
        </dependency>
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
            <version>2.0.1.RELEASE</version>
        </dependency>
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-netflix-eureka-server</artifactId>
            <version>2.0.1.RELEASE</version>
        </dependency>
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-sleuth-zipkin-stream</artifactId>
            <version>1.2.1.RELEASE</version>
        </dependency>
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-stream-rabbit</artifactId>
            <version>1.2.1.RELEASE</version>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-amqp</artifactId>
            <version>2.0.3.RELEASE</version>
        </dependency>
        <dependency>
            <groupId>io.zipkin.java</groupId>
            <artifactId>zipkin-autoconfigure-ui</artifactId>
            <version>1.23.2</version>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-actuator</artifactId>
            <version>2.0.6.RELEASE</version>
        </dependency>
    </dependencies>
</dependencyManagement>

    <modules>
        <module>xcloud-eureka-server</module>
        <module>xcloud-config-server</module>
        <module>xcloud-base-entity</module>
        <module>xcloud-sso-client</module>
        <module>xcloud-zuul</module>
        <module>xcloud-zipkin</module>
        <module>xcloud-admin</module>
        <module>xcloud-websocket</module>
        <module>xcloud-transaction-manager</module>
    </modules>

<build>
    <pluginManagement>
        <plugins>
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-compiler-plugin</artifactId>
                <configuration>
                    <source>${java.version}</source>
                    <target>${java.version}</target>
                </configuration>
            </plugin>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
                <version>2.0.6.RELEASE</version>
            </plugin>
        </plugins>
    </pluginManagement>
</build>
</project>

```

### 2. config-server

#### 2.1 启动类

```java

package com.xuexin.xcloud.config.server;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.cloud.client.discovery.EnableDiscoveryClient;
import org.springframework.cloud.config.server.EnableConfigServer;

@EnableDiscoveryClient
@EnableConfigServer
@SpringBootApplication
public class ConfigServerStart {

	public static void main(String[] args) {
		SpringApplication.run(ConfigServerStart.class, args);
	}
}

```

### 2.2 配置文件 application.yml

```yml
server:
  port: 11110

spring:
  application:
     name: CONFIG-SRV  # Service registers under this name
# spring cloud configuration
  cloud:
    config:
      server:
        git:
          uri: http://git..../xxx.git
          username: ****
          password: ***
  profiles:  
    active: local
---
#本地开发环境    
spring:
  profiles: local  

#eureka configuration
eureka:
  client:
    serviceUrl:
      defaultZone: http://127.0.0.1:11120/eureka/ # 可配置多个地址
  instance:
    prefer-ip-address: true   #服务之间调用时，指定IP调用
    instance-id: ${spring.cloud.client.ip-address}:${server.port}
```

### 2.3 pom.xml

```xml
<?xml version="1.0"?>
<project
	xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd"
	xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
	<modelVersion>4.0.0</modelVersion>
	<parent>
		<groupId>com.xuexin</groupId>
		<artifactId>xcloud-base</artifactId>
		<version>2.0.5</version>
	</parent>

	<artifactId>xcloud-config-server</artifactId>
	<name>xcloud-config-server</name>

	<properties>
		<project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
	</properties>

	<dependencies>
		<dependency>
			<groupId>org.springframework.cloud</groupId>
			<artifactId>spring-cloud-config-server</artifactId>
		</dependency>
		<dependency>
			<groupId>org.springframework.cloud</groupId>
			<artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
		</dependency>
	</dependencies>

	<build>
		<plugins>
			<plugin>
				<groupId>org.springframework.boot</groupId>
				<artifactId>spring-boot-maven-plugin</artifactId>
			</plugin>
		</plugins>
	</build>
</project>

```

## 3.Zuul

### 3.1 启动类

```java
package com.xuexin.xcloud.zuul;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.cloud.client.discovery.EnableDiscoveryClient;
import org.springframework.cloud.netflix.zuul.EnableZuulProxy;

@EnableZuulProxy
@EnableDiscoveryClient
@SpringBootApplication
public class ZuulServerStart {
	public static void main(String[] args) {
		SpringApplication.run(ZuulServerStart.class, args);
	}
}
```

### 3.2 配置文件 application.yml

```yml
# HTTP Server
server:
  port: 80   # HTTP (Tomcat) port
  tomcat:
    max-threads: 16 # 最大worker线程
    min-spare-threads: 8 # 最小worker线程
  undertow:
    io-threads: 4 # IO线程数，默认为CPU核心数，最小为2
    worker-threads: 32 # 阻塞任务线程池，值设置取决于系统的负载，默认为io-threads * 8
    buffer-size: 512 # 每块buffer的空间大小

# 默认的profile为local，其他环境通过指定启动参数使用不同的profile，比如：  
# Spring properties
spring:
  application:
    name: ZUUL-SRV  # Service registers under this name
  profiles:  
    active: local
  servlet:
    multipart:
      max-file-size: 100Mb
      max-request-size: 100Mb

zuul:
  sensitiveHeaders:
  ignoreSecurityHeaders: false
  host:
    socket-timeout-millis: 60000
    connect-timeout-millis: 60000
    max-per-route-connections: 100   #扩大路由线程池的容量
    max-total-connections: 1000   #最大1000

hystrix:
  threadpool:
    default:
      coreSize: 500  #核心线程数
      maximumSize: 10000  #最大线程池大小
      queueSizeRejectionThreshold: -1 #排队线程数量阈值，默认为5
      maxQueueSize: -1  #  最大排队长度。默认-1
  command:
    default:
      execution:
        timeout:
          enabled: true
        isolation:
          thread:  #针对所有方法超时时间，这里也可以针对不同的命令设置超时时间
            timeoutInMilliseconds: 60000
          semaphore:  #最大并发数
            maxConcurrentRequests: 200

ribbon:
  ReadTimeout: 150000   #响应的最大时间
  ConnectTimeout: 60000  #链接的超时时间
  MaxAutoRetries: 2  #同实体重试次数
  MaxAutoRetriesNextServer: 1  #不同的实体最大的重试次数

---
#本地开发环境    
#spring cloud 服务发现地址
# 集成开发环境配置
spring:
  profiles: local

eureka:
  client:
    serviceUrl:
      defaultZone: http://127.0.0.1:11120/eureka/ # 可配置多个地址
  instance:
    prefer-ip-address: true   #服务之间调用时，指定IP调用
    instance-id: ${spring.cloud.client.ip-address}:${server.port}

```

### 3.3 pom.xml

```xml
<?xml version="1.0"?>
<project
	xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd"
	xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
	<modelVersion>4.0.0</modelVersion>
	<parent>
		<groupId>com.xuexin</groupId>
		<artifactId>xcloud-base</artifactId>
		<version>2.0.5</version>
	</parent>
	<artifactId>xcloud-zuul</artifactId>
	<name>xcloud-zuul</name>
	<dependencies>
		<dependency>
			<groupId>org.springframework.cloud</groupId>
			<artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
		</dependency>
		<dependency>
			<groupId>org.springframework.cloud</groupId>
			<artifactId>spring-cloud-starter-config</artifactId>
		</dependency>
		<dependency>
			<groupId>org.springframework.cloud</groupId>
			<artifactId>spring-cloud-starter-netflix-zuul</artifactId>
		</dependency>
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-actuator</artifactId>
		</dependency>
	</dependencies>

	<build>
		<plugins>
			<plugin>
				<groupId>org.springframework.boot</groupId>
				<artifactId>spring-boot-maven-plugin</artifactId>
			</plugin>
		</plugins>
	</build>

</project>

```



### 改动1 ： eureka client 依赖


```xml

<dependency>
	<groupId>org.springframework.cloud</groupId>
	<artifactId>spring-cloud-starter-eureka</artifactId>
</dependency>

```
改为

```xml

<dependency>
	<groupId>org.springframework.cloud</groupId>
	<artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
</dependency>

```

### 改动2 ： Redis改动，redis默认实现类从原来的Jedis改为lettuce，yml中参数也需要修改

```yml

spring:
  redis:
    host: 10.**.**.104
    port: 6379
    password:*****
    pool:
      max-idle: 100 # pool settings ...
      min-idle: 10
      max-active: 50
      max-wait: -1
```

改为

```yml

spring:
  redis:
    host: 10.**.**.104
    port: 6379
    password: *****
    lettuce:
      pool:
        max-idle: 100 # pool settings ...
        min-idle: 10
        max-active: 50
      max-wait: -1
```

### 改动3： eureka实例展示为ip:port的方式

```yml
eureka:
  instance:
    prefer-ip-address: true   #服务之间调用时，指定IP调用
    instance-id: ${spring.cloud.client.ipAddress}:${server.port}
    lease-expiration-duration-in-seconds: 10
    lease-renewal-interval-in-seconds: 5
```

改为

```yml
eureka:
  instance:
    prefer-ip-address: true   #服务之间调用时，指定IP调用
    instance-id: ${spring.cloud.client.ip-address}:${server.port}
    lease-expiration-duration-in-seconds: 10
    lease-renewal-interval-in-seconds: 5
```

### 改动4：FeignClients依赖包修改

```java
import org.springframework.cloud.netflix.feign.EnableFeignClients;

<dependency>
	<groupId>org.springframework.cloud</groupId>
	<artifactId>spring-cloud-starter-feign</artifactId>
</dependency>

```

改为

```java
import org.springframework.cloud.openfeign.EnableFeignClients;

<dependency>
	<groupId>org.springframework.cloud</groupId>
	<artifactId>spring-cloud-starter-openfeign</artifactId>
</dependency>

```

### 改动5：SpringBoot2.0 自定义RequestMappingHandlerMapping和HandlerInterceptor问题

SpringBoot升级到2.0以后配置文件继承的WebMvcConfigurerAdapter已废除，需要改为WebMvcConfigurationSupport，

addInterceptors()方法覆写不变，但getRequestMappingHandlerMapping() 需要改为createRequestMappingHandlerMapping()

自定义的RequestMappingHandlerMapping才能生效。

```java
package com.xuexin.xcloud.personal.conf;

import feign.Feign;
import org.springframework.boot.autoconfigure.condition.ConditionalOnClass;
import org.springframework.boot.autoconfigure.web.WebMvcRegistrations;
import org.springframework.boot.autoconfigure.web.WebMvcRegistrationsAdapter;
import org.springframework.cloud.openfeign.FeignClient;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.core.annotation.AnnotationUtils;
import org.springframework.web.servlet.mvc.method.annotation.RequestMappingHandlerMapping;

@Configuration
@ConditionalOnClass({Feign.class})
public class FeignMappingDefaultConfiguration {
    @Bean
    public WebMvcRegistrations feignWebRegistrations() {
        return new WebMvcRegistrationsAdapter() {
            @Override
            public RequestMappingHandlerMapping getRequestMappingHandlerMapping() {
                return new FeignFilterRequestMappingHandlerMapping();
            }
        };
    }

    private static class FeignFilterRequestMappingHandlerMapping extends RequestMappingHandlerMapping {
        @Override
        protected boolean isHandler(Class<?> beanType) {
            return super.isHandler(beanType) && (AnnotationUtils.findAnnotation(beanType, FeignClient.class) == null);
        }
    }
}

```

改为

```java
package com.xuexin.xcloud.personal.conf;

import org.springframework.cloud.openfeign.FeignClient;
import org.springframework.core.annotation.AnnotationUtils;
import org.springframework.stereotype.Component;
import org.springframework.web.servlet.config.annotation.WebMvcConfigurationSupport;
import org.springframework.web.servlet.mvc.method.annotation.RequestMappingHandlerMapping;

@Component
public class WebMvConfig extends WebMvcConfigurationSupport {

	/**
	 * 自定义RequestMappingHandlerMapping
	 *  FeignMappingDefaultConfiguration
	 * @return
	 */
	@Override
	protected RequestMappingHandlerMapping createRequestMappingHandlerMapping() {
		return new FeignFilterRequestMappingHandlerMapping();
	}

	private static class FeignFilterRequestMappingHandlerMapping extends RequestMappingHandlerMapping {
		@Override
		protected boolean isHandler(Class<?> beanType) {
			return super.isHandler(beanType) && (AnnotationUtils.findAnnotation(beanType, FeignClient.class) == null);
		}
	}

}

```


### 遗留问题

+ springcloud支持传递MultipartFile类型
