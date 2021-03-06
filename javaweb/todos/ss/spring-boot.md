# Spring Boot

* [完整官方文档](https://docs.spring.io/spring-boot/docs/current-SNAPSHOT/reference/htmlsingle/#getting-started)
* Spring Boot内置了容器, 所以不再需要安装Tomcat或Jetty
* Spring Boot主要包含4个方面
    - starter
        - 有多种starter, 每种starter针对特定的需求提供了依赖的组合, 引入一个starter就相当于引入了相关的依赖
        - 为了简化以前Spring开发中繁琐的模板式配置文件
    - 自动配置
        - 自动配置免去了Bean的配置
    - CLI
        - 用于运行Groovy脚本
    - Actuator
        - 管理端点
        - 合理的异常处理, 默认`/error`映射端点
        - 获取应用信息的`/info`端点
        - Spring Security的审计事件框架


## 安装Spring Boot CLI

* Spring Boot CLI不是必须安装的, 但它能够运行Groovy脚本, 帮助快速调试Spring Boot
* 安装要求:
    - JDK 1.6+
* 安装方式: 
    - Homebrew: 
        - 先升级homebrew, 否则可能找不到CLI: `brew update`
        - `brew tap pivotal/tap`
        - `brew install springboot`
    - 手动安装
        - 下载压缩包, 解压后根据`INSTALL.txt`进行操作
        - 将`{SPRING_HOME}/bin`目录添加到`PATH`中
* 验证安装:
    - `spring --version`


## 创建简单的Spring Boot项目

* [官网](https://projects.spring.io/spring-boot/)


* maven添加web starter

```xml
<parent>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-parent</artifactId>
    <version>1.5.8.RELEASE</version>
</parent>
<dependencies>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-web</artifactId>
    </dependency>
</dependencies>
```

* 创建Controller

```java
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.EnableAutoConfiguration;
import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.ResponseBody;

@Controller
@EnableAutoConfiguration
public class HelloController {

    // 定义处理根路径的方法
    @RequestMapping("/")
    @ResponseBody
    String hello() {
        return "hello spring boot!";
    }

    // 运行入口
    public static void main(String[] args) {
        SpringApplication.run(HelloController.class, args);
    }
}
```

* 运行该程序即可, 默认内置了Tomcat, 启动后监听8080端口
    - 可以使用Run按钮
    - 也可以在项目根目录执行: `mvn spring-boot:run`

```
  .   ____          _            __ _ _
 /\\ / ___'_ __ _ _(_)_ __  __ _ \ \ \ \
( ( )\___ | '_ | '_| | '_ \/ _` | \ \ \ \
 \\/  ___)| |_)| | | | | || (_| |  ) ) ) )
  '  |____| .__|_| |_|_| |_\__, | / / / /
 =========|_|==============|___/=/_/_/_/
 :: Spring Boot ::        (v1.5.8.RELEASE)

