### 覆盖Spring Boot自动配置

当你在应用程序里添加安全特性时，自动配置做得还不够好。安全配置并不是放之四海而皆准的，围绕应用程序安全有很多决策要做，Spring Boot不能替你做决定。 虽然Spring Boot为安全提供了一些基本的自动配置，但是你还是需要自己覆盖一些配置以满足特 定的安全要求。

#### 保护应用程序

```xml
<dependency> 
  <groupId>org.springframework.boot</groupId> 			
  <artifactId>spring-boot-starter-security</artifactId>
</dependency>
```

引入spring-security。Security起步依赖在应用程序的Classpath里添加了Spring Secuirty(和其他一些东西)。Classpath里 有Spring Security后，自动配置就能介入其中创建一个基本的Spring Security配置

### 创建自定义的安全配置

覆盖自动配置很简单，就当自动配置不存在，直接显式地写一段配置。这段显式配置的形式 不限，Spring支持的XML和Groovy形式配置都可以。在Spring Security的场景下，可以写一个扩展了WebSecurityConfigurerAdapter的配置类

```java
package readinglist;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.context.annotation.Configuration;
import org.springframework.security.config.annotation.authentication.
builders.AuthenticationManagerBuilder; 
import org.springframework.security.config.annotation.web.builders.
HttpSecurity; 
import org.springframework.security.config.annotation.web.configuration.
EnableWebSecurity;
import org.springframework.security.config.annotation.web.configuration.
WebSecurityConfigurerAdapter; 
import org.springframework.security.core.userdetails.UserDetails;
import org.springframework.security.core.userdetails.UserDetailsService; import org.springframework.security.core.userdetails.UsernameNotFoundException;

@Configuration
@EnableWebSecurity
public class SecurityConfig extends WebSecurityConfigurerAdapter {
  
  @Autowired
	private ReaderRepository readerRepository;
  
  @Override
	protected void configure(HttpSecurity http) throws Exception {
    http
    	.authorizeRequests()
      .antMatchers("/").access("hasRole('READER')")
      .antMatchers("/**").permitAll()
    	.and()
      .formLogin()
      .loginPage("/login")
      .failureUrl("/login?error=true");
  }
  
  @Override
	protected void configure(AuthenticationManagerBuilder auth) throws Exception {
    auth
    	.userDetailsService(new UserDetailsService() {
        @Override
        public UserDetails loadUserByUsername(String username)
            throws UsernameNotFoundException {
          return readerRepository.findOne(username);
        } 
  	});
	}
}
```

扩展了WebSecurityConfigurerAdapter的配置类可以覆盖两个不同的configure()方 法。在SecurityConfig里，第一个configure()方法指明，“/”(ReadingListController 的方法映射到了该路径)的请求只有经过身份认证且拥有READER角色的用户才能访问。其他的所有请求路径向所有用户开放了访问权限。这里还将登录页和登录失败页(带有一个error属性) 指定到了/login

Spring Security为身份认证提供了众多选项，后端可以是JDBC(Java Database Connectivity)、 LDAP和内存用户存储。在这个应用程序中，我们会通过JPA用数据库来存储用户信息。第二个 configure()方法设置了一个自定义的UserDetailsService，这个服务可以是任意实现了 UserDetailsService的类，用于查找指定用户名的用户

```java
package readinglist;
import org.springframework.data.jpa.repository.JpaRepository;

public interface ReaderRepository extends JpaRepository<Reader, String> {
  
}
```

和BookRepository类似，无需自己实现ReaderRepository。这是因为它扩展了 JpaRepository，Spring Data JPA会在运行时自动创建它的实现。提供了18个操作Reader实体的方法

