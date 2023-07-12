# SpringCloud之初入江湖

v1.0.10_201809 By BoBo

课程目标：

- 能够说出SpringCloud包含的主要框架

- 能够使用服务发现组件Eureka（微服务注册中心）

- 能够使用Feign实现服务间的调用（调用Restful的工具）

- 完成交友微服务开发




# 1 SpringCloud简介

## 1.1 什么是SpringCloud

> Spring Cloud provides tools for developers to quickly build some of the common patterns in distributed systems (e.g. configuration management, service discovery, circuit breakers, intelligent routing, micro-proxy, control bus, one-time tokens, global locks, leadership election, distributed sessions, cluster state). Coordination of distributed systems leads to boiler plate patterns, and using Spring Cloud developers can quickly stand up services and applications that implement those patterns. They will work well in any distributed environment, including the developer's own laptop, bare metal data centres, and managed platforms such as Cloud Foundry.

Spring Cloud是一系列框架的有序集合。它利用Spring Boot的开发便利性巧妙地简化了分布式系统基础设施的开发，如服务发现注册、配置中心、消息总线、负载均衡、熔断器、数据监控等，都可以用Spring Boot的开发风格做到一键启动和部署。Spring并没有重复制造轮子，它只是将目前各家公司开发的比较成熟、经得起实际考验的服务框架组合起来，通过Spring Boot风格进行再封装屏蔽掉了复杂的配置和实现原理，最终给开发者留出了一套简单易懂、易部署和易维护的`分布式系统开发工具包`。

Spring Cloud项目的官方网址：http://projects.spring.io/spring-cloud/

## 1.2 SpringCloud与SpringBoot的关系

Spring Boot 是 Spring 的一套快速配置脚手架，可以基于Spring Boot 快速开发单个微服务，Spring Cloud是一个基于Spring Boot实现的云应用开发工具；Spring Boot专注于快速、方便集成的单个微服务个体，Spring Cloud关注全局的服务治理框架；Spring Boot使用了默认大于配置的理念，很多集成方案已经帮你选择好了，能不配置就不配置，Spring Cloud很大的一部分是基于Spring Boot来实现，可以不基于Spring Boot吗？不可以。

Spring Boot可以离开Spring Cloud独立使用开发项目，但是Spring Cloud离不开Spring Boot，属于依赖的关系。

SpringCloud主要框架：

- 服务发现——Netflix Eureka

- 服务调用——Netflix Feign

- 熔断器——Netflix Hystrix

- 服务网关——Netflix Zuul

- 分布式配置——Spring Cloud Config

- 消息总线 —— Spring Cloud Bus



## 1.3 Spring Cloud和Dubbo对比

  或许很多人会说Spring Cloud和Dubbo的对比有点不公平，Dubbo只是实现了服务治  理，而Spring Cloud下面有17个子项目（可能还会新增）分别覆盖了微服务架构下的方  方面面，服务治理只是其中的一个方面，一定程度来说，Dubbo只是Spring Cloud  Netflix中的一个子集。

|              | Dubbo     | Spring Cloud                 |
| ------------ | --------- | ---------------------------- |
| 服务注册中心 | Zookeeper | Spring Cloud Netflix Eureka  |
| 服务调用方式 | RPC       | REST API                     |
| 服务网关     | 无        | Spring Cloud Netflix Zuul    |
| 熔断器       | 不完善    | Spring Cloud Netflix Hystrix |
| 分布式配置   | 无        | Spring Cloud Config          |
| 服务跟踪     | 无        | Spring Cloud Sleuth          |
| 消息总线     | 无        | Spring Cloud Bus             |
| 数据流       | 无        | Spring Cloud Stream          |
| 批量任务     | 无        | Spring Cloud Task            |
| ……           | ……        | ……                           |



## 1.4 说说SpringCloud的版本

我们目前课程采用的SpringCloud版本为Finchley.SR1 。你可能会觉得这个版本怎么这么奇怪？SpringCloud由于是一系列框架组合，为了避免与包含的自框架版本产生混淆，采用伦敦地铁站的名称作为版本名，形式为版本名+里程碑号。 M9为第9个里程碑版本。

以下是SpringBoot与Spring Cloud版本的对照表，大家看看有没有找到什么规律呢？（伦敦地铁站名、ABCDF）

| Spring Boot | Spring Cloud             |
| ----------- | ------------------------ |
| 1.2.x       | Angel版本                |
| 1.3.x       | Brixton版本              |
| 1.4.x       | Camden版本               |
| 1.5.x       | Dalston版本、Edgware版本 |
| 2.0.x       | Finchley版本             |



# 2. 微服务发现组件 Eureka



## 2.1 Eureka

