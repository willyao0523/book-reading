### 测试

在编写单元测试的时候，Spring通常不需要介入。Spring鼓励松耦合、接口驱动的设计，这些都能让用户很轻松地编写单元测试。但是在写单元测试时并不需要用到Spring。但是，集成测试要用到Spring。如果生产应用程序使用Spring来配置并组装组件，那么测试 就需要用它来配置并组装那些组件

Spring的SpringJUnit4ClassRunner可以在基于JUnit的应用程序测试里加载Spring应用程 序上下文。在测试Spring Boot应用程序时，Spring Boot除了拥有Spring的集成测试支持，还开启 了自动配置和Web服务器，并提供了不少实用的测试辅助工具

### 集成测试自动配置

Spring Framework的核心工作是将所有组件编织在一起，构成一个应用程序。整个过程就是 读取配置说明(可以是XML、基于Java的配置、基于Groovy的配置或其他类型的配置)，**在应用程序上下文里初始化Bean，将Bean注入依赖它们的其他Bean中**

对Spring应用程序进行集成测试时，让Spring遵照生产环境来组装测试目标Bean是非常重要的一点。当然，也可以手动初始化组件，并将它们注入其他组件，但对那些大型应用程序来说， 这是项费时费力的工作。而且，Spring提供了额外的辅助功能，比如组件扫描、自动织入和声明 性切面(缓存、事务和安全，等等)

Spring自1.1.1版就向集成测试提供了极佳的支持。自Spring 2.5开始，集成测试支持的形式就 变成了SpringJUnit4ClassRunner。这是一个JUnit类运行器，会为JUnit测试加载Spring应用程 序上下文，并为测试类自动织入所需的Bean

```java
@RunWith(SpringJUnit4ClassRunner.class)
@ContextConfiguration(
	classes=AddressBookConfiguration.class
)
public class AddressServiceTest {
  
  @AutoWired
  private AddressService addressService;
  
  @Test
  public void testService() {
    Address address = addressService.findByLastName("Sheman");
    assertEqual("P", address.getFirstName);
    assertEquals("Sherman", address.getLastName()); 
    assertEquals("42 Wallaby Way", address.getAddressLine1());
    assertEquals("Sydney", address.getCity()); 
    assertEquals("New South Wales", address.getState());
    assertEquals("2000", address.getPostCode());
  }
}
```

AddressServiceTests上加注了@RunWith和@ContextConfiguration注解。 @RunWith的参数是SpringJUnit4ClassRunner.class，开启了Spring集成测试支持。与此 同时，@ContextConfiguration指定了如何加载应用程序上下文。此处我们让它加载AddressBookConfiguration里配置的Spring应用程序上下文

除了加载应用程序上下文，SpringJUnit4ClassRunner还能通过自动织入从应用程序上下文里向测试本身注入Bean。因为这是一个针对AddressService Bean的测试，所以需要将它注 入测试。最后，testService()方法调用地址服务并验证了结果

虽然@ContextConfiguration在加载Spring应用程序上下文的过程中做了很多事情，但它没能加载完整的Spring Boot。Spring Boot应用程序最终是由SpringApplication加载的。它可以显式加载，在这里也可以使用SpringBootServletInitializer。SpringApplication不仅加载应用程序上下文，还会开启日志、 加载外部属性(application.properties或application.yml)，以及其他Spring Boot特性。用@ContextConfiguration则得不到这些特性

要在集成测试里获得这些特性，可以把@ContextConfiguration替换为Spring Boot的 @SpringApplicationConfiguration:

```java
@RunWith(SpringJUnit4ClassRunner.class)
@SpringApplicationConfiguration(
	classes=AddressBookConfiguration.class
)
public class AddressServiceTest {
	...
}
```

@SpringApplicationConfiguration的用法和@ContextConfiguration大致相同，但也有不同的地方，@SpringApplicationConfiguration加载Spring应用程序上下文的方式同 SpringApplication相同，处理方式和生产应用程序中的情况相同。这包括加载外部属性和 Spring Boot日志

