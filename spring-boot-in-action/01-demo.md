### 初始化项目结构

整个项目结构遵循传统的Maven或Gradle项目的布局，即主要应用程序代码位于src/man/java目录里，资源都在src/main/resources目录里，测试代码则在src/test/java目录里

- build.gradle：Gradle构建说明文件
- ReadingListApplication.java：应用程序的启动引导类（bootstrap class）也是主要的Spring配置类
- application.properties：用于配制应用程序和Spring Boot的属性
- ReadingListApplicationTests.java：一个基本的集成测试类

### `ReadingListApplication.java`

1. 启动引导Spring

两个作用：配置和启动引导。首先这是主要的Spring配置类，进行少量配置来启动自动配置

```java
package readlinglist;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

@SpringBootApplication
public class ReadingListApplication {
  public static void main(String[] args) {
    SpringApplication.run(ReadingListApplication.class, args);
  }
}
```

@SpringBootApplication开启了Spring的组件扫描和Spring Boot的自动配置功能。其由三个有用的注解组合在一起：

- Spring的@Configuration：标明该类使用Spring基于Java的配置
- Spring的@componentScan：启用组件扫描，这样Web控制器类和其他组件才能被自动发现并注册为Spring的应用程序上下文里的Bean
- Spring Boot的@EnableAutoConfiguration：也可以成为@Abracadabra，这一行配置开启了Spring Boot自动配置，不用写配置（abracadabra意思为咒语）

ReadingListApplication还是一个启动引导类，要运行Spring Boot应用程序的几种方式，其中包含传统的WAR文件部署。但这里的main方法可以在命令行里把该应用程序当作一个可执行JAR文件来运行。这里向`SpringApplication.run()`传递了一个ReadingListApplication类的引用，还有命令行参数，通过这些东西启动应用程序

2. 测试Spring Boot应用程序

Initializer提供了一个测试类的骨架，可以基于它视为应用程序编写测试。但ReadingListApplicationTests不只是用于测试的占位符，它还是一个例子，告诉如何为Spring Boot应用程序编写测试

```java
package readinglist;

import org.junit.Test;
import org.junit.Test;
import org.junit.runner.RunWith;
import org.springframework.boot.test.SpringApplicationConfiguration; 
import org.springframework.test.context.junit4.SpringJUnit4ClassRunner; import org.springframework.test.context.web.WebAppConfiguration;

import readinglist.ReadingListApplication;

@RunWith(SpringJUnit4ClassRunner.class)
@SpringApplicationConfiguration(
  classes = ReadingListApplication.class
) // 通过Spring Boot加载上下文
@WebAppConfiguration
public class ReadingListApplicationTests {
  
  @Test
  public void contextLoads() {
    // 测试加载的上下文
  }
}
```

一个典型的Spring集成测试会用@Configuration注解标识如何加载Spring的应用程序上下文。RedingListApplicationTests使用@SpringApplicationConfiguration注解从ReadingListApplication配置类里加载Spring应用程序上下文

contextLoads是一个空方法，但这个空方法足以证明应用程序上下文的加载没有问题。

3. 配置应用程序属性

Initializer生成的application.properties文件是一个空文件，这个文件是可选的

要注意的是，完全不用告诉Spring Boot为应用加载application.properties，主要它存在就会被加载，Spring和应用程序代码都能获取其中的属性

### 指定基于功能的依赖

Spring Boot通过提供众多起步依赖境地项目依赖的复杂度。起步依赖本质上是一个maven项目对象模型（Project Object Model，POM），定义了对其他库的传递依赖，这些东西加起来即支持某项功能。这些起步依赖的命名都暗示了它们提供的某种或某类的功能

#### 覆盖起步依赖引入的传递依赖

在Maven中，可以用exclusions元素来排除传递依赖

```xml
<dependency>
	<groupId>org.springframework.boot</groupId>
  <artifactId>spring-boot-starter-web</artifactId>
  <exclusions>
    <exclusion>
      <groupId>com.fasterxml.jackson.core</groupId>
    </exclusion>
  </exclusions>
</dependency>
```

也许项目需要Jackson，但需要另一个版本的Jackson来进行构建，而不是Web起步依赖里的那个。在Mavin里，可以直接在pom.xml中表达

