### GORM数据持久化

Grails里最让人着迷的恐怕就是GORM了。GORM将数据库相关工作简化到和声明要持久化的实体一样容易

```groovy
package radinglist

import grails.persistence.*
  
@Entity
class Book {
  Reader reader
  String isbn
  String title
  String author
  String description
}
```

就和Book的Java版本一样，这个类里有很多描述图书的属性。但又与Java版本不一样，这里 没有分号、public或private修饰符、setter和getter方法或其他Java中常见的代码噪声。是Grails 的@Entity注解让这个类变成了GORM实例。实体将对象映射到数据库，为Book添加持久化方法，通过这些方法可以存取图书

要在Spring Boot项目里使用GORM，必须在项目里添加GORM依赖。在Maven中， `<dependency>`看起来是这样的:

```xml
<dependency>
	<groupId>org.grails</groupId> 
  <artifactId>gorm-hibernate4-spring-boot</artifactId
  <version>1.1.0.RELEASE</version>
</dependency>
```

一样的依赖，在Gradle里是这样的:

`compile("org.grails:gorm-hibernate4-spring-boot:1.1.0.RELEASE")`

这个库自带了一些Spring Boot自动配置，会自动配置所有支持GORM所需的Bean

> #### GORM在Spring Boot里的另一个选择
>
> gorm-hibernate4-spring-boot是通过Hibernate开启GORM数据持久化的。对于MongoDB，Spring Boot里的MongoDB GORM，它的Maven依赖是这样的:
>
> ```xml
> <dependency>
>   <groupId>org.grails</groupId>
>   <artifactId>gorm-mongodb-spring-boot</artifactId>
>   <version>1.1.0.RELEASE</version>
> </dependency>
> ```
>
> 下面是相同的Gradle依赖:
>
> `compile("org.grails:gorm-mongodb-spring-boot:1.1.0.RELEASE")`
>
> GORM的工作原理要求实体类必须用Groovy来编写

```groovy
package readinglist
    import grails.persistence.*
import org.springframework.security.core.GrantedAuthority import
org.springframework.security.core.authority.SimpleGrantedAuthority import org.springframework.security.core.userdetails.UserDetails

@Entity
class Reader implements UserDetails {
  String username
  String fullname
  String password
  Collection<? extends GrantedAuthority> getAuthorities() {
    Arrays.asList(new SimpleGrantedAuthority("READER"))
  }
  boolean isAccountNonExpired() {
    true
  }
  boolean isAccountNonLocked() {
    true
  }
  boolean isCredentialsNonExpired() {
    true
  }
  boolean isEnabled() {
    true
  }
}
```

```groovy
package readinglist
import org.springframework.beans.factory.annotation.Autowired import
 org.springframework.boot.context.properties.ConfigurationProperties
import org.springframework.http.HttpStatus
import org.springframework.stereotype.Controller
import org.springframework.ui.Model
import org.springframework.web.bind.annotation.ExceptionHandler import org.springframework.web.bind.annotation.RequestMapping import org.springframework.web.bind.annotation.RequestMethod import org.springframework.web.bind.annotation.ResponseStatus

@Controller
@RequestMapping("/")
@ConfigurationProperties("amazon")
class ReadingListController {
	@Autowired
  AmazonProperties amazonProperties

  @ExceptionHandler(value=RuntimeException.class) 	
  @ResponseStatus(value=HttpStatus.BANDWIDTH_LIMIT_EXCEEDED) 
  def error() {
  	"error" }
	}
	
  @RequestMapping(method=RequestMethod.GET)
  def readersBooks(Reader reader, Model model) {
    List<Book> readingList = Book.findAllByReader(reader)
    model.addAttribute("reader", reader)
    if (readingList) {
      model.addAttribute("books", readingList)
      model.addAttribute("amazonID", amazonProperties.getAssociateId()) 
    }
  	"readingList"
	}

	@RequestMapping(method=RequestMethod.POST)
	def addToReadingList(Reader reader, Book book) {
    Book.withTransaction {
      book.setReader(reader)
      book.save()
    }
    "redirect:/"
  }
}
```

这个版本的ReadingListController和之前的相比，最明显的不同之处在于，它是用 Groovy写的，没有Java的那些代码噪声。最重要的不同之处在于，无需再注入ReadingListRepository，它直接通过Book类型持久化

在readersBooks()方法里，它调用了Book的findAllByReader()静态方法，传入了指定 的读者信息

与之类似，addToReadingList()方法使用了静态方法withTransaction()和实例方法 save()。这两个方法也是GORM提供的，用于将Book保存到数据库里

**所要做的就是声明一些属性，在Book上添加@Entity注解**

SecurityConfig也要做类似的修改，通过GORM而非ReadingListRepository来获取 Reader