**有充分的理由说，在大多数情况下，为Spring Boot应用程序编写测试时应该用@Spring- ApplicationConfiguration代替@ContextConfiguration**

### 测试Web应用程序

Spring MVC有一个优点:它的编程模型是围绕POJO展开的，在POJO上添加注解，声明如何处理Web请求。这种编程模型不仅简单，还让你能像对待应用程序中的其他组件一样对待这些控 制器。还可以针对这些控制器编写测试，就像测试POJO一样

举例来说，考虑ReadingListController里的addToReadingList()方法:

```java
@RequestMapping(method=RequestMethod.POST)
public String addToReadingList(Book book) {
  book.setReader(reader);
  readingListRepository.save(book);
  return "redirect:/readingList";
}
```

如果忽略@RequestMapping注解，得到的就是一个相当基础的Java方法。则立马就能想到这样一个测试，提供一个ReadingListRepository的模拟实现，直接调用addToReadingList()，判断返回值并验证对ReadingListRepository的save()方法有过调用

该测试的问题在于，它仅仅测试了方法本身，当然，这要比没有测试好一点。然而，它没有 测试该方法处理/readingList的POST请求的情况，也没有测试表单域绑定到Book参数的情况。虽然你可以判断返回的String包含特定值，但没法明确测试请求在方法处理完之后是否真的会重定向到/readingList

**要恰当地测试一个Web应用程序，你需要投入一些实际的HTTP请求，确认它能正确地处理那些请求**。幸运的是，Spring Boot开发者有两个可选的方案能实现这类测试：

- Spring Mock MVC:能在一个近似真实的模拟Servlet容器里测试控制器，而不用实际启动 应用服务器
- Web集成测试:在嵌入式Servlet容器(比如Tomcat或Jetty)里启动应用程序，在真正的应 用服务器里执行测试

这两种方法各有利弊。很明显，启动一个应用服务器会比模拟Servlet容器要慢一些，但毫无 疑问基于服务器的测试会更接近真实环境，更接近部署到生产环境运行的情况

### 模拟Spring MVC

早在Spring 3.2，Spring Framework就有了一套非常实用的Web应用程序测试工具，能模拟 Spring MVC，不需要真实的Servlet容器也能对控制器发送HTTP请求。Spring的Mock MVC框架模 拟了Spring MVC的很多功能。它几乎和运行在Servlet容器里的应用程序一样

要在测试里设置Mock MVC，可以使用MockMvcBuilders，该类提供了两个静态方法

- standaloneSetup():构建一个Mock MVC，提供一个或多个手工创建并配置的控制器

- webAppContextSetup():使用Spring应用程序上下文来构建Mock MVC，该上下文里

  可以包含一个或多个配置好的控制器

> 两者的主要区别在于，standaloneSetup()希望你手工初始化并注入你要测试的控制器，而webAppContextSetup()则基于一个WebApplicationContext的实例，通常由Spring加载。 前者同单元测试更加接近，你可能只想让它专注于单一控制器的测试，而后者让Spring加载控制 器及其依赖，以便进行完整的集成测试

webAppContextSetup()接受一个WebApplicationContext参数。因此，需要为测试类加上@WebAppConfiguration注解，使用@Autowired将WebApplicationContext作为实例变量注入测试类

```java
@RunWith(SpringJUnit4ClassRunner.class)
@SpringApplicationConfiguration(
	classes = ReadingListApplication.class
)
@WebAppConfiguration
public class MockMvcWebTests {

  @Autowired
  private WebApplicationContext webContext;
  
  private MockMvc mockMvc;
  
  @Before
  public void setupMockMvc() {
    mockMvc = MockMvcBuilders
      .webAppContextSetup(webContext)
      .build();
  }
}
```

@WebAppConfiguration注解声明，由SpringJUnit4ClassRunner创建的应用程序上下文应该是一个WebApplicationContext(相对于基本的非WebApplicationContext)

setupMockMvc()方法上添加了JUnit的@Before注解，**表明它应该在测试方法之前执行**。它将WebApplicationContext注入webAppContextSetup()方法，然后调用build()产生了一 个MockMvc实例，该实例赋给了一个实例变量，供测试方法使用