```java
package readinglist;
import java.util.Arrays;
import java.util.Collection;
import javax.persistence.Entity;
import javax.persistence.Id;
import org.springframework.security.core.GrantedAuthority;
import org.springframework.security.core.authority.SimpleGrantedAuthority; import org.springframework.security.core.userdetails.UserDetails;

@Entity
public class Reader implements UserDetails {
  private static final long serialVersionUID = 1L;
  
  @Id
  private String username;
  private String fullname;
  private String password;
  
  public String getUsername() {
    return username;
  }
  public void setUsername(String username) {
    this.username = username;
  }
  public String getFullname() {
    return fullname;
  }
  public void setFullname(String fullname) {
    this.fullname = fullname;
  }
  public String getPassword() {
    return password;
  }
  public void setPassword(String password) {
    this.password = password;
  }
  // UserDetails methods
  @Override
	public Collection<? extends GrantedAuthority> getAuthorities() { 
    return Arrays.asList(new SimpleGrantedAuthority("READER"));
	}
  @Override
  public boolean isAccountNonExpired() {
    return true;
  }
  @Override
  public boolean isAccountNonLocked() {
  	return true;
  }
  @Override
  public boolean isCredentialsNonExpired() {
    return true;
  }
  @Override
  public boolean isEnabled() {
    return true;
  }
}
```

Reader用了@Entity注解，所以这是一个JPA实体。此外，它的username字段 上有@Id注解，表明这是实体的ID。这个选择无可厚非，因为username应该能唯一标识一个 Reader

Reader实现了UserDetails接口以及其中的方法，这样Reader就能代表 Spring Security里的用户了。getAuthorities()方法被覆盖过了，始终会为用户授予READER 权限isAccountNonExpired()、isAccountNonLocked()、isCredentialsNonExpired() 和isEnabled()方法都返回true，这样读者账户就不会过期，不会被锁定，也不会被撤销

### 通过属性文件外置配置

Spring Boot自动配置的Bean提供了300多个用于微调的属性。当需要调整设置时，只 要在环境变量、Java系统属性、JNDI(Java Naming and Directory Interface)、命令行参数或者属 性文件里进行指定就好了

在命令行里运行阅 读列表应用程序时，Spring Boot有一个ascii-art Banner。如果你想禁用这个Banner，可以将 spring.main.show-banner属性设置为false。有几种实现方式，其中之一就是在运行应用程 序的命令行参数里指定:

`$ java -jar readinglist-0.0.1-SNAPSHOT.jar --spring.main.show-banner=false`

另一种方式是创建一个名为application.properties的文件，包含如下内容:

`spring.main.show-banner=false`

也可以创建名为application.yml的YAML文件

```yaml
spirng:
	main:
		show-banner: false
```

还可以将属性设置为环境变量。举例来说，如果用的是bash或者zsh，可以用export命令（这里用的是下划线而不是点和横杠，这是对环境变量名称的要求）:

`export spring_main_show_banner=false`

Spring Boot应用程序有多种设置途径。Spring Boot能从多种属性源获得属性，包括如下几处。

- 命令行参数
- java:comp/env里的JNDI属性

- JVM系统属性
- 操作系统环境变量
- 随机生成的带random.*前缀的属性(在设置其他属性时，可以引用它们，比如${random. long})
- 应用程序以外的application.properties或者appliaction.yml文件
- 打包在应用程序内的application.properties或者appliaction.yml文件
- 通过@PropertySource标注的属性源
- 默认属性

这个列表按照优先级排序，也就是说，任何在高优先级属性源里设置的属性都会覆盖低优先级的相同属性。例如，命令行参数会覆盖其他属性源里的属性

application.properties和application.yml文件能放在以下四个位置

- 外置，在相对于应用程序运行目录的/config子目录里
- 外置，在应用程序运行的目录里
- 内置，在config包内
- 内置，在Classpath根目录

同样，这个列表按照优先级排序。也就是说，/config子目录里的application.properties会覆盖应用程序Classpath里的application.properties中的相同属性

此外，如果你在同一优先级位置同时有application.properties和application.yml，那么application.yml里的属性会覆盖application.properties里的属性