Eureka是Netflix开发的服务发现框架，SpringCloud将它集成在自己的子项目spring-cloud-netflix中，实现SpringCloud的服务发现功能。Eureka包含两个组件：Eureka Server和Eureka Client。

Eureka Server提供服务注册服务，各个节点启动后，会在Eureka Server中进行注册，这样EurekaServer中的服务注册表中将会存储所有可用服务节点的信息，服务节点的信息可以在界面中直观的看到。

Eureka Client是一个java客户端，用于简化与Eureka Server的交互，客户端同时也就别一个内置的、使用轮询(round-robin)负载算法的负载均衡器。在应用启动后，将会向Eureka Server发送心跳,默认周期为30秒，如果Eureka Server在多个心跳周期内没有接收到某个节点的心跳，Eureka Server将会从服务注册表中把这个服务节点移除(默认90秒)。

Eureka Server之间通过复制的方式完成数据的同步，Eureka还提供了客户端缓存机制，即使所有的Eureka Server都挂掉，客户端依然可以利用缓存中的信息消费其他服务的API。

综上，Eureka通过心跳检查、客户端缓存等机制，确保了系统的高可用性、灵活性和可伸缩性。



## 2.2 服务端搭建

（1）创建tensquare_eureka子模块

【了解】

单独新建微服务项目，也可。

（2）引入依赖 

1）父工程pom.xml定义（锁定）SpringCloud版本

```xml
    <dependencyManagement>
        <dependencies>
            <dependency>
                <groupId>org.springframework.cloud</groupId>
                <artifactId>spring-cloud-dependencies</artifactId>
                <version>Finchley.SR1</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
        </dependencies>
    </dependencyManagement>
```

2）tensquare_eureka模块pom.xml引入eureka-server

```xml
    <dependencies>
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-netflix-eureka-server</artifactId>
        </dependency>
    </dependencies> 
```

（2）添加application.yml

```yaml
server:
  #服务端口
  port: 8761
eureka:
  instance:
    #注册中心服务的主机，默认是localhost
    hostname: localhost
  client:
    #是否将当前微服务注册到Eureka服务中。自己是注册中心，因此无需注册。
    register-with-eureka: false
    #是否从Eureka中获取注册信息。自己是注册中心，因此无需获取。
    fetch-registry: false
    #Eureka客户端与与Eureka服务端进行交互的地址Map表
    service-url:
      #默认http://localhost:8761/eureka/
      defaultZone: http://${eureka.instance.hostname}:${server.port}/eureka/

```

（3）编写启动类

创建包com.tensquare.eureka ，包下建立类

com.tensquare.eureka.EurekaApplication

```java
package com.tensquare.eureka;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.cloud.netflix.eureka.server.EnableEurekaServer;

/**
 * Eureka服务的启动类
 */
@SpringBootApplication
//开启Eureka服务
@EnableEurekaServer
public class EurekaApplication {
    public static void main(String[] args) {
        SpringApplication.run(EurekaApplication.class);
    }

}

```

（4）启动运行启动类，然后在浏览器地址栏输入 `http://localhost:8761/` 运行效果如下：

![1536157398085](day07.assets/1536157398085.png)

主界面中：

- system status 系统信息 

- Instances currently registered with Eureka 注册到的所有微服务列表

- General Info 一般信息 

- Instance Info 实例信息




## 2.3 客户端服务注册

我们现在就将所有的微服务都注册到Eureka中，这样所有的微服务之间都可以互相调用
了。

（1）将其他微服务模块添加依赖，公共模块、短信微服务除外。

```xml
		<dependency>
			<groupId>org.springframework.cloud</groupId>
			<artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
		</dependency>
```



（2）修改每个微服务的application.yml，添加注册eureka服务的配置

```yaml
eureka:
  client:
    #Eureka服务的地址
    service-url:
      defaultZone: http://localhost:8761/eureka/
  instance:
    #用于表示在猜测主机名时，服务器的IP地址应该与操作系统报告的主机名相对应。(注册服务和客户端如果在一台机器上则无需配置)
    prefer-ip-address: true
```

（3）修改每个服务类的启动类，添加注解`@EnableEurekaClient`，可以省略。

```java
@EnableEurekaClient
```

（4）启动测试：将每个微服务启动起来，会发现eureka的注册列表中可以看到这些微服务了（8个）：

![1536168186950](day07.assets/1536168186950.png)



## 2.4 保护模式

停掉其中的某服务，再默认等待1分钟，注册实例列表的上方会出现红色的字体提示，内容为：

```
EMERGENCY! EUREKA MAY BE INCORRECTLY CLAIMING INSTANCES ARE UP WHEN THEY'RE NOT. RENEWALS ARE LESSER THAN THRESHOLD AND HENCE THE INSTANCES ARE NOT BEING EXPIRED JUST TO BE SAFE.
```