```xml
<dependency>
	<groupId>com.fasterxml.jackson.core</groupId>
  <artifactId>jackson-databind</artifactId>
	<version>2.4.3</version>
</dependency>
```

### 使用自动配置

Spring Boot的自动配置是一个运行时（更准确地说，是应用程序启动时）的过程。下面这些情况都是Spring Boot自动配置要考虑的：

- Spring的JdbcTemplate是不是在Classpath里？如果是，并且有DataSource的Bean，则自动配置一个JdbcTemplate的Bean
- Thymeleaf是不是在Classpath里？如果是，则配置Thymeleaf的模版解析器、视图解析器以及模版引擎
- Spring Security是不是在Classpath里？如果是，则进行一个非常基本的安全设置

#### 专注于应用程序功能

- 定义领域模型

  ```java
  package readinglist;
  
  import javax.persistence.Entity;
  import javax.persistence.GeneratedValue;
  import javax.persistence.GenerationType;
  import javax.persistence.Id;
  
  @Entity
  public class Book {
    @Id
    @GeneratedValue(strategy=GenerationType.AUTO)
    private Long id;
    private String reader;
    private String isbn;
    private String title;
    private String author;
    private String description;
    
    public Long getId() {
      return id;
  	}
    public void setId(Long id) {
      this.id = id;
  	}
    public String getReader() {
      return reader;
  	}
    public void setReader(String reader) {
      this.reader = reader;
  	}
    public String getIsbn() {
      return isbn;
  	}
    public void setIsbn(String isbn) {
      this.isbn = isbn;
  	}
    public String getTitle() {
      return title;
  	}
    public void setTitle(String title) {
      this.title = title;
  	}
    public String getAuthor() {
      return author;
  	}
    public void setAuthor(String author) {
      this.author = author;
  	}
    public String getDescription() {
      return description;
  	}
    public void setDescription(String description) {
      this.description = description;
  	}
  }
  ```

  @Entity注解表明它是一个JPA实体，id属性加了@Id和@GeneratedValue注解，说明这个字段是实体的唯一标识，并且这个字段的值是自动生成的

- 定义仓库接口

  扩展一下Spring Data JPA的JpaRepository接口

  ```java
  package readinglist;
  
  import java.util.List;
  import org.springframework.data.jpa.repository.JpaRepository;
  
  public interface ReadingListRepository extends JpaRepository<Book, Long> {
    List<Book> findByReader(string reader);
  }
  ```

  通过扩展JpaRepository，ReadingListRepository直接继承了18歌执行常用持久化操作的方法，JpaRepository是个泛性接口，有两个参数：仓库操作的领域对象类型，以及ID属性的类型。此外还添加了一个findByReader()的方法，可以根据读者的用户名来查找阅读列表