2017-10-21 22:55:39.664  INFO 24956 --- [           main] c.l.site.controller.HelloController      : Starting HelloController on fantasymakerdeMacBook-Pro.local with PID 24956 (/Users/fantasymaker/develop/projects/javaweb/site/target/classes started by fantasymaker in /Users/fantasymaker/develop/projects/javaweb/site)
2017-10-21 22:55:39.668  INFO 24956 --- [           main] c.l.site.controller.HelloController      : No active profile set, falling back to default profiles: default
2017-10-21 22:55:39.708  INFO 24956 --- [           main] ationConfigEmbeddedWebApplicationContext : Refreshing org.springframework.boot.context.embedded.AnnotationConfigEmbeddedWebApplicationContext@75881071: startup date [Sat Oct 21 22:55:39 CST 2017]; root of context hierarchy
2017-10-21 22:55:40.632  INFO 24956 --- [           main] s.b.c.e.t.TomcatEmbeddedServletContainer : Tomcat initialized with port(s): 8080 (http)
2017-10-21 22:55:40.641  INFO 24956 --- [           main] o.apache.catalina.core.StandardService   : Starting service [Tomcat]
2017-10-21 22:55:40.642  INFO 24956 --- [           main] org.apache.catalina.core.StandardEngine  : Starting Servlet Engine: Apache Tomcat/8.5.23
2017-10-21 22:55:40.690  INFO 24956 --- [ost-startStop-1] o.a.c.c.C.[Tomcat].[localhost].[/]       : Initializing Spring embedded WebApplicationContext
2017-10-21 22:55:40.691  INFO 24956 --- [ost-startStop-1] o.s.web.context.ContextLoader            : Root WebApplicationContext: initialization completed in 985 ms
2017-10-21 22:55:40.771  INFO 24956 --- [ost-startStop-1] o.s.b.w.servlet.ServletRegistrationBean  : Mapping servlet: 'dispatcherServlet' to [/]
2017-10-21 22:55:40.774  INFO 24956 --- [ost-startStop-1] o.s.b.w.servlet.FilterRegistrationBean   : Mapping filter: 'characterEncodingFilter' to: [/*]
2017-10-21 22:55:40.774  INFO 24956 --- [ost-startStop-1] o.s.b.w.servlet.FilterRegistrationBean   : Mapping filter: 'hiddenHttpMethodFilter' to: [/*]
2017-10-21 22:55:40.774  INFO 24956 --- [ost-startStop-1] o.s.b.w.servlet.FilterRegistrationBean   : Mapping filter: 'httpPutFormContentFilter' to: [/*]
2017-10-21 22:55:40.774  INFO 24956 --- [ost-startStop-1] o.s.b.w.servlet.FilterRegistrationBean   : Mapping filter: 'requestContextFilter' to: [/*]
2017-10-21 22:55:41.025  INFO 24956 --- [           main] s.w.s.m.m.a.RequestMappingHandlerAdapter : Looking for @ControllerAdvice: org.springframework.boot.context.embedded.AnnotationConfigEmbeddedWebApplicationContext@75881071: startup date [Sat Oct 21 22:55:39 CST 2017]; root of context hierarchy
2017-10-21 22:55:41.074  INFO 24956 --- [           main] s.w.s.m.m.a.RequestMappingHandlerMapping : Mapped "{[/]}" onto java.lang.String com.lixplor.site.controller.HelloController.hello()
2017-10-21 22:55:41.077  INFO 24956 --- [           main] s.w.s.m.m.a.RequestMappingHandlerMapping : Mapped "{[/error]}" onto public org.springframework.http.ResponseEntity<java.util.Map<java.lang.String, java.lang.Object>> org.springframework.boot.autoconfigure.web.BasicErrorController.error(javax.servlet.http.HttpServletRequest)
2017-10-21 22:55:41.077  INFO 24956 --- [           main] s.w.s.m.m.a.RequestMappingHandlerMapping : Mapped "{[/error],produces=[text/html]}" onto public org.springframework.web.servlet.ModelAndView org.springframework.boot.autoconfigure.web.BasicErrorController.errorHtml(javax.servlet.http.HttpServletRequest,javax.servlet.http.HttpServletResponse)
2017-10-21 22:55:41.097  INFO 24956 --- [           main] o.s.w.s.handler.SimpleUrlHandlerMapping  : Mapped URL path [/webjars/**] onto handler of type [class org.springframework.web.servlet.resource.ResourceHttpRequestHandler]
2017-10-21 22:55:41.097  INFO 24956 --- [           main] o.s.w.s.handler.SimpleUrlHandlerMapping  : Mapped URL path [/**] onto handler of type [class org.springframework.web.servlet.resource.ResourceHttpRequestHandler]
2017-10-21 22:55:41.123  INFO 24956 --- [           main] o.s.w.s.handler.SimpleUrlHandlerMapping  : Mapped URL path [/**/favicon.ico] onto handler of type [class org.springframework.web.servlet.resource.ResourceHttpRequestHandler]
2017-10-21 22:55:41.205  INFO 24956 --- [           main] o.s.j.e.a.AnnotationMBeanExporter        : Registering beans for JMX exposure on startup
2017-10-21 22:55:41.260  INFO 24956 --- [           main] s.b.c.e.t.TomcatEmbeddedServletContainer : Tomcat started on port(s): 8080 (http)
2017-10-21 22:55:41.270  INFO 24956 --- [           main] c.l.site.controller.HelloController      : Started HelloController in 2.082 seconds (JVM running for 2.817)
```


## 基础

### 打包方式

* `jar`包方式:
    - 将`pom.xml`中的`<packaging>`参数修改为`jar`
    - Spring Boot项目由于内置了web服务器, 因此可以单独打为一个jar包, 使用`java -jar xxx.jar`方式作为一个应用运行
    - 操作:
        - 定义`Application`类作为应用入口, 在类上使用`@SpringBootApplication`注解
        - 类中定义`main()`方法, 执行`SpringApplication.run(Application.class, args);`来启动应用
* `war`包方式:
    - 将`pom.xml`中的`<packaging>`参数修改为`jar`
    - 也可以选择打为war包, 将程序放入单独的web服务器中运行, 但需要做一些额外配置
    - 操作:
        - `Application`
            - 定义`Application`类作为应用入口, 在类上使用`@SpringBootApplication`注解
            - 类中定义`main()`方法, 执行`SpringApplication.run(Application.class, args);`来启动应用
        - `SpringBootStartAplication`
            - 定义`SpringBootStartAplication`类, 继承`SpringBootServletInitializer`
            - 重写`SpringApplicationBuilder configure(SpringApplicationBuilder builder)`方法, 其中执行`return builder.sources(App.class);`
        - 在`pom.xml`中的`spring-boot-starter-web`中将web服务器插件移除, 并添加额外的`javax.servlet-api`依赖
* 执行`mvn clean package`打包

#### jar包方式

```xml
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/maven-v4_0_0.xsd">
  <modelVersion>4.0.0</modelVersion>
  <groupId>com.xxx</groupId>
  <artifactId>xxx</artifactId>
  <packaging>jar</packaging>
  <version>1.0-SNAPSHOT</version>
  <name>site Maven Webapp</name>
  <url>http://maven.apache.org</url>

  <parent>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-parent</artifactId>
    <version>1.5.8.RELEASE</version>
  </parent>

  <dependencies>
    <dependency>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-starter-web</artifactId>
    </dependency>
    <dependency>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-starter-freemarker</artifactId>
    </dependency>
  </dependencies>
  <build>
    <finalName>xxx</finalName>
  </build>