```java
@Test
public void homePage() throws Exception {
  mockMvc.perform(MockMvcRequestBuilders.get("/readinglist"))
    .andExpect(MockMvcResultMatchers.status().isOk())
    .andExpect(MockMvcResultMatchers.view().name("readingList"))
    .andExpect(MockMvcResultMatchers.model().attributeExists("books"))
    .andExpect(
    MockMvcResultMatchers.model()
    .attribute("books",Matchers.is(Matchers.empty())
              )
  );
}
```

在这个测试方法里使用了很多静态方法，包括Spring的MockMvcRequest- Builders和MockMvcResultMatchers里的静态方法，还有Hamcrest库的Matchers里的静态方 法。先添加一些静态import，这样代码看起来更清爽一些

```java
import static org.hamcrest.Matchers.*;
import static org.springframework.test.web.servlet.request.MockMvcRequestBuilders.*;
import static org.springframework.test.web.servlet.result.MockMvcResultMatchers.*;
```

有了这些静态import后，测试方法可以稍作调整:

```java
@Test
public void homePage() throws Exception {
  mockMvc.perform(get("readinglist"))
    .andExcept(status().isOk())
    .andExcept(view().name("readinglist"))
    .andExcept(model().attributeExists("books"))
    .andExcept(model().attribute("books", is(empty)))
}
```

这个测试方法读起来就很自然了。首先向/readingList发起一个GET请求，接下来希望该请求处理成功(isOk()会判断HTTP 200响应码)，并且视图的逻辑名称为readingList。测试 还要断定模型包含一个名为books的属性，该属性是一个空集合。所有的断言都很直观

```java
@Test
public void postBook() throws Exception {
  mockMvc.perform(post("/readingList")
    .contentType(MediaType.APPLICATION_FORM_URLENCODED)
    .param("title", "BOOK TITLE")
    .param("author", "BOOK AUTHOR")
    .param("isbn", "1234567890")
		.param("description", "DESCRIPTION"))
    .andExpect(status().is3xxRedirection())
    .andExpect(header().string("Location", "/readingList"));
  
  Book expectedBook = new Book();
  expectedBook.setId(1L);
  expectedBook.setReader("craig");
  expectedBook.setTitle("BOOK TITLE");
  expectedBook.setAuthor("BOOK AUTHOR");
  expectedBook.setIsbn("1234567890");
  expectedBook.setDescription("DESCRIPTION");
  
  mockMvc.perform(get("/readingList"))
    .andExpect(status().isOk())
    .andExpect(view().name("readingList"))
    .andExpect(model().attributeExists("books"))
    .andExpect(model().attribute("books", hasSize(1)))
    .andExpect(model().attribute("books",
             contains(samePropertyValuesAs(expectedBook)))
  );
}
```

在提交图书时，我们必须确保内容类型(通过MediaType.APPLICATION_FORM_URLENCODED) 设置为application/x-www-form-urlencoded，这才是运行应用程序时浏览器会发送的内容类型。随 后，要用MockMvcRequestBuilders的param方法设置表单域，模拟要提交的表单。一旦请求 执行，我们要检查响应是否是一个到/readingList的重定向

假定以上测试都通过，我们进入第二部分。首先设置一个Book对象，包含想要的值。我们 用这个对象和首页获取的模型的值进行对比

随后要对/readingList发起一个GET请求，大部分内容和我们之前测试主页时一样，只是之前 模型中有一个空集合，而现在有一个集合项。这里要检查它的内容是否和我们创建的expectedBook一致

### 测试Web安全

Spring Security能让你非常方便地测试安全加固后的Web应用程序。为了利用这点优势，你必 须在项目里添加Spring Security的测试模块。要在Gradle里做到这一点，需要的就是以下 testCompile依赖:

`testCompile("org.springframework.security:spring-security-test")`

如果用的是Maven，则添加以下`<dependency>`:

```xml
<dependency>
  <groupId>org.springframework.security</groupId>
  <artifactId>spring-security-test</artifactId>
  <scope>test</scope>
</dependency>
```

应用程序的Classpath里有了Spring Security的测试模块之后，只需在创建MockMvc实例时运用Spring Security的配置器