如果在Eureka Server的首页看到以下这段提示，则说明Eureka已经进入了保护模式。

解释如下：

 Eureka Server在运行期间，会统计心跳失败的比例在15分钟之内是否低于85%，如果出现低于的情况（在单机调试的时候很容易满足，实际在生产环境上通常是由于网络不稳定导致），Eureka Server会将当前的实例注册信息保护起来，同时提示这个警告。保护模式主要用于一组客户端和Eureka Server之间存在网络分区场景下的保护。一旦进入保护模式，Eureka Server将会尝试保护其服务注册表中的信息，不再删除服务注册表中的数据（也就是不会注销任何微服务）。

【了解】

关闭保护模式（如无特殊需要，不建议）：

Eureka server的application.yml

```yaml
eureka:  
  server:  
  	#是否开启安全保护，默认是true开启。
    enableSelfPreservation: false  
    #清理间隔（单位毫秒，默认是60*1000）
    eviction-interval-timer-in-ms: 6000
```

微服务的application.yml

```yaml
eureka:  
  instance:
  	#租期更新时间间隔（默认30秒）
    leaseRenewalIntervalInSeconds: 10  
    #租期到期时间（默认90秒）
    leaseExpirationDurationInSeconds: 30  

```

注意：更改Eureka更新频率将打破服务器的自我保护功能，如无特殊需要，不建议。

优雅下线。。。



# 3. Feign实现微服务间的调用

## 3.1 Feign简介

Feign是简化Java HTTP客户端开发的工具（java-to-httpclient-binder），它的灵感来自于Retrofit、JAXRS-2.0和WebSocket。Feign的初衷是降低统一绑定Denominator到HTTP API的复杂度，不区分是否为restful。



## 3.2 快速体验

我们现在在问答微服务调用基础微服务的方法（根据ID查询标签）

即：使用Feign客户端去调用Eureka注册的服务的接口

（1）在tensquare_qa模块添加依赖

```xml
		<dependency>
			<groupId>org.springframework.cloud</groupId>
			<artifactId>spring-cloud-starter-openfeign</artifactId>
		</dependency>
```

提示：在调用者的服务上操作，被调用者的服务无需操作。

（2）修改tensquare_qa模块的启动类，添加注解

```java
//用来标识开启服务发现功能
@EnableDiscoveryClient
//用来开启Feign功能
@EnableFeignClients
```

【了解】

@EnableDiscoveryClient与@EnableEurekaClient、@EnableFeignClients都是Spring Cloud中的服务（客户端）发现注解，他们之间的关系和区别为：

@EnableDiscoveryClient基于spring-cloud-commons，自动在类路径上实现。spring cloud中discovery service有许多种实现（eureka、consul、zookeeper等等），都要基于该注解。

@EnableEurekaClient、@EnableFeignClients都是具体发现服务的实现的注解，@EnableEurekaClient基于spring-cloud-netflix。

其实用更简单的话来说，就是如果选用的注册中心是eureka，那么就推荐@EnableEurekaClient，如果是其他的注册中心，那么推荐使用@EnableDiscoveryClient。

（3）在tensquare_qa模块创建 com.tensquare.qa.client包，包下创建接口

com.tensquare.qa.client.LabelClient

```java
package com.tensquare.qa.client;

import dto.ResultDTO;
import org.springframework.cloud.openfeign.FeignClient;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.PathVariable;

//用于声明应该创建具有该接口的REST客户端（例如，用于自动连接到另一个组件）
@FeignClient("tensquare-base")//tensquare-base:9001--http://DESKTOP-1CG46P9:9001
public interface LabelClient {

    /**
     * 根据id查询一个标签-用于Feign
     * 注意：方法名和资源路径和label一样
     * http://DESKTOP-1CG46P9:9001//label/{id}
     * @param id
     * @return
     */
    @GetMapping("/label/{id}")
    ResultDTO listById(@PathVariable("id") String id) ;
}

```

@FeignClient注解用于指定从哪个服务中调用功能 ，注意 里面的名称与被调用的服务名保持一致，并且不能包含下划线。

@GetMapping或@RequestMapping注解用于对被调用的微服务进行地址映射。注意 @PathVariable注解一定要指定参数名称，否则出错

（5）修改tensquare_qa模块的 ProblemController

```java
	//注入Feign的labeClint
	@Autowired
	private LabelClient labelClient;


	/**
	 * 根据标签id，通过微服务来查询标签对象
	 * @param labelid
	 * @return
	 */
	@GetMapping("/label/{labelid}")
	public ResultDTO findLabelById(@PathVariable  String labelid){
		//通过FeignClient来调用REst服务接口
		ResultDTO resultDTO = labelClient.listById(labelid);
		return resultDTO;
	}

```

（6）测试：http://localhost:9003/problem/label/1 