</project>
```

```java
@SpringBootApplication
@EnableAutoConfiguration
public class App {

    // 运行入口
    public static void main(String[] args) {
        SpringApplication.run(App.class, args);
    }
}
```


#### war包方式

```xml
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/maven-v4_0_0.xsd">
  <modelVersion>4.0.0</modelVersion>
  <groupId>com.xxx</groupId>
  <artifactId>xxx</artifactId>
  <packaging>war</packaging>
  <version>1.0-SNAPSHOT</version>
  <name>site Maven Webapp</name>
  <url>http://maven.apache.org</url>

  <parent>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-parent</artifactId>
    <version>1.5.8.RELEASE</version>
  </parent>

  <dependencies>
    <dependency>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-starter-web</artifactId>
      <!-- 移除嵌入式tomcat插件 -->
      <exclusions>
        <exclusion>
          <groupId>org.springframework.boot</groupId>
          <artifactId>spring-boot-starter-tomcat</artifactId>
        </exclusion>
      </exclusions>
    </dependency>
    <dependency>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-starter-freemarker</artifactId>
    </dependency>
    <!-- 添加servlet api -->
    <dependency>
      <groupId>javax.servlet</groupId>
      <artifactId>javax.servlet-api</artifactId>
      <version>3.1.0</version>
      <scope>provided</scope>
    </dependency>
  </dependencies>
  <build>
    <finalName>xxx</finalName>
  </build>
</project>
```

```java
@SpringBootApplication
@EnableAutoConfiguration
public class App {

    // 运行入口
    public static void main(String[] args) {
        SpringApplication.run(App.class, args);
    }
}

public class SpringBootStartApplication extends SpringBootServletInitializer {

    @Override
    protected SpringApplicationBuilder configure(SpringApplicationBuilder builder) {
        return builder.sources(App.class);
    }
}
```


### 模板引擎热部署

* 加入springboot的maven插件

```xml
<buid>
  <plugin>
    <plugin>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-maven-plugin</artifactId>
      <configuration>
        <fork>true<fork>
      </configuration>
    </plugin>
  </plugin>
</build>
```

* 在`application.properties`中禁用模板引擎的缓存

```properties
spring.freemarker.cache=false
spring.freemarker.settings.template_update_delay=0
```

* 修改IDEA设置
    - 打开`Settings`, `Build, Execution, Deployment`, `Compliler`, 勾选`Build project automatically`
    - 打开`Help`, `Find Action...`, 输入`Registry...`, 找到`compiler.automake.allow.when.app.running`勾选上
    - 重启IDEA