```java
@Before
public void setupMockMvc() {
  mockMvc = MockMvcBuilders
    .webAppContextSetup(webContext)
    .apply(springSecurity())
    .build();
}
```

springSecurity()方法返回了一个Mock MVC配置器，为Mock MVC开启了Spring Security 支持。只需像上面这样运用就行了，Spring Security会介入MockMvc上执行的每个请求。具体的 安全配置取决于如何配置Spring Security(或者Spring Boot如何自动配置Spring Security)

> **springSecurity()**方法 springSecurity()是SecurityMockMvcConfigurers 的一个静态方法，

开启了Spring Security之后，在请求主页的时候，便不能只期待HTTP 200响应。如果请求未经身份验证，应该期待重定向到登录页面

```java
@Test
public void homePage_unauthenticatedUser() throws Exception {
  mockMvc.perform(get("/"))
    .andExpect(status().is3xxRedirection())
    .andExpect(header().string("Location",
                               "http://localhost/login"));
}
```

Spring Security提供了两个注解：

- @WithMockUser:加载安全上下文，其中包含一个UserDetails，使用了给定的用户名、

  密码和授权

- @WithUserDetails:根据给定的用户名查找UserDetails对象，加载安全上下文

在这两种情况下，Spring Security的安全上下文都会加载一个UserDetails对象，添加了该 注解的测试方法在运行过程中都会使用该对象。@WithMockUser注解是两者里比较基础的那个， 允许显式声明一个UserDetails，并加载到安全上下文

```java
@Test
@WithMockUser(username="craig", password="password", roles="READER")
public void homePage_authenticatedUser() throws Exception {
	... 
}
```

@WithMockUser绕过了对UserDetails对象的正常查询，用给定的值创建了一 个UserDetails对象取而代之。在简单的测试里，这就够用了。但测试需要Reader(实现了UserDetails)而非@WithMockUser创建的通用UserDetails。为此需要 @WithUserDetails

@WithUserDetails注解使用事先配置好的UserDetailsService来加载UserDetails对象；配置了一个UserDetailsService Bean，它会根据给定的用户名查找并返回一个Reader对象

```java
@Test
@WithUserDetails("craig")
public void homePage_authenticatedUser() throws Exception {
  Reader expectedReader = new Reader();
  expectedReader.setUsername("craig");
  expectedReader.setPassword("password");
  expectedReader.setFullname("Craig Walls");
	mockMvc.perform(get("/"))
    .andExpect(status().isOk())
    .andExpect(view().name("readingList"))
    .andExpect(model().attribute("reader",
                                 samePropertyValuesAs(expectedReader)))
		.andExpect(model().attribute("books", hasSize(0)))
}
```

通过@WithUserDetails注解声明要在测试方法执行过程中向安全上下文里加载craig用户。Reader会放入模型，该测试方法先创建了一个期望的Reader对象，后续可以用来进行比较。随后GET请求发起，也有了针对视图名和模型内容的断言，其中包括名为reader的模型属性

同样，此处没有启动Servlet容器来运行这些测试，Spring的Mock MVC取代了实际的Servlet 容器。这样做的好处是测试方法运行相对较快。因为不需要等待服务器启动，而且不需要打开 Web浏览器发送表单，所以测试比较简单快捷

这并不是一个完整的测试。它比直接调用控制器方法要好，但它并没有真的在Web浏 览器里执行应用程序，验证呈现出的视图。为此需要启动一个真正的Web服务器，用真实浏览器来访问它

### 测试运行中的应用程序

在真实的服务器里启动应用程序，用真实 的Web浏览器访问它，这样比使用模拟的测试引擎更能展现应用程序在用户端的行为

但是，用真实的Web浏览器在真实的服务器上运行测试会很麻烦。虽然构建时的插件能把应 用程序部署到Tomcat或者Jetty里，但它们配置起来多有不便。而且测试这么多，几乎不可能隔离运行，也很难不启动构建工具