```groovy
package readinglist
import org.springframework.context.annotation.Configuration
import org.springframework.security.config.annotation.authentication.builders.AuthenticationManagerBuilder 
import org.springframework.security.config.annotation.web.builders.HttpSecurity 
import org.springframework.security.config.annotation.web.configuration.WebSecurityConfigurerAdapter 
import org.springframework.security.core.userdetails.UserDetailsService

@Configuration
class SecurityConfig extends WebSecurityConfigurerAdapter {
  void configure(HttpSecurity http) throws Exception {
    http
      .authorizeRequests()
      .antMatchers("/").access("hasRole('READER')")
      .antMatchers("/**").permitAll()
      .and()
      .formLogin()
      .loginPage("/login")
      .failureUrl("/login?error=true")
	}
  
  void configure(AuthenticationManagerBuilder auth) throws Exception { 
    auth
    	.userDetailsService(
		  { username -> Reader.findByUsername(username) }
			  as UserDetailsService)
  }
}
```

除了用Groovy重写，SecurityConfig里最明显的变化无疑就是第二个configure()方法。它使用了一个闭包(UserDetailsService的实现类)，其中调用静态方法findByUsername()来查找Reader，这个功能是GORM提供的

可以通过各种运行Spring Boot应用程序的方法来启动阅读列表应用程序。启动后， 应用程序应该能像从前一样工作。但是持久化机制已经被改变了

### Groovy Server Pages定义视图

无论选择哪种模板，你要做的就是 添加合适的起步依赖，在Classpath根部的templates/目录里编写模板。自动配置会处理剩下的事情

Grails项目也提供GSP的自动配置。如果你想在Spring Boot应用程序里使用GSP，必须向项目 里添加Spring Boot的GSP库:

` compile("org.grails:grails-gsp-spring-boot:1.0.0")`

和Spring Boot提供的其他视图模板一样，库放在Classpath里就会触发自动配置，设置所需的 视图解析器，以便在Spring MVC的视图层里使用GSP

要把Thymeleaf的 readingList.html 文 件 用 GSP 的 形 式 重 写 ， 放 在 readingList.gsp 文 件 ( 位 于 src/main/resources/ templates)里

```html
<!DOCTYPE html>
<html>
  <head>
    <title>Reading List</title>
    <link rel="stylesheet" href="/style.css"></link>
	</head>
	<body>
  	<h2>Your Reading List</h2>
    <g:if test="${books}">
      <g:each in="${books}" var="book">
        <dl>
          <dt class="bookHeadline">
            ${book.title} by ${book.author}
            (ISBN: ${book.isbn}")
          </dt>          
          <dd class="bookDescription">
            <g:if test="book.description">
              ${book.description}
						</g:if>
            <g:else>
				       No description available
				    </g:else>
          </dd> 
        </dl>
			</g:each>
    </g:if>
    <g:else>
		  <p>You have no books in your book list</p>
		</g:else>
    <hr />
		<h3>Add a book</h3>
    
    <form method="POST">
      <label for="title">Title:</label>
      <input type="text" name="title"  
        value="${book?.title}"/><br/>
      <label for="author">Author:</label>
      <input type="text" name="author"
               value="${book?.author}"/><br/>
      <label for="isbn">ISBN:</label>
      <input type="text" name="isbn"
               value="${book?.isbn}"/><br/>
      <label for="description">Description:</label><br/>
      <textarea name="description" rows="5" cols="80">
        ${book?.description}
      </textarea>
      <input type="hidden" name="${_csrf.parameterName}"
             value="${_csrf.token}" />
      <input type="submit" value="Add Book" />
		</form>
	</body>
</html>
```

GSP模板中使用了表达式语言引用(用${}包围的部分)以及GSP标签库(例如 `<g:if>`和`<g:each>`)。这并不是Thymeleaf那样的纯HTML

### 结合 Spring Boot 与 Grails 3

Grails一直都是构建于Spring、Groovy、Hibernate和其他巨人肩膀之上的高阶框架。到了Grails 3

在Mac OS X和大部分Unix系统上，最简单的安装方法是 在命令行里使用SDKMAN:

`sdk install grails`

在Grails项目中，你会使用grails命令行工具执行很多任务，包括创建项目。要创建阅读列表项目，可以这样使用grails命令:

`grails create-app readinglist`

```
- build.gradle
- gradle
	- wrapper
- gradle.properties
- gradlew
- gradlew.bat
- grails-app
	- assets
	- conf
	- controllers
	- domain
	- i18n
	- init
	- services
	- taglib
	- utils
	- views
- src
	- integration-test
	- main
	- test
```

build.gradle文件，先，构建说明文件里使用了Spring Boot的Gradle插件: `apply plugin: "spring-boot"`，这意味着能像使用其他Spring Boot应用程序那样构建并运行这个Grails应用程序。依赖里有不少有用的Spring Boot库:

```groovy
dependencies {
	compile 'org.springframework.boot:spring-boot-starter-logging'
  compile("org.springframework.boot:spring-boot-starter-actuator") 
  compile "org.springframework.boot:spring-boot-autoconfigure" 
  compile "org.springframework.boot:spring-boot-starter-tomcat" 
  ...
}
```

运行应用程序，运行Grails应用程序最直接的方式是在命令行里使用grails工具的run-app命令:`$ grails run-app`

还可以用各种运行 Spring Boot项目的方式来运行这个应用程序。此处通过Gradle引入了bootRun任务:`gradle bootRun`。还可以构建项目，运行生成的可执行JAR文件:

`gradle build`

`java -jar build/lib/readingList-0.1.jar`

#### 定义领域模型

阅读列表应用程序里的核心领域模型是Book类。虽然我们可以手工创建Book.groovy文件， 但通常还是用grails工具来创建领域模型类比较好。因为它知道该把文件放到哪里，并且能在 同一时间生成各种相关内容。要创建Book类，我们会使用grails工具的create-domain-class命令`grails create-domain-class Book`

这条命令会生成两个源文件:一个Book.groovy文件和一个BookSpec.groovy文件。后者是一 个Spock说明，用来测试Book类。一开始这个文件是空的可以填入各种测试内容来验证Book 的各种功能

Book.groovy文件里定义了Book类，可以在grails-app/domain/readingList里找到这个文件。 它一开始基本没什么内容:

```groovy
package readinglist

class Book {
  static constraints = {
    
  }
}
```

填入内容后，

```groovy
package readinglist

class Book {
  static constraints = {    
  }
  
  String reader
  String isbn
  String title
  String author
  String description
}
```

静态的constraints变量里可以定义各种附加在Book实例上的验证约束

#### 开发Grails控制器

有了领域模型，通过grails工具创建出控制器就很容易了。关于控制器，有几个命令可供 选择

- create-controller:创建空控制器，让开发者来编写控制器的功能
- generate-controller:生成一个控制器，其中包含特定领域模型类的基本CRUD操作
- generate-all:生成针对特定领域模型类的基本CRUD控制器，及其视图

脚手架控制器很好用，也是Grails中比较知名的特性

用create-controller 命令来创建原始的控制器，然后填入所需的方法`grails create-controller ReadingList`

这个命令会在grails-app/controllers/readingList里创建一个名为ReadingListController的

控制器:

```groovy
package readinglist
class ReadingListController {
  def index() { }
}
```

它能处理发往 /readingList的请求，将请求转给grails-app/views/readingList/index.gsp里定义的视图。修改后，

```groovy
package readinglist
import static org.springframework.http.HttpStatus.*
import grails.transaction.Transactional

class ReadingListController {
  def index() {
    respond Book.list(params), model:[book: new Book()]
  }
  @Transactional
  def save(Book book) {
    book.reader = 'Craig'
    book.save flush:true
    redirect(action: "index")
	}
}
```

ReadingListController 功能已经基本完整。它可以处理发往/readingList的GET请求，获取并展示图书列表。在表单提交 后，它还会处理POST请求，保存图书，随后重定向回index动作(由index()方法来处理)

#### 创建试图

```html
<!DOCTYPE html>
<html>
  <head>
    <meta name="layout" content="main"/>
    <title>Reading List</title>
    <link rel="stylesheet"
          href="/assets/main.css?compile=false"  />
    <link rel="stylesheet"
          href="/assets/mobile.css?compile=false"  />
    <link rel="stylesheet"
          href="/assets/application.css?compile=false"  />    
  </head>
  <body>
    <h2>Your Reading List</h2>
    <g:if test="${bookList && !bookList.isEmpty()}">
      <g:each in="${bookList}" var="book">
        <dl>
          <dt class="bookHeadline">
            ${book.title}</span> by ${book.author}
            (ISBN: ${book.isbn}")
          </dt>
          <dd class="bookDescription">
            <g:if test="${book.description}">
              ${book.description}
            </g:if>
            <g:else>
              No description available
            </g:else>
          </dd>          
        </dl>
      </g:each>
  	</g:if>
  	<g:else>
		  <p>You have no books in your book list</p>
  	</g:else>
  	<hr/>
  	<h3>Add a book</h3>
  	<g:form action="save">
      <fieldset class="form">
        <label for="title">Title:</label>
        <g:field type="text" name="title" value="${book?.title}"/><br/> 					<label for="author">Author:</label>
		    <g:field type="text" name="author"
                          value="${book?.author}"/><br/>
      	<label for="isbn">ISBN:</label>
				<g:field type="text" name="isbn" value="${book?.isbn}"/><br/>
        <label for="description">Description:</label><br/> 
        <g:textArea name="description" value="${book?.description}"
                                 rows="5" cols="80"/>
			</fieldset>
			<fieldset class="buttons">
			  <g:submitButton name="create" class="save"
                       value="${message(code:'default.button.create.label',
                              default: 'Create')}" />
      </fieldset>
		</g:form>
  </body>
</html>
```