#### 自动配置微调

- 禁用模版缓存

  将spring.thymeleaf.cache设置为false就能禁用Thymeleaf模板缓存。在命令行里运行 应用程序时，将其设置为命令行参数即可:

  `java -jar readinglist-0.0.1-SNAPSHOT.jar --spring.thymeleaf.cache=false`

  或者希望每次运行都禁用缓存，可穿件一个application.yml

  ```yaml
  spirng:
  	thymeleaf:
  		cache: false
  ```

  或者通过命令行

  `export spring_thymeleaf_cache=false`

- 配置嵌入式服务器

  `$ java -jar readinglist-0.0.1-SNAPSHOT.jar --server.port=8000`

  ```yaml
  server:
  	port: 8000
  	
  server:
  	port: 8443
  	ssl:
  		key-store: file://path/to/mykey.jks
  		key-store-password: letmein
  		key-password: letmein
  ```

  此处的server.port设置为8443，开发环境的HTTPS服务器大多会选这个端口。 server.ssl.key-store属性指向密钥存储文件的存放路径。这里用了一个file://开头的URL， 从文件系统里加载该文件。你也可以把它打包在应用程序的JAR文件里，用classpath: URL来 引用它。server.ssl.key-store-password和server.ssl.key-password设置为创建该文 件时给定的密码