- 创建web界面

  ```java
  package readinglist;
  
  import org.springframework.beans.factory.annotation.Autowired;
  import org.springframework.stereotype.Controller;
  import org.springframework.ui.Model;
  import org.springframework.web.bind.annotation.PathVariable;
  import org.springframework.web.bind.annotation.RequestMapping;
  import org.springframework.web.bind.annotation.RequestMethod;
  
  import java.util.List;
  
  @Controller
  @RequestMapping("/")
  public class ReadingListController {
    private ReadingListRepository readingListRepository;
    
    @Autowired
    public ReadingListController(ReadingListRepository readingListRepository) {
      this.readingListRepository = readingListRepository;
    }
    
    @RequestMapping(value = "/{reader}", method=RequestMethod.GET)
    public String readersBooks(
    	@PathVariable("reader") String reader,
      Model model
    ) {
      List<Book> readingList = readingListRepository.findByReader(reader);
      if(readingList != null) {
        model.addAttribute("books", readingList);
      }
      return "readingList";
    }
    
    @RequestMapping(value = "/{reader}", method=RequestMethod.POST)
    public String addToReadingList(
    	@PathVariable("reader") String reader,
      Book book
    ) {
      book.setReader(reader);
      readingListRepository.save(book);
      return 'redirect:/{reader}';
    }
  }
  ```

  ReadingListController使用了@Controller注解，这样组件扫描会自动将其注册为Sping应用程序上下文里的一个Bean。它还用了@RequestMapping注解，将其中所有的处理器方法都映射到这个“/“这个URL路径上

  控制器有两个方法：

  1. `readersBooks()`：处理/{reader}上的HTTP GET请求，根据路径里指定的读者，从【通过控制器的构造器注入的仓库】获取Book列表，随后将这个列表塞入模型，用的键是books，最后返回readingList作为呈现模型的视图逻辑名称
  2. `addToReadingList()`：处理/{reader}上的HTTP POST请求，将请求正文里的数据绑定到一个Book对象上。该方法是Book对象的reader属性设置为读者的姓名，随后通过仓库的save()方法保存修改后的Book对象，最后重定向到/{reader}（控制器中的另一个方法会处理该请求）

   `readerBooks()`方法最后返回readingList作为逻辑视图名，为此必须创建该视图。接下来就在src/main/resources/templates创建一个名为readingList.html的文件

  ```html
  <html>
    <head>
      <title>Reading List</title>
      <link ref="stylesheet" th:herf="@{/style.css}"></link>
    </head>
  	<body>
    	<h2>Your Reading List</h2>
      <div th:unless="${#lists.isEmpty(books)}">
        <dl th:each="book : ${books}">
          <dt class="bookHeadline">
            <span th:text="${book.title}">Title</span> by
            <span th:text="${book.author}">Author</span>
            (ISBN: <span th:text="${book.isbn}">ISBN</span>)
          </dt>
          <dd class="bookDescription">
            <span th:if="${book.description}"
                  th:text="${book.description}">Description</span>
            <span th:if="${book.description eq null}">
                  No description available</span>
  				</dd> 
        </dl>
      </div>
      <div th:if="${#lists.isEmpty(books)}">
        <p>You have no books in your book list</p>
      </div>
      
      <hr/>
      
      <h3>Add a book</h3>
      <form method="POST">
        <label for="title">Title:</label>
          <input type="text" name="title" size="50"></input><br/>
        <label for="author">Author:</label>
          <input type="text" name="author" size="50"></input><br/>
        <label for="isbn">ISBN:</label>
          <input type="text" name="isbn" size="15"></input><br/>
        <label for="description">Description:</label><br/>
          <textarea name="description" cols="80" rows="5">
          </textarea><br/>
        <input type="submit"></input>
      </form>
    
  	</body>
  </html>
  ```

  #### 运行应用程序

  在向应用程序加入Spring Boot时，有个名为spring-boot-autoconfigure的JAR文件，其中包含了 很多配置类。每个配置类都在应用程序的Classpath里，都有机会为应用程序的配置添砖加瓦。这 些配置类里有用于Thymeleaf的配置，有用于Spring Data JPA的配置，有用于Spiring MVC的配置， 还有很多其他东西的配置，你可以自己选择是否在Spring应用程序里使用它们。

  所有这些配置如此与众不同，原因在于它们利用了Spring的条件化配置，这是Spring 4.0引入 的新特性。条件化配置允许配置存在于应用程序中，但在满足某些特定条件之前都忽略这个配置。

  在Spring里可以方便地编写自己的条件，所要做的就是实现Condition接口，覆盖它的matches()方法

  ```java
  package readinglist;
  
  import org.springframework.context.annotation.Condition;
  import org.springframework.context.annotation.ConditionContext; 
  import org.springframework.core.type.AnnotatedTypeMetadata;
  
  public class JdbcTemplateCondition implement Condition {
    @override
    public boolean matches(
      ConditionContext context, 
      AnnotatedTypeMetadata metadata
    ) {
      try {
        context.getClassLoader()
          .loadClass("org.springframework.jdbc.core.JdbcTemplate");
        return true;
      } catch(Exception e) {
        return false;
      }
    }
  }
  ```

  当用Java声明Bean的时候，可以使用这个自定义条件类

  ```java
  @Conditional(JdbcTemplateCondition.class)
  public MyService myService() {
    ...
  }
  ```

  这里，只有当JdbcTemplateCondition类的条件成立时才会创建MyService这个Bean。**也就是说MyService Bean创建的条件是Classpath里有JdbcTemplate。否则，这个Bean的声明就会被忽略掉**

  Spring Boot定了很多更有趣的条件，并把它们运用到了配置类上，这些配置类构成了Spring Boot的自动配置。Spring Boot运用条件化配置的方法是，定义多个特殊的条件化注解，并将它们用到配置类上

  - @ConditionalOnBean：配置了某个特定Bean
  - @ConditionalOnMissingBean：没有配置特定的Bean
  - @ConditionalOnClass：Classpath里有指定的类
  - @ConditionalOnMissingClass：Classpath里缺少指定的类
  - @ConditionalOnExpression：给定的Spring Expression Language（SpEL）表达式计算结果是true
  - @ConditionalOnJava：Java的版本匹配特定值或者一个范围值
  - @ConditionalOnJndi：参数中给定的JNDI位置必须存在一个，如果没有给参数，则要有JNDI InitialContext
  - @ConditionalOnProperty：指定的配置属性要有一个明确的值
  - @ConditionalOnResource：Classpath里有指定的资源
  - @ConditionalOnWebApplication：这是一个Web应用程序
  - @ConditionalOnNotWebApplication：这不是一个Web应用程序