能看到查询到的标签的信息，并查看控制台。



## 3.3 负载均衡

测试：同时开启多个base基础微服务，看是否是轮流调用。

修改tensquare_base工程LabelController的findById方法

```java
/**
     * 根据id查询一个
     *
     * @param id
     * @return
     */
   @GetMapping("/{id}")
    public ResultDTO listById(@PathVariable String id, HttpServletRequest request){
        //获取被调用时的服务端口
        System.out.println("当前服务端口："+request.getServerPort());
        Label label = labelService.findLabelById(id);
        return new ResultDTO(true, StatusCode.OK,"查询成功",label);
    }
```

1）启动基础微服务后，

2）修改端口和输出信息，再次启动基础微服务

启动问答微服务，浏览器多次执行http://localhost:9003/problem/label/1 看是否轮流启动。



# 4. 交友微服务开发

## 4.1 需求分析

交友微服务不但要操作交友表中的数据，还要操作用户表中的数据，而该数据通过用户微服务来提供。

好友表：

| 好友表   | tb_friend    |          |                         |
| -------- | ------------ | -------- | ----------------------- |
| 字段名称 | 字段含义     | 字段类型 | 备注                    |
| userid   | 用户ID       | 文本     |                         |
| friendid | 好友ID       | 文本     |                         |
| islike   | 是否互相喜欢 | 文本     | 0：单向喜欢 1：互相喜欢 |



非好友表（相当于黑名单）

| 非好友表 | tb_nofriend |          |      |
| -------- | ----------- | -------- | ---- |
| 字段名称 | 字段含义    | 字段类型 | 备注 |
| userid   | 用户ID      | 文本     |      |
| friendid | 好友ID      | 文本     |      |



交友微服务本身的功能:
（1）当用户登陆后在推荐好友列表中点击“心”，表示喜欢此人 ，在数据库tb_friend表中插入一条数据，islike 为0
（2）当你点击了喜欢过的人，也喜欢了你 , 表示互粉成功！也向tb_friend表中插入一条数据，islike为1 ，并且将你喜欢她的数据islike也修改为1
（3）当你点击了不喜欢某人（点击了叉），向tb_nofriend添加记录.
（4）当两个人互粉后，其中一人不喜欢对方了，删除好友表中的记录 ，向非好友表中添加记录
什么场景下使用springCloud呢？
我们来看用户表，有两列： fanscount 表示粉丝数 ，followcount表示关注数（1）当用户点击了喜欢：
比如小宝关注了楚楚，小宝的followcount（关注数）加1 ， 楚楚的fanscount （粉丝数）加1
（2）当用户删除了好友：
比如楚楚删除了好友小宝，小宝的fanscount （粉丝数）减1 ，楚楚的followcount（关注数）减1



## 4.2 交友微服务-添加与删除好友

### 4.2.1 模块搭建

（1）使用  代码生成器   生成tensquare_friend子模块工程，pom.xml添加新的依赖，结果如下：

```xml
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <parent>
        <groupId>com.tensquare</groupId>
        <artifactId>tensquare_parent</artifactId>
        <version>1.0-SNAPSHOT</version>
    </parent>
    <artifactId>tensquare_friend</artifactId>
    <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-data-jpa</artifactId>
        </dependency>
        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
        </dependency>
        <dependency>
            <groupId>com.tensquare</groupId>
            <artifactId>tensquare_common</artifactId>
            <version>1.0-SNAPSHOT</version>
        </dependency>
        <!--拷贝开始-->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-data-redis</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-openfeign</artifactId>
        </dependency>
    </dependencies>
</project>

```

（2）更改application.yml，增加jwt和eureka的配置：

```yaml
server: 
  port: 9010
spring: 
  application:  
    name: tensquare-friend #指定服务名
  datasource:  
    driverClassName: com.mysql.jdbc.Driver
    url: jdbc:mysql://192.168.40.128:3306/tensquare_friend?characterEncoding=UTF8
    username: root
    password: 123456
  jpa: 
    database: MySQL
    show-sql: true
#自定义属性jwt相关
jwt:
  config:
    key: itcast
eureka:
  client:
    #Eureka服务的地址
    service-url:
      defaultZone: http://localhost:8761/eureka/
  instance:
    #用于表示在猜测主机名时，服务器的IP地址应该与操作系统报告的主机名相对应。(注册服务和客户端如果在一台机器上则无需配置)
    prefer-ip-address: true
```

（3）更改启动类，添加三个新的注解、JwtUtil的Bean：

com.tensquare.friend.FriendApplication