- 配置日志

  - 用其他日志实现替换Logback

    一般来说，你不需要切换日志实现;Logback能很好地满足你的需要。但是，如果决定使用Log4j或者Log4j2，那么你只需要修改依赖，引入对应该日志实现的起步依赖，同时排除掉 Logback。

    以Maven为例，应排除掉根起步依赖传递引入的默认日志起步依赖，这样就能排除 Logback了:

    ```xml
    <dependency>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-starter</artifactId>
      <exclusions>
    		<exclusion> 
          <groupId>org.springframework.boot</groupId> 			  
          <artifactId>spring-boot-starter-logging</artifactId>
        </exclusion>
      </exclusions>
    </dependency>
    ```

    在Gradle里，在configurations下排除该起步依赖是最简单的办法:

    ```groovy
    configurations {
      all*.exclude group:'org.springframework.boot',
    }
    module:'spring-boot-starter-logging'
    ```

    排除默认日志的起步依赖后，就可以引入想用的日志实现的起步依赖了。在Maven里可以这样添加Log4j:

    ```xml
    <dependency>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-starter-log4j</artifactId>
    </dependency>
    ```

    在Gradle里可以这样添加Log4j: 

    ```groovy
    compile("org.springframework.boot:spring-boot-starter-log4j")
    ```

  - 要完全掌握日志配置，可以在Classpath的根目录(src/main/resources)里创建logback.xml文 件。下面是一个logback.xml的简单例子:

    ```xml
    <configuration>
    	<appender name="STDOUT" 		 class="ch.qos.logback.core.ConsoleAppender">
    		<encoder>
          <pattern>
            %d{HH:mm:ss.SSS} [%thread] %-5level %logger{36} - %msg%n
          </pattern>
        </encoder>
      </appender>
      <logger name="root" level="INFO"/>
      <root level="INFO">
        <appender-ref ref="STDOUT" />
      </root>
    </configuration>
    ```

    除了日志格式之外，这个Logback配置和不加logback.xml文件的默认配置差不多。但是，通过编辑logback.xml，你可以完全掌控应用程序的日志文件

    即使如此，你对日志配置最常做的改动就是修改日志级别和指定日志输出的文件。使用了 Spring Boot的配置属性后，可以在不创建logback.xml文件的情况下修改那些配置

    要设置日志级别，可以创建以logging.level开头的属性，后面是要日志名称。如果根 日志级别要设置为WARN，但Spring Security的日志要用DEBUG级别，可以在application.yml里加入 6 以下内容:

    ```yaml
    logging:
    	level:
    		root: WARN
    		org:
    			springframework:
    				security: DEBUG
    ```

    也可以把Spring Security的包名写成一行:

    ```yaml
    logging:
    	level:
    		root: WARN
    		org.springframework.security: DEBUG
    ```

    想把日志写到位于/var/logs/目录里的BookWorm.log文件里。使用logging.path和loggin.file属性就行了:

    ```yaml
    logging:
    	path: /var/logs/
    	file: BookWorm.log
    	level:
    		root: WARN
    		org:
    			springframework:
    			 security: DEBUG
    ```

    假设应用程序有/var/logs/的写权限，日志就能被写入/var/logs/BookWorm.log。默认情况下， 日志文件的大小达到10MB时会切分一次

    与之类似，这些属性也能在application.properties里设置:

    ```properties
    logging.paht=/var/logs
    logging.file=BookWorm.log
    logging.level.root=WARN
    logging.level.root.org.springframework.security=DEBUG
    ```

    如果还是想要完全掌控日志配置，但是又不想用logback.xml作为Logback配置的名字，可以通过logging.config属性指定自定义的名字:

    ```yaml
    logging:
    	config:
    		classpath;logging-config.xml
    ```

  - 配置数据源

    虽然可以显式配置自己的DataSource Bean，但通常并不用这么做，只需简单地通过属性 配置数据库的URL和身份信息就可以了。举例来说，如果用的是MySQL数据库，你的 application.yml文件看起来可能是这样的:

    ```yaml
    spring:
    	datasource:
    		url: jdbc:mysql://localhost/readinglist
    		username: dbuser
    		password: dbpass
    ```

    通常你都无需指定JDBC驱动，Spring Boot会根据数据库URL识别出需要的驱动，但如果识别出问题了，还可以设置spring.datasource.driver-class-name属性:

    ```yaml
    spring:
    	datasource:
    		url: jdbc:mysql://localhost/readinglist
    		username: dbuser
    		password: dbuser
    		driver-class-name: com.mysql.jdbc.Driver
    ```

    在自动配置DataSource Bean的时候，Spring Boot会使用这里的连接数据。DataSource Bean是一个连接池，如果Classpath里有Tomcat的连接池DataSource，那么就会使用这个连接池; 否则，Spring Boot会在Classpath里查找以下连接池:

    - HikariCP
    - Commons DBCP
    - Commons DBCP2

    这里列出的只是自动配置支持的连接池，还可以自己配置DataSource Bean，使用你喜欢的各种连接池

    也可以设置spring.datasource.jndi-name属性，从JNDI里查找DataSource:

    ```yaml
    spring:
    	datasource:
    		jndi-name: java:/comp/env/jdbc/readingListDS
    ```

    一旦设置了spring.datasource.jndi-name属性，其他数据源连接属性都会被忽略，除 非没有设置别的数据源连接属性

    有很多影响Spring Boot自动配置组件的方法，只需设置一两个属性即可。但这种配置外置的方法并不局限于Spring Boot配置的Bean

  - 应用程序 Bean 的配置外置

    可以不在模板里硬编码Amazon Associate ID，而是把它变成模型中的一个值:

    ```html
    <a th:href="'http://www.amazon.com/gp/product/'
                    + ${book.isbn}
                    + '/tag=' + ${amazonID}"
           th:text="${book.title}">Title</a>
    ```

    ReadingListController需要在模型里包含amazonID这个键，其中的内容是 Amazon Associate ID。同样的道理，我们不应该硬编码这个值，而是应该引用一个实例变量。这 个变量的值应该来自属性配置

    ```java
    package readinglist;
    import java.util.List;
    import org.springframework.beans.factory.annotation.Autowired;
    import org.springframework.boot.context.properties.ConfigurationProperties; import org.springframework.stereotype.Controller;
    import org.springframework.ui.Model;
    import org.springframework.web.bind.annotation.RequestMapping;
    import org.springframework.web.bind.annotation.RequestMethod;
    
    @Controller
    @RequestMapping("/")
    @ConfigurationProperties(prefix="amazon")
    public class ReadingListController {
      private String associateId;
      private ReadingListRepository readingListRepository;
    
      @Autowired
      public ReadingListController(
            ReadingListRepository readingListRepository) {
        this.readingListRepository = readingListRepository;
    	}
      
      public void setAssociateId(String associateId) {
        this.associateId = associateId;
    	}
      
      @RequestMapping(method=RequestMethod.GET)
      public String readersBooks(Reader reader, Model model) {
        List<Book> readingList =
                   readingListRepository.findByReader(reader);
        if (readingList != null) {
          model.addAttribute("books", readingList);
          model.addAttribute("reader", reader);
          model.addAttribute("amazonID", associateId);
    		}
        return "readingList";
      }
      
      @RequestMapping(method=RequestMethod.POST)
      public String addToReadingList(Reader reader, Book book) {
        book.setReader(reader);
        readingListRepository.save(book);
        return "redirect:/";
    	}
    }
    ```

    ReadingListController现在有了一个associateId属性，还有对应的setAssociateId()方法，用它可以设置该属性。readersBooks()现在能通过amazonID这个键把 associateId的值放入模型

    请注意，ReadingListController上加了@ConfigurationProperties注解，这说明该 Bean的属性应该是(通过setter方法)从配置属性值注入的。说得更具体一点，prefix属性说明 ReadingListController应该注入带amazon前缀的属性

    综合起来，我们指定ReadingListController的属性应该从带amazon前缀的配置属性中 进行注入。ReadingListController只有一个setter方法，就是设置associateId属性用的setter 方法。因此，设置Amazon Associate ID唯一要做的就是添加amazon.associateId属性，把它加 入支持的任一属性源位置里即可

    例如，我们可以在application.properties里设置该属性:

    `amazon.associatedId=hahuma-20`

    或者在application.yml里设置:

    ```yaml
    amazon:
    	associateId: habuma-20
    ```

    或者，我们可以将其设置为环境变量，把它作为命令行参数，或把它加到任何能够设置配置属性的地方

    > 需要注意，Spring Boot的属性解析器非常智能，它会自动把驼峰规则的属性和使用连字符或下划线的同名属性关联起来。换句话说，amazon.associateId这个属性和amazon.associate_id以及amazon.associate-id都是等价的。用你习惯的命名规则就好

    > 开启配置属性 从技术上来说，@ConfigurationProperties注解不会生效，除非先向Spring配置类添加@EnableConfigurationProperties注解。但通常无需这么 做，因为Spring Boot自动配置后面的全部配置类都已经加上了@EnableConfigura- tionProperties注解。因此，除非你完全不使用自动配置，否则就 无需显式地添加@EnableConfigurationProperties

    在一个类里收集属性

    虽然在ReadingListController上加上@ConfigurationProperties注解跑起来没问 题，但这并不是一个理想的方案。ReadingListController和Amazon没什么关系，但属性的前缀却是amazon; 后续的各种功能可能需要在ReadingList- Controller里新增配置属性，而它们和Amazon无关

    与其在ReadingListController里加载配置属性，还不如创建一个单独的Bean，为它加上 @ConfigurationProperties注解，让这个Bean收集所有配置属性

    ```java
    package readinglist;
    
    import org.springframework.boot.context.properties.ConfigurationProperties;
    import org.springfrmework.stereotype.Component;
    
    @Component
    @ConfigurationProperties("amazon")
    public class AmazonProperties {
      private String associateId;
      
      public void setAssociateId(String associateId) {
        this.associateId = associateId;
      }
      
      public String getAssociateId() {
        return associateId;
      }
    }
    ```

    有 了 加 载amazon.associateId配 置 属 性 的AmazonProperties后 ， 我 们 可 以 调 整 ReadingListController，让它从注入的AmazonProperties中获取 Amazon Associate ID

    ```java
    package readinglist;
    import java.util.List;
    import org.springframework.beans.factory.annotation.Autowired; import org.springframework.stereotype.Controller;
    import org.springframework.ui.Model;
    import org.springframework.web.bind.annotation.RequestMapping; import org.springframework.web.bind.annotation.RequestMethod;
    
    @Controller
    @RequestMapping("/")
    public class ReadingListController {
      private ReadingListRepository readingListRepository;
      private AmazonProperties amazonProperties;
    	
      
      @Autowired
      public ReadingListController(
          ReadingListRepository readingListRepository,
          AmazonProperties amazonProperties) {
        this.readingListRepository = readingListRepository;
        this.amazonProperties = amazonProperties;
    	}
      
      @RequestMapping(method=RequestMethod.GET)
      public String readersBooks(Reader reader, Model model) {
        List<Book> readingList =
            readingListRepository.findByReader(reader);
        if (readingList != null) {
          model.addAttribute("books", readingList); 	
          model.addAttribute("reader", reader);
       		model.addAttribute("amazonID",amazonProperties.getAssociateId());
    		}
        return "readingList";
      }
      
      @RequestMapping(method=RequestMethod.POST)
      public String addToReadingList(Reader reader, Book book) {
        book.setReader(reader);
        readingListRepository.save(book);
        return "redirect:/";
    	}
    }
    ```

    **ReadingListController不再直接加载配置属性，转而通过注入其中的AmazonProperties Bean来获取所需的信息**

  - 使用Profile进行配置

    当应用程序需要部署到不同的运行环境时，一些配置细节通常会有所不同。比如，数据库连 接的细节在开发环境下和测试环境下就会不一样，在生产环境下又不一样。Spring Framework从 Spring 3.1开始支持基于Profile的配置。Profile是一种条件化配置，基于运行时激活的Profile，会 使用或者忽略不同的Bean或配置类

    在这个例子中，我们就能为SecurityConfig加上@Profile注解:

    ```java
    @Profile("production")
    @Configuration
    @EnableWebSecurity
    public class SecurityConfig extends WebSecurityConfigurerAdapter {
      ...
    }
    ```

    这里用的@Profile注解要求运行时激活production Profile，这样才能应用该配置。如果 production Profile没有激活，就会忽略该配置，而此时缺少其他用于覆盖的安全配置，于是应用自动配置的安全配置

    设置spring.profiles.active属性就能激活Profile，任意设置配置属性的方式都能用于 设置这个值。例如，在命令行里运行应用程序时，可以这样激活production Profile:

    `java -jar readinglist-0.0.1-SNAPSHOT.jar --spring.profiles.active=production`

    也可以向application.yml里添加spring.profiles.active属性:

    ```yaml
    spring:
    	profiles:
    		active: production
    ```

    还可以设置环境变量，将其放入application.properties

    但由于Spring Boot的自动配置做了太多的事情，要找到一个能放置@Profile的地方还 真不怎么方便。幸运的是，Spring Boot支持为application.properties和application.yml里的属性配置Profile

    - 使用特定于Profile的属性文件

    如果你正在使用application.properties，可以创建额外的属性文件，遵循application-{profile}. properties这种命名格式，这样就能提供特定于Profile的属性了

    在日志这个例子里，开发环境的配置可以放在名为application-development.properties的文件 里，配置包含日志级别和输出到控制台: `logging.level.root=DEBUG`

    对于生产环境，application-production.properties会将日志级别设置为WARN或更高级别，并将日志写入日志文件:

    ```properties
    logging.path=/var/logs/
    logging.file=BookWorm.log
    logging.level.root=WARN
    ```

    与此同时，那些并不特定于哪个Profile或者保持默认值(以防万一有哪个特定于Profile的配置不指定这个值)的属性，可以继续放在application.properties里:

    ```properties
    amazon.associateId=habuma-20
    logging.level.root=INFO
    ```

    - 使用多Profile YAML文件进行配置

    如果使用YAML来配置属性，则可以遵循与配置文件相同的命名规范，即创建application- {profile}.yml这样的YAML文件，并将与Profile无关的属性继续放在application.yml里。但既然用了YAML，你就可以把所有Profile的配置属性都放在一个application.yml文件里

    ```yaml
    logging:
    	level:
    		root: INFO
    ---
    spring:
    	profiles: development
    logging:
    	level:
    		root: DEBUG
    ---
    spring:
    	profiles: production
    logging:
    	path: /tmp/
    	file: BookWorm.log
    	level:
    		root: WARN
    ```

    这个application.yml文件分为三个部分，使用一组三个连字符(---)作为分隔符。 第二段和第三段分别为spring.profiles指定了一个值，这个值表示该部分配置应该应用在哪 个Profile里。第二段中定义的属性应用于开发环境，因为spring.profiles设置为 development。与之类似，最后一段的spring.profile设置为production，在production Profile被激活时生效。另一方面，第一段并未指定spring.profiles，因此这里的属性对全部Profile都生效，或者对那些未设置该属性的激活Profile生效

  - 定制应用程序错误层面

    最简单的方法就是创建一个自定义视图，让解析出的视图名为 error。这一点归根到底取决于错误视图解析时的视图解析器

    - 实现了Spring的View接口的Bean，其ID为error(由Spring的BeanNameViewResolver所解析)
    - 如果配置了Thymeleaf，则有名为error.html的Thymeleaf模板
    - 如果配置了FreeMarker，则有名为error.ftl的FreeMarker模板
    - 如果配置了Velocity，则有名为error.vm的Velocity模板
    - 如果是用JSP视图，则有名为error.jsp的JSP模板

    因为我们的阅读列表应用程序使用了Thymeleaf，所以我们要做的就是创建一个名为 error.html的文件，把它和其他的应用程序模板一起放在模板文件夹里

    ```html
    <html>
      <head>
        <title>Oops!</title>
        <link rel="stylesheet" th:href="@{/style.css}"></link>
      </head>
    	<html>
      <div class="errorPage">
        <span class="oops">Oops!</span><br/>
        <img th:src="@{/MissingPage.png}"></img>
        <p>There seems to be a problem with the page you requested (<span th:text="${path}"></span>).</p>
        <p th:text="${'Details: ' + message}"></p>
      </div>
    	</html>
    </html>
    ```

    这个自定义的错误模板应该命名为error.html，放在模板目录里，这样Thymeleaf模板解析器才能找到它。在典型的Maven或Gradle项目里，这就意味着要把该文件放在src/main/resources/templates中，运行时它就在Classpath的根目录里

    基本上，这个简单的Thymeleaf模板就是显示一张图片和一些提示错误的文字。其中有两处 特别的信息需要呈现:错误的请求路径和异常消息。但这还不是错误页上的全部细节。默认情况 下，Spring Boot会为错误视图提供如下错误属性

    - timestamp:错误发生的时间
    - status:HTTP状态码
    - error:错误原因
    - exception:异常的类名
    - message:异常消息(如果这个错误是由异常引起的)
    - errors:BindingResult异常里的各种错误(如果这个错误是由异常引起的)
    - trace:异常跟踪信息(如果这个错误是由异常引起的)
    - path:错误发生时请求的URL路径

    其中某些属性，比如path，在向用户交待问题时还是很有用的。其他的，比如trace，用起来要保守一点，将其隐藏，或者用得聪明点，让错误页尽可能对用户友好

    [[资源位置]] 请注意，模板里还引用了一张名为MissingPage.png的图片。图片的实际内容并不重要，所以 尽情挑选适合你的图片就好了，但请一定将它放在src/main/resources/static或src/main/resources/public里，这样应用程序运行时才能找到它

    