然而Spring Boot找到了解决方案。它支持将Tomcat或Jetty这样的嵌入式Servlet容器作为运行 中的应用程序的一部分，可以运用相同的机制，在测试过程中用嵌入式Servlet容器来启动应用 程序

Spring Boot的@WebIntegrationTest注解就是这么做的。在测试类上添加@Web- IntegrationTest注解，可以声明不仅希望Spring Boot为测试创建应用程序上下文，还要启动一个嵌入式的Servlet容器。一旦应用程序运行在嵌入式容器里就可以发起真实的HTTP请求，断言结果了

```java
@RunWith(SpringJUnit4ClassRunner.class)
@SpringApplicationConfiguration(
  classes = ReadingListApplication.class
)
@WebIntegrationTest
public class SimpleWebTest {
  @Test(expected=HttpClientErrorException.class)
  public void pageNotFound() {
    try {
      RestTemplate rest = new RestTemplate();
      rest.getForObject("http://localhost:8080/bogusPage", string.class);
      fail("Should result in HTTP 404");        
    } catch (HttpClientErrorException e) {
      assertEquals(HttpStatus.NOT_FOUND, e.getStatusCode());
      throw e;
    }
  }
}
```

演示如何使用@WebIntegrationTest在服务器里启动应用程序。要判断实际启动的服务器究竟是哪个，可以遵循在命令行里运行应用程序时的逻辑。默认 情况下，会有一个监听8080端口的Tomcat启动。但是，如果Classpath里有的话，Jetty或者Undertow 也能启动这些服务器

测试方法的主体部分假设应用程序已经运行，监听了8080端口。它使用了Spring的 RestTemplate对一个不存在的页面发起请求，判断服务器的响应是否为HTTP 404(NOT FOUND)。如果返回了其他响应，则测试失败

#### 用随机端口启动服务器

此处的默认行为是启动服务器监听8080端口。在一台机器上一次只运行一个测 试的话，这没什么问题，因为没有其他服务器监听8080端口。本机总是有其他服务器在监听8080端口，那该怎么办?这时测试会失败，因为端口冲突，服务器启动不了

Spring Boot在随机选择的端口上启动服务器很方便。一种办法是将**server.port属性设置为0**，让Spring Boot选择一个随机的可用端口。@WebIntegrationTest 的value属性接受一个String数组，数组中的每项都是键值对，形如name=value，用来设置测 试中使用的属性。要设置server.port可以这样做:

`@WebIntegrationTest(value={"server.port=0"})`

另外，因为只要设置一个属性，所以还能有更简单的形式:

`@WebIntegrationTest("server.port=0")`

通过value属性来设置属性通常还算方便。但@WebIntegrationTest还提供了一个 randomPort属性，更明确地表示让服务器在随机端口上启动。可以将randomPort设置为 true，启用随机端口:

`@WebIntegrationTest(randomPort=true)`

getForObject()方法在URL里硬编码了8080端口。如果端口是随机选择的，如下构建：

- 首先需要以**实例变量**的形式注入选中的端口。为了方便，Spring Boot将local.server.port的值设置为了选中的端口。只需使用Spring的@Value注解将其注入即可:
  ```java
  @Value("${local.server.port}")
  private int port;
  ```

- 有了端口之后，只需对getForObject()稍作修改，使用这个port就好了:
  ```java
  rest.getForObject(
  	"http://localhost:{port}/bogusPage",
    String.class, 
    port
  );
  ```

这里我们在URL里把硬编码的8080改为{port}占位符。在getForObject()调用里把port属性作为最后一个参数传入，就能确保该占位符被替换为注入port的值了

### 使用Selenium测试HTML页面

RestTemplate对于简单的请求而言使用方便，是测试REST端点的理想工具。但是，就算它能对返回HTML页面的URL发起请求，也不方便对页面内容或者页面上执行的操作进行断言。 结果HTML里的内容最好能够精确判断(这种测试很脆弱)。不过无法轻易判断页面上选中的 内容，或者执行诸如点击链接或提交表单这样的操作。

对于HTML应用程序测试，有一个更好的选择——Selenium，它的功 能远不止提交请求和获取结果。它能实际打开一个Web浏览器，在浏览器的上下文中执行测试。 Selenium尽量接近手动执行测试，但与手工测试不同。Selenium的测试是自动的，而且可以重复运行