```java
package com.tensquare.friend;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.cloud.client.discovery.EnableDiscoveryClient;
import org.springframework.cloud.netflix.eureka.EnableEurekaClient;
import org.springframework.cloud.openfeign.EnableFeignClients;
import org.springframework.context.annotation.Bean;
import utils.IdWorker;
import utils.JwtUtil;

@SpringBootApplication
@EnableEurekaClient
@EnableDiscoveryClient
@EnableFeignClients
public class FriendApplication {

	public static void main(String[] args) {
		SpringApplication.run(FriendApplication.class, args);
	}

	@Bean
	public IdWorker idWorker(){
		return new IdWorker(1, 1);
	}

	@Bean
	public JwtUtil jwtUtil(){
		return new utils.JwtUtil();
	}
	
}

```

（4）创建JwtInterceptor（参见User或Qa工程，直接拷贝过来）

com.tensquare.friend.web.interceptor.JwtInterceptor

````java
package com.tensquare.friend.web.interceptor;

import io.jsonwebtoken.Claims;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Component;
import org.springframework.web.servlet.handler.HandlerInterceptorAdapter;
import utils.JwtUtil;

import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

/**
 * 自定义拦截器，用于Jwt验证token
 */
@Component
public class JwtInterceptor extends HandlerInterceptorAdapter {

    //注入JwtUtil
    @Autowired
    private JwtUtil jwtUtil;

    @Override
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
        System.out.println("经过了JwtInterceptor拦截器...");
        //获取头信息，约定头信息key为Authorization
        final String authorizationHeader = request.getHeader("JwtAuthorization");

        //判断authorizationHeader不为空,并且是"Bearer "开头的
        if(null !=authorizationHeader && authorizationHeader.startsWith("Bearer ")){
            //获取令牌，The part after "Bearer "
            final String token=authorizationHeader.substring(7);
            //获取载荷
            Claims claims = null;
            try {
                claims = jwtUtil.parseJWT(token);
            } catch (Exception e) {
                e.printStackTrace();
            }

            //判断载荷是否为空
            if(null != claims){
                //判断令牌中的自定义载荷中的角色是否是admin（管理员）
                if("admin".equals(claims.get("roles"))){
                    request.setAttribute("admin_claims",claims);
                }
                //判断令牌中的自定义载荷中的角色是否是user（普通用户）
                if("user".equals(claims.get("roles"))){
                    request.setAttribute("user_claims",claims);
                }
            }
        }

        return true;
    }
}

````



（5）创建ApplicationConfig（参见User或Qa工程，直接拷贝过来，并修改）

com.tensquare.friend.config.WebMvcConfiguration

```java
package com.tensquare.friend.config;

import com.tensquare.friend.web.interceptor.JwtInterceptor;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.context.annotation.Configuration;
import org.springframework.web.servlet.config.annotation.InterceptorRegistry;
import org.springframework.web.servlet.config.annotation.WebMvcConfigurationSupport;

/**
 * WebMvc配置类
 */
@Configuration
public class WebMvcConfiguration extends WebMvcConfigurationSupport {

    //注入JwtInterceptor
    @Autowired
    private JwtInterceptor jwtInterceptor;

    /**
     * WebMvc中添加拦截器
     * @param registry
     */
    @Override
    protected void addInterceptors(InterceptorRegistry registry) {
        registry.addInterceptor(jwtInterceptor)
                //添加需要拦截器拦截的资源
                .addPathPatterns("/**");
    }
}

```



![1536212300762](day07.assets/1536212300762.png)

启动项目，查看是否正常注册到Eureka中。



### 4.2.2 添加好友

（1）创建实体类

```java
package com.tensquare.friend.po;

import javax.persistence.Entity;
import javax.persistence.Id;
import javax.persistence.IdClass;
import javax.persistence.Table;
import java.io.Serializable;

@Entity
@Table(name = "tb_friend")
//联合（复合）主键的类，如果该实体类的属性畜类联合主键属性外，还有其他属性，则必须加该注解，指定联合主键的类
@IdClass(Friend.class)
public class Friend implements Serializable {
    @Id
    private String userid;//用户id
    @Id
    private String friendid;//好友id

    private String islike;//是否喜欢

    public String getUserid() {
        return userid;
    }

    public void setUserid(String userid) {
        this.userid = userid;
    }

    public String getFriendid() {
        return friendid;
    }

    public void setFriendid(String friendid) {
        this.friendid = friendid;
    }

    public String getIslike() {
        return islike;
    }

    public void setIslike(String islike) {
        this.islike = islike;
    }
}
```

（2）新建dao包，创建FriendRepository接口

