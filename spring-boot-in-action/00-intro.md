### 基本需要

- 项目结构，其中一个包含必要依赖的Maven或者Gradle构建文件，起码要有Spring MVC和Servlet API这些依赖
- 一个web.xml文件，或者有一个WebApplicationInitializer实现，其中声明了Spring的DispatcherServlet
- 一个启用了Spring MVC的Spring配置
- 一个控制类，响应HTTP请求
- 一个用于部署应用程序的Web应用服务器，如Tomcat

### Spring Boot精要

- 自动配置：针对很多Spring应用程序常见的应用功能，Spring Boot能自动提供相关配置
- 起步依赖：告知Spring Boot需要的功能供其引入需要的库
- 命令行页面：可选特性，只需写代码就能完成完整的应用程序，无需传统项目构建
- Actuator：能够深入运行中的Spring Boot应用程序

### 自动配置

```java
@Bean
public JdbcTemplate jdbcTemplate(DateSource dataSource) {
  return new JdbcTemplate(dateSource)
}
// 需要配置一个DataSource的Bean，这样才能满足依赖关系
@Bean
public DataSource dataSource() {
  return new EmbeddedDatabaseBuilder()
    .setType(EmbeddedDataSourceType.H2)
    .addScripts('schema.sql', 'data.sql')
    .build();
}
```

Spring Boot自动配置远不止嵌入式数据库和JdbcTemplate，涉及Java持久化API（Java Persistence API，JPA）、Thymeleaf模版、安全和Spring MVC

### 起步依赖

Spring Boot通过起步依赖为项目的依赖管理提供帮助。起步依赖就是特殊的Maven依赖和Gradle依赖，利用了传递依赖解析，把常用库聚合在一起，组成了几个为特定功能而定制的依赖

此外，Spring Boot的起步依赖还把开发者从“需要这些库的哪些版本”问题中解放。起步依赖引入的库的版本都是经过测试的，不会出现不兼容的情况

### 命令行界面

Spring Boot CLI利用起步依赖和自动配置，检测使用了哪些类，它知道要向Classspath中添加哪些起步依赖才能让它运转起来。一旦那些依赖出现在Classpath中，一系列自动配置就会接踵而来保证启用DispatcherSevlet和Spring MVC，这样控制器就能响应请求了

Spring Boot CLI是非必需部分

### Actuator

其他几个部分是为了简化Spring开发，而Actuator则要提供运行时检视应用程序内部情况的能力。Actuator可以查看应用程序的内部情况：

- Spring应用程序上下文里配置的Bean
- Spring Boot的自动配置做的决策
- 应用程序取到的环境变量、系统属性、配置属性和命令行参数
- 应用程序里线程的当前状态
- 应用程序最近处理的HTTP请求的追踪情况
- 各种和内存用量、垃圾回收、Web请求以及数据源用量相关的指标

Actuator通过Web端点和shell界面向外界提供信息

### Spring Boot不是什么

- 不是应用服务器。Spring Boot在应用程序里嵌入了Servlet容器（Tomcat、Jetty或Undertow），以此实现此功能。但这是内嵌的Servlet容器提供的攻讷讷个，而不是Spring Boot实现的
- Spring Boot也没有显示诸如JPA或JMS（Java Message Service，Java消息服务）之类的企业级Java规范。它的确支持不少企业级java规范，但是要在Spring里自动配置支持这些特性的Bean
- Spring Boot没有引入任何形式的代码生成，而是利用了Spring 4的条件化配置特性，Bean配置