为了用Selenium测试阅读列表应用程序，先写一个测试来获取首页，为新书填写表单， 提交表单，随后判断返回的页面里是否包含新添加的图书

- 首先需要把Selenium作为测试依赖添加到项目里:
  `testCompile("org.seleniumhq.selenium:selenium-java:2.45.0")`

- 使用了Spring Boot 的@WebIntegrationTest
  ```java
  @RunWith(SpringJUnit4ClassRunner.class)
  @SpringApplicationConfiguration(
    classes=ReadingListApplication.class
  )
  @WebIntegrationTest(randomPort=true)
  public class ServerWebTests {
    private static FirefoxDriver browser;
    @Value("${local.server.port}")
    private int port;
    @BeforeClass
    public static void openBrowser() {
      browser = new FirefoxDriver();
      browser.manage().timeouts()
        .implicitlyWait(10, TimeUnit.SECONDS);    
    }
    @AfterClass
    public static void closeBrowser() {
      browser.quit();
    }
  }
  ```

  和之前更简单的Web测试一样，这个类添加了@WebIntegrationTest注解，将randomPort 设置为true，这样应用程序启动后会运行一个监听随机端口的服务器。同样，端口号注入port 属性，这样就能用它来构造指向运行中应用程序的URL了静态方法

  openBrowser()会创建一个FirefoxDriver的实例，它将打开Firefox浏览器(需 要在运行测试的服务器上安装该浏览器)。测试方法将通过FirefoxDriver实例来执行浏 览器操作。在页面上查找元素时，FirefoxDriver配置了10秒的等候时间(以防元素加载过慢)。

  测试执行完毕需要关闭Firefox浏览器。因此要在closeBrowser()里要调用 FirefoxDriver实例的quit()方法，关闭浏览器

  > 选择浏览器 虽然我们用Firefox进行了测试，但Selenium还提供了不少其他浏览器 的驱动，包括IE、Google的Chrome，还有Apple的Safari。测试可以使用其他浏览器

- 想要加载首页，填充并发送表单，然后判 断登录的页面是否包含刚刚添加的新书

  ```java
  @Test
  public void addBookToEmptyList() {
  	String baseUrl = "http://localhost:" + port;
    browser.get(baseUrl);
    assertEquals("You have no books in your book list",
              browser.findElementByTagName("div").getText());
    browser.findElementByName("title")
      .sendKeys("BOOK TITLE");
    browser.findElementByName("author")
  		.sendKeys("BOOK AUTHOR");
    browser.findElementByName("isbn")
  		.sendKeys("1234567890");
    browser.findElementByName("description")
  		.sendKeys("DESCRIPTION");
    browser.findElementByTagName("form")
  		.submit();
    
    WebElement dl = browser.findElementByCssSelector("dt.bookHeadline");
    assertEquals("BOOK TITLE by BOOK AUTHOR (ISBN: 1234567890)",
                 dl.getText());
  	WebElement dt = 	
      browser.findElementByCssSelector("dd.bookDescription");
    assertEquals("DESCRIPTION", dt.getText());
  }
  ```

  该测试方法所做的第一件事是使用FirefoxDriver来发起GET请求，获取阅读列表的主页， 随后查找页面里的一个`<div>`元素，从它的文本里判断列表里没有图书

  接下来的几行查找表单里的元素，使用驱动的sendKeys()方法模拟敲击键盘事件(实际上 就是用给定的值填充那些表单域)。最后，找到`<form>`元素并提交

  提交的表单经处理后，浏览器就会跳到一个页面，上面的列表包含了新添加的图书。因此最后几行查找列表里的`<dt>`和`<dd>`元素，判断其中是否包含测试表单里提交的数据

  这个测试里最值得注意的是，@WebIntegrationTest可以为我们启动应用程序和服务器， 这样Selenium才可以用Web浏览器执行测试。但真正有趣的是你可以使用IDE的测试功能来运行 测试，运行几次都行，无需依赖构建过程中的某些插件启动服务器