```java
package com.tensquare.friend.dao;

import com.tensquare.friend.po.Friend;
import org.springframework.data.jpa.repository.JpaRepository;
import org.springframework.data.jpa.repository.Modifying;
import org.springframework.data.jpa.repository.Query;

/**
 * 交友数据访问层
 */
public interface FriendRepository extends JpaRepository<Friend, String> {
    
    /**
     * 根据用户ID与被关注的好友用户ID，查询记录个数
     *
     * @param userid
     * @param friendid
     * @return
     */
    @Query("select count(f) from Friend f where f.userid=?1 and f.friendid=?2")
    int findCountByUseridAndFriendid(String userid, String friendid);
    
        /**
     * 根据用户ID与被关注的好友用户ID，查询记录个数(属性表达式的方式)
     *
     * @param userid
     * @param friendid
     * @return
     */
    int countByUseridAndFriendid(String userid,String friendid);

    /**
     * 根据用户id和好友id，更新互相喜欢的状态（互粉状态，islike）
     *
     * @param userid
     * @param friendid
     */
    @Modifying
    @Query("update Friend f  set f.islike=?3 where f.userid=?1 and f.friendid=?2")
    void updateIslikeByUseridAndFriendid(String userid, String friendid, String islike);
}
```

（3）创建业务逻辑类

创建com.tensquare.friend.service包，包下建立类FriendService

```java
package com.tensquare.friend.service;

import com.tensquare.friend.dao.FriendRepository;
import com.tensquare.friend.po.Friend;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;
import org.springframework.transaction.annotation.Transactional;

@Service
public class FriendService {
    @Autowired
    private FriendRepository friendRepository;

     /**
     * 保存好友
     * @param userid
     * @param friendid
     * @return
     */
    @Transactional
    public int saveFriend(String userid, String friendid) {
        //判断如果用户已经添加了这个好友，则不进行任何操作,返回0
        if (friendRepository.findCountByUseridAndFriendid(userid, friendid) > 0) {
            return 0;
        }
        //向好友表中添加记录
        Friend friend = new Friend();
        friend.setUserid(userid);
        friend.setFriendid(friendid);
        friend.setIslike("0");
        friendRepository.save(friend);
        //判断对方是否喜欢你，如果喜欢，将islike设置为1
        //判断对方是否已经添加你为好友，如果是，则修改互粉状态。聊天通道打开。
        if (friendRepository.findCountByUseridAndFriendid(friendid, userid) > 0) {
            friendRepository.updateIslikeByUseridAndFriendid(userid, friendid, "1");
            friendRepository.updateIslikeByUseridAndFriendid(friendid, userid, "1");
        }
        
        //正常添加了
        return 1;
    }
}
```

（3）控制器类

创建包com.tensquare.friend.web.controller，包下创建FriendController

```java
package com.tensquare.friend.web.controller;

import com.tensquare.friend.service.FriendService;
import constants.StatusCode;
import dto.ResultDTO;
import io.jsonwebtoken.Claims;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.PathVariable;
import org.springframework.web.bind.annotation.PutMapping;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;
import org.springframework.web.bind.annotation.CrossOrigin;

import javax.servlet.http.HttpServletRequest;

@RestController
@RequestMapping("/friend")
@CrossOrigin
public class FriendController {
    @Autowired
    private FriendService friendService;
    @Autowired
    private HttpServletRequest request;

    /**
     * 添加好友
     *
     * @param friendid 对方用户ID
     * @param type     1：喜欢 0：不喜欢
     * @return
     */
    @PutMapping("/like/{friendid}/{type}")
    public ResultDTO add(@PathVariable String friendid, @PathVariable String type) {
        //获取当前登录人的信息
        Claims claims = (Claims) request.getAttribute("user_claims");
        //判断是否能获取到
        if (claims == null) {
            return new ResultDTO(false, StatusCode.ACCESSERROR, "无权访问，请先登录");
        }
       //判断是否是喜欢
        if (type.equals("1")) {
            //如果是喜欢，要添加好友
            if (friendService.saveFriend(claims.getId(), friendid) == 0) {
                return new ResultDTO(false, StatusCode.REPERROR, "已经添加过此好友");
            }
        } else {
            //不喜欢
        }
        return new ResultDTO(true, StatusCode.OK, "操作成功");
    }
}
```



（4）测试：(...)启动用户微服务，登陆用户获取token

```
POST http://localhost:9008/user/login
{
  "mobile": "18516566511",
  "password": "123456"
}
```



2）启动交友微服务，通过postMan测试

```
PUT http://localhost:9010/friend/like/1030998915601797120对方的id/1
HEADER头信息 Authorization:Bearer 加上获取的token
```

3）相互加好友测试。



### 4.2.3 添加非好友（去掉）

需求：当用户点击不喜欢某人，将对方的ID记录不喜欢列表（黑名单）中

（1）构建实体类