我们可以看一下DataSourceAutoConfiguration里的这个片段(这是Spring Boot自动配置库 的一部分):

```java
@Configuration
@ConditionalOnClass({ DataSource.class, EmbeddedDatabaseType.class }) @EnableConfigurationProperties(DataSourceProperties.class)
@Import({ 
  Registrar.class, 
  DataSourcePoolMetadataProvidersConfiguration.class 
}) 
public class DataSourceAutoConfiguration {
 ... 
}
```

DataSourceAutoConfiguration添加了@Configuration注解，它从其他配置类里导入了一些额外配置，还自己定义了一些Bean。最重要的是，DataSourceAutoConfiguration上添加了@ConditionalOnClass注解，要求Classpath里必须要有DataSource和EmbeddedDatabaseType。如果它们不存在，条件就不成立，DataSourceAutoConfiguration 提供的配置都会被忽略掉。

DataSourceAutoConfiguration里嵌入了一个JdbcTemplateConfiguration类，自动配置了一个JdbcTemplate Bean:

```java
@Configuration @Conditional(DataSourceAutoConfiguration.DataSourceAvailableCondition.class)
protected static class JdbcTemplateConfiguration {
  @Autowired(required = false)
  private DataSource dataSource;
  @Bean
  @ConditionalOnMissingBean(JdbcOperations.class)
  public JdbcTemplate jdbcTemplate() {
    return new JdbcTemplate(this.dataSource);
  }
	...
}
```

JdbcTemplateConfiguration使用了@Conditional注解，判断DataSourceAvailable- Condition条件是否成立——基本上就是要有一个DataSource Bean或者要自动配置创建一个。 假 设 有DataSource Bean ， 使 用 了@Bean注 解 的jdbcTemplate()方 法 会 配 置 一 个 JdbcTemplate Bean。这个方法上还加了@ConditionalOnMissingBean注解，因此只有在不 存在JdbcOperations(即JdbcTemplate实现的接口)类型的Bean时，才会创建JdbcTemplate Bean。

自动配置会做出以下配置决策，它们和之前的例子息息相关。

- 因为Classpath里有H2，所以会创建一个嵌入式的H2数据库Bean，它的类型是javax.sql.DataSource，JPA实现(Hibernate)需要它来访问数据库。
- 因为Classpath里有Hibernate(Spring Data JPA传递引入的)的实体管理器，所以自动配置会 配 置 与 Hibernate 相 关 的 Bean ， 包 括 Spring 的 LocalContainerEntityManager-FactoryBean和JpaVendorAdapter。
- 因为Classpath里有Spring Data JPA，所以它会自动配置为根据仓库的接口创建仓库实现。
- 因为Classpath里有Thymeleaf，所以Thymeleaf会配置为Spring MVC的视图，包括一个Thymeleaf的模板解析器、模板引擎及视图解析器。视图解析器会解析相对于Classpath根目录的/templates目录里的模板。
- 因为Classpath里有Spring MVC(归功于Web起步依赖)，所以会配置Spring的DispatcherServlet并启用Spring MVC。
- 因为这是一个Spring MVC Web应用程序，所以会注册一个资源处理器，把相对Classpath根目录的/static目录里的静态内容提供出来。(这个资源处理器还能理/public、/resources和/META-INF/resources的静态内容。)
- 因为Classpath里有Tomca(t 通过Web起步依赖传递引用)，所以会启动一个嵌入式的Tomcat容器，监听8080端口。