```java
package com.tensquare.friend.po;

import javax.persistence.*;
import java.io.Serializable;

@Entity
@Table(name = "tb_nofriend")
//联合（复合）主键的类，如果该实体类的属性畜类联合主键属性外，还有其他属性，则必须加该注解，指定联合主键的类
@IdClass(NoFriend.class)
public class NoFriend implements Serializable {
    @Id
    @Column(length=20)
    private String userid;//用户id
    @Id
    @Column(length=20)
    private String friendid;//好友id


    public String getUserid() {
        return userid;
    }

    public void setUserid(String userid) {
        this.userid = userid;
    }

    public String getFriendid() {
        return friendid;
    }

    public void setFriendid(String friendid) {
        this.friendid = friendid;
    }

}
```

如果没有表，可以通过jpa自动建表在application.yml中修改配置配置如下：

```yaml
spring
  jpa
  	#自动建表
    generate-ddl: true
```



（2）创建数据访问接口

NoFriendRepository

```java
package com.tensquare.friend.dao;

import com.tensquare.friend.po.NoFriend;
import org.springframework.data.jpa.repository.JpaRepository;

/**
 * 非好友数据访问层
 */
public interface NoFriendRepository extends JpaRepository<NoFriend, String> {

}
```

（3）修改业务逻辑类FriendService，添加新的方法saveNoFriend

```java
 @Autowired
    private NoFriendRepository noFriendRepository;
    /**
     * 向不喜欢列表中添加记录
     * @param userid
     * @param friendid
     */
    public void saveNoFriend(String userid,String friendid){
        NoFriend noFriend=new NoFriend();
        noFriend.setUserid(userid);
        noFriend.setFriendid(friendid);
        noFriendRepository.save(noFriend);
    }
```

（4）修改FriendController的saveFriend方法

```java
/**
     * 添加好友
     *
     * @param friendid 对方用户ID
     * @param type     1：喜欢 0：不喜欢
     * @return
     */
    @PutMapping("/like/{friendid}/{type}")
    public ResultDTO add(@PathVariable String friendid, @PathVariable String type) {
        //获取当前登录人的信息
        Claims claims = (Claims) request.getAttribute("user_claims");
        //判断是否能获取到
        if (claims == null) {
            return new ResultDTO(false, StatusCode.ACCESSERROR, "无权访问，请先登录");
        }

       //判断是否是喜欢
        if (type.equals("1")) {
            //如果是喜欢，要添加好友
            if (friendService.saveFriend(claims.getId(), friendid) == 0) {
                return new ResultDTO(false, StatusCode.REPERROR, "已经添加过此好友");
            }
        } else {
            //不喜欢,添加非好友
            friendService.saveNoFriend(claims.getId(),friendid);//向不喜欢列表中添加记录
        }
        return new ResultDTO(true, StatusCode.OK, "操作成功");
    }
```

（5）测试：

1）启动用户微服务，登陆用户获取token

```
POST http://localhost:9008/user/login
{
  "mobile": "18516566511",
  "password": "123456"
}
```



2）启动交友微服务，通过postMan测试

```
PUT http://localhost:9010/friend/like/1030998915601797120对方的id/0
HEADER头信息 Authorization:Bearer 加上获取的token
```



### 4.2.4 删除好友

需求：去除好友

（1）FriendRepository新增方法定义

```java
    /**
     * 删除喜欢的好友
     * @param userid
     * @param friendid
     */
    @Modifying
    @Query("delete from Friend f where f.userid=?1 and f.friendid=?2")
    void deleteByUseridAndFriendid(String userid,String friendid);

    /**
     * 删除喜欢的好友(属性表达式)
     * @param userid
     * @param friendid
     */
    void removeByUseridAAndFriendid(String userid,String friendid);
```

（2）FriendService新增方法

```java

    /**
     * 删除好友
     * @param userid
     * @param friendid
     */
    @Transactional
    public void deleteFriend(String userid,String friendid){
        //删除关系
        friendRepository.deleteByUseridAndFriendid(userid,friendid);
        //判断对方是否已经添加你为好友，
        if (friendRepository.findCountByUseridAndFriendid(friendid, userid) > 0) {
            //修改对方对你的状态
            friendRepository.updateIslikeByUseridAndFriendid(friendid, userid, "0");
        }
        //向不喜欢表中添加记录
        //saveNoFriend(userid,friendid);
    }
```

（3）FriendController新增方法

```java
   /**
     * 删除好友
     * @param friendid
     * @return
     */
    @DeleteMapping("/{friendid}")
    public ResultDTO remove(@PathVariable String friendid){
        //获取当前登录信息
        Claims claims=(Claims)request.getAttribute("user_claims");
        if(claims==null){
            return new ResultDTO(false, StatusCode.ACCESSERROR,"无权访问，请登录");
        }
        friendService.deleteFriend(claims.getId(), friendid);
        return new ResultDTO(true, StatusCode.OK, "删除成功");
    }
```

测试



## 4.3 用户微服务-粉丝数与关注数的变更

### 4.3.1 变更粉丝数

（1）修改tensquare_user工程的UserRepository，增加方法定义

```java
    /**
     * 更新粉丝数
     * @param userid 用户ID
     * @param x 粉丝数
     */
    @Modifying
    @Query("update User u set u.fanscount=u.fanscount+?2  where u.id=?1")
    public void updateFanscountIncByUserid(String userid,int x);
```

（2）UserService增加方法

```java
	/**
	 *  更新粉丝数    
	 * @param x
	 */
	@Transactional
	public void  updateFanscountIncByUserid(String userid,int x){
		userRepository.updateFanscountIncByUserid(userid,x);
	}
```

（3）UserController增加方法

```java
	/**
	 *  增加粉丝数(给其他微服务调用)
	 * @param userid
	 * @param x
	 */
	@PostMapping("/incfans/{userid}/{x}")
	public void incFanscount(@PathVariable String userid,@PathVariable int x){
		userService.updateFanscountIncByUserid(userid,x);
	}
```

（4）测试

```
POST http://localhost:9008/user/incfans/1231245/1
或
POST http://localhost:9008/user/incfans/1231245/-1
```



### 4.3.2 变更关注数

（1）修改tensquare_user工程的UserRepository，增加方法定义

```java
    /**
     * 更新关注数
     *
     * @param userid 用户ID
     * @param x      粉丝数
     */
    @Modifying
    @Query("update User u set u.followcount=u.followcount+?2  where u.id=?1")
    void updateFollowcountIncByUserid(String userid, int x);
```

（2）UserService增加方法

```java
	/**
	 *  更新关注数    
	 * @param x
	 */
	@Transactional
	public void  updateFollowcountIncByUserid(String userid,int x){
		userRepository.updateFollowcountIncByUserid(userid,x);
	}
```

（3）UserController增加方法

```java

	/**
	 *  增加关注数    
	 * @param userid
	 * @param x
	 */
	@PostMapping("/incfollow/{userid}/{x}")
	public void incFollowcount(@PathVariable String userid,@PathVariable int x){
		userService.updateFollowcountIncByUserid(userid,x);
	}
```

（4）测试

```
POST http://localhost:9008/user/incfollow/1231245/1
或
POST http://localhost:9008/user/incfollow/1231245/-1
```



## 4.4 交友微服务调用用户微服务

### 4.4.1 喜欢某人更新用户数据

（1）在tensquare_friend模块创建 com.tensquare.friend.client包，包下创建接口

com.tensquare.friend.client.tensquare

```java
package com.tensquare.friend.client;

import org.springframework.cloud.openfeign.FeignClient;
import org.springframework.web.bind.annotation.PathVariable;
import org.springframework.web.bind.annotation.PostMapping;

@FeignClient("tensquare-user")
public interface UserClient {

    /**
     *  增加粉丝数
     * @param userid
     * @param x
     */
    @PostMapping("/user/incfans/{userid}/{x}")
    public void incFanscount(@PathVariable("userid") String userid, @PathVariable("x") int x);

    /**
     *  增加关注数
     * @param userid
     * @param x
     */
    @PostMapping("/user/incfollow/{userid}/{x}")
    public void incFollowcount(@PathVariable("userid") String userid,@PathVariable("x") int x);
}

```

@FeignClient注解用于指定从哪个服务中调用功能

@RequestMapping注解用于对被调用的微服务进行地址映射。

注意 @PathVariable注解一定要指定参数名称，否则出错

（2）修改FriendService ，注入UserClient

```java
    //注入FeignClient
    @Autowired
    private UserClient userClient;
```

修改saveFriend方法,增加对userClient方法的调用

```java
...
     public int saveFriend(String userid, String friendid) {
...
		//增加自己的关注数
        userClient.incFollowcount(userid,1);
        //增加对方的粉丝数
        userClient.incFanscount(friendid,1);
...
     return 1;
    }
...
```

（3）测试：添加好友，看关注数和粉丝数是否增加



### 4.4.2 删除好友更新用户数据

修改FriendService的deleteFriend方法 ,增加调用

```java
...
     public void deleteFriend(String userid,String friendid){
...
		//减少自己的关注数
        userClient.incFollowcount(userid,-1);
        //减少对方的粉丝数
        userClient.incFanscount(friendid,-1);
    }
...
```

（3）测试：删除好友，看关注数和粉丝数是否减少



# 总结

## 课程总结



## 面试问题

1）你在项目中使用SpringCloud的哪些组件?

服务注册组件Eureka 服务发现组件Feign 熔断器 网关 SpringCloudConfig (集中配置管理)、SpringCloudBus(消息总线)

2）你在项目中哪个业务场景使用到微服务间的调用

交友微服务 添加好友，在交友微服务中调用用户微服务的更改粉丝数与关注数的方法。