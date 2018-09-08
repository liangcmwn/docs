# Part II. Spring 4.x的新功能
Part II. What’s New in Spring Framework 4.x

This chapter provides an overview of the new features and improvements that have been introduced with Spring Framework 4.3. If you are interested in more details, please see the link: Issue Tracker tickets that were resolved as part of the 4.3 development process.

# 3. Spring 4.0增强和新功能
# 3. New Features and Enhancements in Spring Framework 4.0  

Spring框架第一个版本发布于2004年，自发布以来已历经三个主要版本更新:Spring 2.0提供了XML命名空间和AspectJ支持；Spring 2.5增加了注释驱动（annotation-driven）的配置支持；Spring 3.0增加了对Java 5+版本的支持和@Configuration模型。

The Spring Framework was first released in 2004; since then there have been significant major revisions: Spring 2.0 provided XML namespaces and AspectJ support; Spring 2.5 embraced annotation-driven configuration; Spring 3.0 introduced a strong Java 5+ foundation across the framework codebase, and features such as the Java-based @Configuration model.

Spring 4.0是最新的主要版本，并且首次完全支持Java 8的特性。你仍然可以使用老版本的Java，但是最低版本的要求已经提高到Java SE 6。我们也借主要版本更新的机会删除了许多过时的类和方法。

Version 4.0 is the latest major release of the Spring Framework and the first to fully support Java 8 features. You can still use Spring with older versions of Java, however, the minimum requirement has now been raised to Java SE 6. We have also taken the opportunity of a major release to remove many deprecated classes and methods.

你可以在 Spring Wiki文档 上查看 升级Spring 4.0迁移指南。

A migration guide for upgrading to Spring 4.0 is available on the Spring Framework GitHub Wiki.

## 3.1 改进的入门体验
3.1 Improved Getting Started Experience

新的 spring.io 网站提供了一整个系列的 "入门指南" 帮助你学习Spring。你可以本文档的 Spring 入门概述 一节阅读更多的入门指南。新网站还提供了Spring之下其他额外项目的一个全面的概述。

The new spring.io website provides a whole series of "Getting Started" guides to help you learn Spring. You can read more about the guides in the Chapter 1, Getting Started with Spring section in this document. The new website also provides a comprehensive overview of the many additional projects that are released under the Spring umbrella.

如果你是一个Maven用户，你可能会对 BOM 这个有用的POM文件感兴趣， 这个文件已经与每个Spring的发布版发布。

If you are a Maven user you may also be interested in the helpful bill of materials POM file that is now published with each Spring Framework release.

## 3.2 移除过时的包和方法
3.2 Removed Deprecated Packages and Methods

所有过时的包和许多过时的类和方法已经从Spring4中移除。如果你从之前的发布版升级Spring，你需要保证已经修复了所有使用过时的API方法。
All deprecated packages, and many deprecated classes and methods have been removed with version 4.0. If you are upgrading from a previous release of Spring, you should ensure that you have fixed any deprecated calls that you were making to outdated APIs.

查看完整的变化： API差异报告。

For a complete set of changes, check out the API Differences Report.

请注意，所有可选的第三方依赖都已经升级到了最低2010/2011(例如Spring4通常只支持2010年的最新或者现在的最新发布版本):尤其是 Hibernate 3.6+、EhCache 2.1+、Quartz 1.8+、Groovy 1.8+、Joda-Time 2.0+。但是有一个例外，Spring4依赖最近的Hibernate Validator 4.3+，现在对Jackson的支持集中在2.0+版本 (Spring3.2支持的Jackson 1.8/1.9，现在已经过时）。

Note that optional third-party dependencies have been raised to a 2010/2011 minimum (i.e. Spring 4 generally only supports versions released in late 2010 or later now): notably, Hibernate 3.6+, EhCache 2.1+, Quartz 1.8+, Groovy 1.8+, and Joda-Time 2.0+. As an exception to the rule, Spring 4 requires the recent Hibernate Validator 4.3+, and support for Jackson has been focused on 2.0+ now (with Jackson 1.8/1.9 support retained for the time being where Spring 3.2 had it; now just in deprecated form).

## 3.3 Java 8 (以及6和7)
3.3 Java 8 (as well as 6 and 7)  

Spring4支持Java8的一些特性。你可以在Spring的回调接口中使用 lambda 表达式 和 方法引用。支持java.time (JSR-310)的值类型和一些改进过的注解，例如@Repeatable。你还可以使用Java8的参数名称发现机制（基于-parameters编译器标志）。

Spring Framework 4.0 provides support for several Java 8 features. You can make use of lambda expressions and method references with Spring’s callback interfaces. There is first-class support for java.time (JSR-310), and several existing annotations have been retrofitted as @Repeatable. You can also use Java 8’s parameter name discovery (based on the -parameters compiler flag) as an alternative to compiling your code with debug information enabled.

Spring仍然兼容老版本的Java和JDK：Java SE 6（具体来说，支持JDK6 update 18）以上版本，我们建议新的基于Spring4的项目使用Java7或Java8。

Spring remains compatible with older versions of Java and the JDK: concretely, Java SE 6 (specifically, a minimum level equivalent to JDK 6 update 18, as released in January 2010) and above are still fully supported. However, for newly started development projects based on Spring 4, we recommend the use of Java 7 or 8.

As of late 2017, JDK 6 is being phased out and therefore also Spring’s JDK 6 support. Oracle as well as IBM will terminate all commercial support efforts for JDK 6 in 2018. While Spring will retain its JDK 6 runtime compatibility for the entire 4.3.x line, we require an upgrade to JDK 7 or higher for any further support beyond this point: in particular for JDK 6 specific bug fixes or other issues where an upgrade to JDK 7 addresses the problem.

## 3.4 Java EE 6和7
3.4 Java EE 6 and 7

Java EE 6 或以上版本是Spring4的底线,与JPA2.0和Servlet3.0规范有着特殊的意义。为了保持与Google App Engine和旧的应用程序服务器兼容,Spring4可以部署在Servlet2.5运行环境。但是我们强烈的建议您在Spring测试和模拟测试的开发环境中使用Servlet3.0+。

Java EE version 6 or above is now considered the baseline for Spring Framework 4, with the JPA 2.0 and Servlet 3.0 specifications being of particular relevance. In order to remain compatible with Google App Engine and older application servers, it is possible to deploy a Spring 4 application into a Servlet 2.5 environment. However, Servlet 3.0+ is strongly recommended and a prerequisite in Spring’s test and mock packages for test setups in development environments.

[Note]
如果你是WebSphere 7的用户，一定要安装JPA2.0功能包。在WebLogic 10.3.4或更高版本，安装附带的JPA2.0补丁。这样就可以将这两种服务器变成Spring4兼容的部署环境。

If you are a WebSphere 7 user, be sure to install the JPA 2.0 feature pack. On WebLogic 10.3.4 or higher, install the JPA 2.0 patch that comes with it. This turns both of those server generations into Spring 4 compatible deployment environments.

从长远的观点来看，Spring4.0现在支持Java EE 7级别的适用性规范：尤其是JMS 2.0, JTA 1.2, JPA 2.1, Bean Validation 1.1 和JSR-236并发工具类。像往常一样，支持的重点是独立的使用这些规范。例如在Tomcat或者独立环境中。但是，当把Spring应用部署到Java EE 7服务器时它同样适用。

On a more forward-looking note, Spring Framework 4.0 supports the Java EE 7 level of applicable specifications now: in particular, JMS 2.0, JTA 1.2, JPA 2.1, Bean Validation 1.1, and JSR-236 Concurrency Utilities. As usual, this support focuses on individual use of those specifications, e.g. on Tomcat or in standalone environments. However, it works equally well when a Spring application is deployed to a Java EE 7 server.

注意，Hibernate 4.3是JPA 2.1的提供者，因此它只支持Spring4。同样适用用于作为Bean Validation 1.1提供者的Hibernate Validator 5.0。这两个都不支持Spring3.2。

Note that Hibernate 4.3 is a JPA 2.1 provider and therefore only supported as of Spring Framework 4.0. The same applies to Hibernate Validator 5.0 as a Bean Validation 1.1 provider. Neither of the two are officially supported with Spring Framework 3.2.

## 3.5 Groovy DSL定义Bean
3.5 Groovy Bean Definition DSL

Spring4.0支持使用Groovy DSL来进行外部的bean定义配置。这在概念上类似于使用XML的bean定义，但是支持更简洁的语法。使用Groovy还允许您轻松地将bean定义直接嵌入到引导代码中。例如：

Beginning with Spring Framework 4.0, it is possible to define external bean configuration using a Groovy DSL. This is similar in concept to using XML bean definitions but allows for a more concise syntax. Using Groovy also allows you to easily embed bean definitions directly in your bootstrap code. For example:

```groovy
def reader = new GroovyBeanDefinitionReader(myApplicationContext)
reader.beans {
    dataSource(BasicDataSource) {
        driverClassName = "org.hsqldb.jdbcDriver"
        url = "jdbc:hsqldb:mem:grailsDB"
        username = "sa"
        password = ""
        settings = [mynew:"setting"]
    }
    sessionFactory(SessionFactory) {
        dataSource = dataSource
    }
    myService(MyService) {
        nestedBean = { AnotherBean bean ->
            dataSource = dataSource
        }
    }
}
```
有关更多信息，请参阅 GroovyBeanDefinitionReader javadocs.

For more information consult the GroovyBeanDefinitionReader javadocs.

## 3.6 核心容器改进
3.6 Core Container Improvements

有几种对核心容器的常规改进：

There have been several general improvements to the core container:

* Spring现在注入Bean的时候把 泛型类型 当成一种形式的 限定符。例如：如果你使用Spring Data Repository你可以方便的插入特定的实现：@Autowired Repository<Customer> customerRepository。

Spring now treats generic types as a form of qualifier when injecting Beans. For example, if you are using a Spring Data Repository you can now easily inject a specific implementation: @Autowired Repository<Customer> customerRepository.

* 如果你使用Spring的元注解支持，你现在可以开发自定义注解来公开源注解的特定属性。

If you use Spring’s meta-annotation support, you can now develop custom annotations that expose specific attributes from the source annotation.

* 当自动装配到lists和arrays时，Beans现在可以被 排序 了。支持@Order注解和Ordered接口两种方式。

Beans can now be ordered when they are autowired into lists and arrays. Both the @Order annotation and Ordered interface are supported.

* @Lazy注解现在可以用在注入点以及@Bean定义上。

The @Lazy annotation can now be used on injection points, as well as on @Bean definitions.

* 引入@Description注解,开发人员可以使用基于Java方式的配置。

The @Description annotation has been introduced for developers using Java-based configuration.

* 根据条件筛选Beans的广义模型通过@Conditional注解加入。这和@Profile支持的类似，但是允许以编程式开发用户定义的策略。

A generalized model for conditionally filtering beans has been added via the @Conditional annotation. This is similar to @Profile support but allows for user-defined strategies to be developed programmatically.

* 基于CGLIB的代理类不在需要默认的构造方法。这个支持是由 objenesis库提供。这个库重新打包到Spring框架中，作为Spring框架的一部分发布。通过这个策略，针对代理实例被调用没有构造可言了。

CGLIB-based proxy classes no longer require a default constructor. Support is provided via the objenesis library which is repackaged inline and distributed as part of the Spring Framework. With this strategy, no constructor at all is being invoked for proxy instances anymore.

* 框架现在支持管理时区。例如LocaleContext。

There is managed time zone support across the framework now, e.g. on LocaleContext.

## 3.7 常规Web改进
3.7 General Web Improvements

现在仍然可以部署到Servlet 2.5服务器，但是Spring4.0现在主要集中在Servlet 3.0+环境。如果你使用Spring MVC测试框架，你需要将Servlet 3.0兼容的JAR包放到 测试的classpath下。

Deployment to Servlet 2.5 servers remains an option, but Spring Framework 4.0 is now focused primarily on Servlet 3.0+ environments. If you are using the Spring MVC Test Framework you will need to ensure that a Servlet 3.0 compatible JAR is in your test classpath.

除了稍后会提到的WebSocket支持外，下面的常规改进已经加入到Spring的Web模块：

In addition to the WebSocket support mentioned later, the following general improvements have been made to Spring’s Web modules:

你可以在Spring MVC应用中使用新的@RestController注解，不在需要给@RequestMapping的方法添加@ResponseBody注解。

You can use the new @RestController annotation with Spring MVC applications, removing the need to add @ResponseBody to each of your @RequestMapping methods.

* AsyncRestTemplate类已被添加进来，当开发REST客户端时，允许非阻塞异步支持。

The AsyncRestTemplate class has been added, allowing non-blocking asynchronous support when developing REST clients.

* 当开发Spring MVC应用时，Spring现在提供了全面的时区支持 。

Spring now offers comprehensive timezone support when developing Spring MVC applications.

## 3.8 WebSocket、SockJS和STOMP消息
3.8 WebSocket, SockJS, and STOMP Messaging

一个新的spring-websocket模块提供了全面的基于WebSocket和在Web应用的客户端和服务器之间双向通信的支持。它和Java WebSocket API JSR-356兼容，此外还提供了当浏览器不支持WebSocket协议时的基于SockJS的备用选项。

A new spring-websocket module provides comprehensive support for WebSocket-based, two-way communication between client and server in web applications. It is compatible with JSR-356, the Java WebSocket API, and in addition provides SockJS-based fallback options (i.e. WebSocket emulation) for use in browsers that don’t yet support the WebSocket protocol (e.g. Internet Explorer < 10).

一个新的spring-messaging模块添加了支持STOMP作为WebSocket子协议用于在应用中使用注解编程模型路由和处理从WebSocket客户端发送的STOMP消息。由于@Controller现在可以同时包含@RequestMapping和@MessageMapping方法用于处理HTTP请求和来自WebSocket连接客户端发送的消息。新的spring-messaging模块还包含了来自以前Spring集成项目的关键抽象，例如Message、MessageChannel、MessageHandler和其他作为基于消息传递的应用程序的基础。

A new spring-messaging module adds support for STOMP as the WebSocket sub-protocol to use in applications along with an annotation programming model for routing and processing STOMP messages from WebSocket clients. As a result an @Controller can now contain both @RequestMapping and @MessageMapping methods for handling HTTP requests and messages from WebSocket-connected clients. The new spring-messaging module also contains key abstractions formerly from the Spring Integration project such as Message, MessageChannel, MessageHandler, and others to serve as a foundation for messaging-based applications.

欲知详情以及较全面的介绍，请参见Chapter 20, WebSocket 支持一节。

For further details, including a more thorough introduction, see the Chapter 26, WebSocket Support section.

## 3.9 测试改进
3.9 Testing Improvements

除了精简spring-test模块中过时的代码外，Spring4还引入了几个用于单元测试和集成测试的新功能。

In addition to pruning of deprecated code within the spring-test module, Spring Framework 4.0 introduces several new features for use in unit and integration testing.

几乎spring-test模块中所有的注解（例如：@ContextConfiguration、@WebAppConfiguration、@ContextHierarchy、@ActiveProfiles等等)现在可以用作元注解来创建自定义的composed annotations并且可以减少测试套件的配置。

Almost all annotations in the spring-test module (e.g., @ContextConfiguration, @WebAppConfiguration, @ContextHierarchy, @ActiveProfiles, etc.) can now be used as meta-annotations to create custom composed annotations and reduce configuration duplication across a test suite.

现在可以以编程方式解决Bean定义配置文件的激活。只需要实现一个自定义的ActiveProfilesResolver，并且通过@ActiveProfiles的resolver属性注册。

Active bean definition profiles can now be resolved programmatically, simply by implementing a custom ActiveProfilesResolver and registering it via the resolver attribute of @ActiveProfiles.

新的SocketUtils类被引入到了spring-core模块。这个类可以使你能够扫描本地主机的空闲的TCP和UDP服务端口。这个功能不是专门用在测试的，但是可以证明在你使用Socket写集成测试的时候非常有用。例如测试内存中启动的SMTP服务器，FTP服务器，Servlet容器等。

A new SocketUtils class has been introduced in the spring-core module which enables you to scan for free TCP and UDP server ports on localhost. This functionality is not specific to testing but can prove very useful when writing integration tests that require the use of sockets, for example tests that start an in-memory SMTP server, FTP server, Servlet container, etc.

从Spring 4.0开始,org.springframework.mock.web包中的一套mock是基于Servlet 3.0 API。此外，一些Servlet API mocks（例如：MockHttpServletRequest、MockServletContext等等）已经有一些小的改进更新，提高了可配置性。

As of Spring 4.0, the set of mocks in the org.springframework.mock.web package is now based on the Servlet 3.0 API. Furthermore, several of the Servlet API mocks (e.g., MockHttpServletRequest, MockServletContext, etc.) have been updated with minor enhancements and improved configurability.

# 4. Spring 4.1增强和新功能
# 4. New Features and Enhancements in Spring Framework 4.1

Version 4.1 included a number of improvements, as described in the following sections:

## 4.1 JMS改进
4.1 JMS Improvements

Spring 4.1引入了一个更简单的基础架构，使用 @JmsListener注解bean方法来注册JMS监听端点。XML命名空间已经通过增强来支持这种新的方式（jms:annotation-driven），它也可以完全通过Java配置( @EnableJms, JmsListenerContainerFactory)来配置架构。也可以使用 JmsListenerConfigurer注解来注册监听端点。

Spring 4.1 introduces a much simpler infrastructure to register JMS listener endpoints by annotating bean methods with @JmsListener. The XML namespace has been enhanced to support this new style (jms:annotation-driven), and it is also possible to fully configure the infrastructure using Java config (@EnableJms, JmsListenerContainerFactory). It is also possible to register listener endpoints programmatically using JmsListenerConfigurer.

Spring 4.1还调整了JMS的支持，使得你可以从spring-messaging在Spring4.0引入的抽象获益，即：

Spring 4.1 also aligns its JMS support to allow you to benefit from the spring-messaging abstraction introduced in 4.0, that is:

* 消息监听端点可以有更为灵活的签名，并且可以从标准的消息注解获益，例如@Payload、@Header、@Headers和@SendTo注解。另外，也可以使用一个标准的消息，以代替javax.jms.Message作为方法参数。

Message listener endpoints can have a more flexible signature and benefit from standard messaging annotations such as @Payload, @Header, @Headers, and @SendTo. It is also possible to use a standard Message in lieu of javax.jms.Message as method argument.

* 一个新的可用 JmsMessageOperations接口和允许操作使用Message抽象的JmsTemplate。

A new JmsMessageOperations interface is available and permits JmsTemplate like operations using the Message abstraction.

最后，Spring 4.1提供了其他各种各样的改进：

Finally, Spring 4.1 provides additional miscellaneous improvements:

* JmsTemplate中的同步请求-答复操作支持  
Synchronous request-reply operations support in JmsTemplate
* 监听器的优先权可以指定每个<jms:listener/>元素  
Listener priority can be specified per <jms:listener/> element
* 消息侦听器容器恢复选项可以通过使用 BackOff 实现进行配置  
Recovery options for the message listener container are configurable using a BackOff implementation
* JMS 2.0消费者支持共享  
JMS 2.0 shared consumers are supported

## 4.2 Caching（缓存）改进
4.2 Caching Improvements

Spring 4.1 支持JCache (JSR-107)注解使用Spring的现有缓存配置和基础结构的抽象；使用标准注解不需要任何更改。

Spring 4.1 supports JCache (JSR-107) annotations using Spring’s existing cache configuration and infrastructure abstraction; no changes are required to use the standard annotations.

#### Spring 4.1也大大提高了自己的缓存抽象：  
Spring 4.1 also improves its own caching abstraction significantly:

* 缓存可以在运行时使用CacheResolver解决。因此使用value参数定义的缓存名称不在是强制性的。
更多的操作级自定义项：缓存解析器，缓存管理器，键值生成器  
Caches can be resolved at runtime using a CacheResolver. As a result the value argument defining the cache name(s) to use is no longer mandatory.

* 一个新的@CacheConfig类级别注解允许在类级别上共享常用配置，不需要启用任何缓存操作。  
More operation-level customizations: cache resolver, cache manager, key generator
A new @CacheConfig class-level annotation allows common settings to be shared at the class level without enabling any cache operation.

* 使用CacheErrorHandler更好的处理缓存方法的异常  
Better exception handling of cached methods using CacheErrorHandler

* Spring 4.1为了在CacheInterface添加一个新的putIfAbsent方法也做了重大的更改。  
Spring 4.1 also has a breaking change in the Cache interface as a new putIfAbsent method has been added.

## 4.3 Web改进
4.3 Web Improvements

* 新的抽象ResourceResolver, ResourceTransformer和ResourceUrlProvider扩展了已存在的基于ResourceHttpRequestHandler的资源处理程序。一些内置的实现提供了对带版本的资源URL（为了有效的HTTP缓存）、定位gzip资源、生成HTML 5 AppCache清单等的支持。参考21.16.9 资源服务。  
The existing support for resource handling based on the ResourceHttpRequestHandler has been expanded with new abstractions ResourceResolver, ResourceTransformer, and ResourceUrlProvider. A number of built-in implementations provide support for versioned resource URLs (for effective HTTP caching), locating gzipped resources, generating an HTML 5 AppCache manifests, and more. See Section 16.16.7, “资源服务”.

* JDK 1.8的java.util.Optional现在支持@RequestParam, @RequestHeader和@MatrixVariable控制器方法的参数。  
JDK 1.8’s java.util.Optional is now supported for @RequestParam, @RequestHeader, and @MatrixVariable controller method arguments.
* ListenableFuture作为返回值替代了DeferredResult，在这方面一项基础服务（或者说对AsyncRestTemplate的调用）已经返回了ListenableFuture。  
ListenableFuture is supported as a return value alternative to DeferredResult where an underlying service (or perhaps a call to AsyncRestTemplate) already returns ListenableFuture.
* @ModelAttribute方法现在按照依赖间的顺序依次被调用。
@ModelAttribute methods are now invoked in an order that respects inter-dependencies. See SPR-6299.
* Jackson的@JsonView直接作用于@ResponseBody和ResponseEntity控制器方法，用于序列化同一个POJO的不同形式（比如，汇总和详情）。这可以通过为模型属性添加指定key的序列化视图类型来渲染视图。参考Jackson序列化视图支持。
Jackson’s @JsonView is supported directly on @ResponseBody and ResponseEntity controller methods for serializing different amounts of detail for the same POJO (e.g. summary vs. detail page). This is also supported with View-based rendering by adding the serialization view type as a model attribute under a special key. See the section called “支持 Jackson 序列化视图” for details.

* Jackson现在支持JSONP。参考Jackson JSONP支持。
JSONP is now supported with Jackson. See the section called “支持 Jackson JSONP”.

* 新的生命周期选项可用于在控制器方法返回后且响应写出前拦截@ResponseBody和ResponseEntity方法，声明一个@ControllerAdvice bean实现ResponseBodyAdvice即可，内置的@JsonView和JSONP恰恰利用了这点。参考21.4.1 使用HandlerInterceptor拦截请求。

A new lifecycle option is available for intercepting @ResponseBody and ResponseEntity methods just after the controller method returns and before the response is written. To take advantage declare an @ControllerAdvice bean that implements ResponseBodyAdvice. The built-in support for @JsonView and JSONP take advantage of this. See Section 16.4.1, “使用 HandlerInterceptor 拦截请求”.

* 有三个HttpMessageConverter选项：   
There are three new HttpMessageConverter options:
  * Gson——比Jackson更轻的足迹，已用于Spring Android中。   
  Gson — lighter footprint than Jackson; has already been in use in Spring Android.

  * Google协议缓冲——企业内部有效的服务间通信数据协议，但是也可以作为JSON和XML暴露于浏览器中。
Google Protocol Buffers — efficient and effective as an inter-service communication data protocol within an enterprise but can also be exposed as JSON and XML for browsers.

  * 通过jackson-dataformat-xml扩展支持基于XML的Jackson。当使用@EnableWebMvc或<mvc:annotation-driven>时，如果classpath下存在jackson-dataformat-xml则默认会替代JAXB2。
Jackson based XML serialization is now supported through the jackson-dataformat-xml extension. When using @EnableWebMvc or <mvc:annotation-driven/>, this is used by default instead of JAXB2 if jackson-dataformat-xml is in the classpath.

* 类似JSP的视图现在可以通过引用控制器映射的名称与控制器建立链接。默认的名称将被赋给每一个@RequestMapping。例如，FooController拥有方法handleFoo，它的名称为“FC#handleFoo”。命名策略是可插拔的，也可以通过name属性为@RequestMapping明确地命名。在Spring JSP标签库中新的mvcUrl功能可以让使用JSP页面变得更方便。参考21.7.2 从视图为Controller及其方法创建URI。

Views such as JSPs can now build links to controllers by referring to controller mappings by name. A default name is assigned to every @RequestMapping. For example FooController with method handleFoo is named "FC#handleFoo". The naming strategy is pluggable. It is also possible to name an @RequestMapping explicitly through its name attribute. A new mvcUrl function in the Spring JSP tag library makes this easy to use in JSP pages. See Section 16.7.2, “Building URIs to Controllers and methods from views”.

* ResponseEntity提供了创建者风格的API用于引导控制器方法为服务端响应做准备。例如，ResponseEntity.ok()。

ResponseEntity provides a builder-style API to guide controller methods towards the preparation of server-side responses, e.g. ResponseEntity.ok().

* RequestEntity是一种新类型，它提供了创建者风格的API用于引导客户端REST代码为HTTP请求做准备。
RequestEntity is a new type that provides a builder-style API to guide client-side REST code towards the preparation of HTTP requests.

* MVC Java配置与XML命名空间： 
MVC Java config and XML namespace:
  * 视图解析器可以被配置，包含对内容协商的支持。参考21.16.8 视图解析器。
View resolvers can now be configured including support for content negotiation, see Section 16.16.6, “视图解析器”.
  * 视图控制器内置了对重定向及设置响应状态的支持。应用程序可以使用它配置重定向的URL，用视图渲染 404 响应，发送“无内容”响应，等等。一些用例请点击这里。  
View controllers now have built-in support for redirects and for setting the response status. An application can use this to configure redirect URLs, render 404 responses with a view, send "no content" responses, etc. Some use cases are listed here.
  * 内置了自定义的路径匹配。参考21.16.11 路径匹配。  
Path matching customizations are frequently used and now built-in. See Section 16.16.9, “Path Matching”.
* 支持Groovy标记模板（基于Groovy 2.3）。参考GroovyMarkupConfigurer和各自的ViewResolver及视图实现 。  
Groovy markup template support (based on Groovy 2.3). See the GroovyMarkupConfigurer and respecitve ViewResolver and ‘View’ implementations.

## 4.4 WebSocket 消息处理的改进
4.4 WebSocket Messaging Improvements
* 支持SockJS（Java）客户端。参考SockJsClient和同包下的类。
SockJS (Java) client-side support. See SockJsClient and classes in same package.
* 当STOMP客户端订阅和取消订阅时新的应用上下文事件SessionSubscribeEvent和SessionUnsubscribeEvent会被触发。
New application context events SessionSubscribeEvent and SessionUnsubscribeEvent published when STOMP clients subscribe and unsubscribe.
* 新的作用域“websocket”。参考25.4.14 WebSocket作用域。
New "websocket" scope. See Section 26.4.16, “WebSocket Scope”.
* @SendToUser只能把单会话作为目标，而且不需要用户身份验证。
@SendToUser can target only a single session and does not require an authenticated user.
* @MessageMapping方法可以使用点“.”代替斜杠“/”作为分割符。参考SPR-11660。
@MessageMapping methods can use dot "." instead of slash "/" as path separator. See SPR-11660.
* STOMP/WebSocket监测信息收集和日志管理。参考25.4.16 运行时监测。
STOMP/WebSocket monitoring info collected and logged. See Section 26.4.18, “Monitoring”.
* 得到极大优化和改进的日志管理保留了可读性和简洁性，甚至是在DEBUG水平。
Significantly optimized and improved logging that should remain very readable and compact even at DEBUG level.
* 优化了消息的创建，包含了对临时消息可变性的支持，并避免自动消息id和时间戳的创建。参考Javadoc中的MessageHeaderAccessor。
Optimized message creation including support for temporary message mutability and avoiding automatic message id and timestamp creation. See Javadoc of MessageHeaderAccessor.
* 在WebSocket会话创建60秒后没有活动则将会关闭STOMP/WebSocket连接。参考SPR-11884。
Close STOMP/WebSocket connections that have no activity within 60 seconds after the WebSocket session is established. See SPR-11884.

## 4.5 测试改进
4.5 Testing Improvements
* Groovy脚本现在可用于配置ApplicationContext，其中ApplicationContext在测试上下文框架中被加载用于集成测试。参考带有Groovy脚本的上下文配置。
Groovy scripts can now be used to configure the ApplicationContext loaded for integration tests in the TestContext framework.
See the section called “Context configuration with Groovy scripts” for details.

* 在事务测试方法中可以通过TestTransaction API编程式地开始和结束测试事务。参考编程式事务管理。
Test-managed transactions can now be programmatically started and ended within transactional test methods via the new TestTransaction API.
See the section called “Programmatic transaction management” for details.

* SQL脚本执行可以通过在每个类或方法上添加新的@Sql和@SqlConfig注解声明式地配置。参考14.5.7执行SQL脚本。
SQL script execution can now be configured declaratively via the new @Sql and @SqlConfig annotations on a per-class or per-method basis.  
See Section 15.5.8, “Executing SQL scripts” for details.

* 可以通过新的@TestPropertySource注解配置用于测试的property源文件，它能够自动地重写系统和应用的property源文件。参考带有测试property源文件的上下文配置。
Test property sources which automatically override system and application property sources can be configured via the new @TestPropertySource annotation.  
See the section called “Context configuration with test property sources” for details.
* 默认的TestExecutionListeners能够被自动地发现。参考自动发现默认的TestExecutionListeners。
Default TestExecutionListeners can now be automatically discovered.

* 自定义的TestExecutionListeners能够被自动地合并到默认的监听器中。参考合并TestExecutionListeners。
See the section called “Automatic discovery of default TestExecutionListeners” for details.
Custom TestExecutionListeners can now be automatically merged with the default listeners.

See the section called “Merging TestExecutionListeners” for details.

* 测试上下文框架中事务测试的文档提供了更多深入的解释和附加的案例。参考14.5.6 事务管理。
The documentation for transactional testing support in the TestContext framework has been improved with more thorough explanations and additional examples.

See Section 15.5.7, “Transaction management” for details.

* 对MockServletContext, MockHttpServletRequest和其它Servlet API模拟的各种各样的改进。
Various improvements to MockServletContext, MockHttpServletRequest, and other Servlet API mocks.
* AssertThrows被重构了用于支持Throwable而不是Exception。
AssertThrows has been refactored to support Throwable instead of Exception.

* 在Spring MVC测试中，JSON Assert作为使用JSONPath的额外选项，可以为JSON响应断言，这就像使用XMLUnit为XML断言一样。
In Spring MVC Test, JSON responses can be asserted with JSON Assert as an extra option to using JSONPath much like it has been possible to do for XML with XMLUnit.

* 可以通过MockMvcConfigurer创建MockMvcBuilder。这使得应用Spring安全设置变得很容易，也可用于把通用设置压缩进任何第三方框架或项目中。
MockMvcBuilder recipes can now be created with the help of MockMvcConfigurer. This was added to make it easy to apply Spring Security setup but can be used to encapsulate common setup for any 3rd party framework or within a project.

* MockRestServiceServer现在支持AsyncRestTemplate用于客户端测试。
MockRestServiceServer now supports the AsyncRestTemplate for client-side testing.

# 5. New Features and Enhancements in Spring Framework 4.2
5. Spring 4.2的新特性和增强功能
Version 4.2 included a number of improvements, as described in the following sections:

Section 5.1, “Core Container Improvements”
Section 5.2, “Data Access Improvements”
Section 5.3, “JMS Improvements”
Section 5.4, “Web Improvements”
Section 5.5, “WebSocket Messaging Improvements”
Section 5.6, “Testing Improvements”

## 5.1 核心容器的改进
5.1 Core Container Improvements
* 类似@Bean的注解被发现并用于处理Java 8的默认方法，允许实现接口的配置类带有默认的@Bean方法。（译者注：@Bean注解可以用到Java 8接口的默认方法上，然后配置类实现这个接口一样可以得到bean）
Annotations such as @Bean get detected and processed on Java 8 default methods as well, allowing for composing a configuration class from interfaces with default @Bean methods.

* 配置类现在可以声明@Import引入普通的组件类了，允许混合引入配置类和组件类。（译者注：@Import以前只能引入配置类，现在也可以引入没有任何注解的组件类）
Configuration classes may declare @Import with regular component classes now, allowing for a mix of imported configuration classes and component classes.

* 配置类可以声明一个@Order值，按照一定的顺序处理（比如，按名称重写bean），甚至是在classpath扫描的时候。（译者注：@Order值大的会覆盖小的）
Configuration classes may declare an @Order value, getting processed in a corresponding order (e.g. for overriding beans by name) even when detected through classpath scanning.

* @Resource注入的元素支持@Lazy声明，像@Autowired一样，对请求目标的bean接受延迟初始化的代理。
@Resource injection points support an @Lazy declaration, analogous to @Autowired, receiving a lazy-initializing proxy for the requested target bean.

* 应用程序事件现在提供基于注解的模型了，也可以发布任何事件。 
The application event infrastructure now offers an annotation-based model as well as the ability to publish any arbitrary event.

  * bean中的任何公共方法都能够通过@EventListener注解来消费事件。
Any public method in a managed bean can be annotated with @EventListener to consume events.

  * @TransactionalEventListener提供了事务绑定的事件支持。
@TransactionalEventListener provides transaction-bound event support.

* Spring 4.2提供了一流的支持用于声明和查找注解属性的别名。新的@AliasFor注解可以用来在单个注解内声明一对别名属性，或者声明一个从自定义注解属性到元注解属性的别名。
Spring Framework 4.2 introduces first-class support for declaring and looking up aliases for annotation attributes. The new @AliasFor annotation can be used to declare a pair of aliased attributes within a single annotation or to declare an alias from one attribute in a custom composed annotation to an attribute in a meta-annotation.

  * 以下注解都通过@AliasFor翻新过了，以便为value属性提供更有意义的别名：@Cacheable, @CacheEvict, @CachePut, @ComponentScan, @ComponentScan.Filter, @ImportResource, @Scope, @ManagedResource, @Header, @Payload, @SendToUser, @ActiveProfiles, @ContextConfiguration, @Sql, @TestExecutionListeners, @TestPropertySource, @Transactional, @ControllerAdvice, @CookieValue, @CrossOrigin, @MatrixVariable, @RequestHeader, @RequestMapping, @RequestParam, @RequestPart, @ResponseStatus, @SessionAttributes, @ActionMapping, @RenderMapping, @EventListener, @TransactionalEventListener。
The following annotations have been retrofitted with @AliasFor support in order to provide meaningful aliases for their value attributes: @Cacheable, @CacheEvict, @CachePut, @ComponentScan, @ComponentScan.Filter, @ImportResource, @Scope, @ManagedResource, @Header, @Payload, @SendToUser, @ActiveProfiles, @ContextConfiguration, @Sql, @TestExecutionListeners, @TestPropertySource, @Transactional, @ControllerAdvice, @CookieValue, @CrossOrigin, @MatrixVariable, @RequestHeader, @RequestMapping, @RequestParam, @RequestPart, @ResponseStatus, @SessionAttributes, @ActionMapping, @RenderMapping, @EventListener, @TransactionalEventListener.

  * 例如，来自spring-test模块的@ContextConfiguration现在定义如下：
For example, @ContextConfiguration from the spring-test module is now declared as follows:
```java
public @interface ContextConfiguration {

    @AliasFor("locations")
    String[] value() default {};

    @AliasFor("value")
    String[] locations() default {};

    // ...
}
```

* 类似地，重写了元注解属性的注解现在也可以使用@AliasFor细粒度地控制那些在注解层次结构中被重写的属性。实际上，现在可以为元注解的value属性声明一个别名。
Similarly, composed annotations that override attributes from meta-annotations can now use @AliasFor for fine-grained control over exactly which attributes are overridden within an annotation hierarchy. In fact, it is now possible to declare an alias for the value attribute of a meta-annotation.

* 例如，现在可以像下面一样开发一种重写了自定义属性的组合注解。
For example, one can now develop a composed annotation with a custom attribute override as follows.
```java
@ContextConfiguration
public @interface MyTestConfig {

    @AliasFor(annotation = ContextConfiguration.class, attribute = "value")
    String[] xmlFiles();

    // ...
}
```
* 参考Spring注解编程模型。
See Spring Annotation Programming Model.

* Spring在发现元注解的搜索算法上做了很多改进。例如，在注解继承体系中可以声明局部的组合注解。
Numerous improvements to Spring’s search algorithms used for finding meta-annotations. For example, locally declared composed annotations are now favored over inherited annotations.

* 重写了元注解属性的组合注解现在可以用在接口、抽象类、桥接和接口方法上，也可以用在类、标准方法、构造方法和字段上。
Composed annotations that override attributes from meta-annotations can now be discovered on interfaces and on abstract, bridge, & interface methods as well as on classes, standard methods, constructors, and fields.

* 代表注解属性的Map（和AnnotationsAttributes实例）可以被合成（或者转换）到一个注解中。
Maps representing annotation attributes (and AnnotationAttributes instances) can be synthesized (i.e., converted) into an annotation.

* 基于字段的数据绑定（DirectFieldAccessor）可以与当前基于属性的数据绑定（BeanWrapper）一起使用。特别地，基于字段的绑定现在支持为集合、数据和Map导航。
The features of field-based data binding (DirectFieldAccessor) have been aligned with the current property-based data binding (BeanWrapper). In particular, field-based binding now supports navigation for Collections, Arrays, and Maps.

* DefaultConversionService为Steam、Charset、Currency和TimeZone提供了可以直接使用的转换器。这些转换器也可以被添加到任意的ConversionService中。
DefaultConversionService now provides out-of-the-box converters for Stream, Charset, Currency, and TimeZone. Such converters can be added individually to any arbitrary ConversionService as well.

* DefaultFormattingConversionService为JSR-354中的货币提供了支持（如果javax.money存在于classpath下），即MonetaryAmount和CurrencyUnit。这也包含对@NumberFormat的支持。
DefaultFormattingConversionService comes with out-of-the-box support for the value types in JSR-354 Money & Currency (if the 'javax.money' API is present on the classpath): namely, MonetaryAmount and CurrencyUnit. This includes support for applying @NumberFormat.

* @NumberFormat现在可以作为元注解使用。
@NumberFormat can now be used as a meta-annotation.

* JavaMailSenderImpl有一个新的方法testConnection()用于检查与服务器间的连接。
JavaMailSenderImpl has a new testConnection() method for checking connectivity to the server.

* ScheduledTaskRegistrar暴露计划的任务。
ScheduledTaskRegistrar exposes scheduled tasks.

* Apache的commons-pool2现在支持AOP池CommonsPool2TargetSource。
Apache commons-pool2 is now supported for a pooling AOP CommonsPool2TargetSource.

* 为脚本化bean引入了StandardScriptFactory作为一个基于JSR-223的机制，暴露于XML中的lang:std元素。对JavaScript和JRuby的支持。（注意：JRubyScriptFactory和lang:jruby现在过时了，请使用JSR-223）
Introduced StandardScriptFactory as a JSR-223 based mechanism for scripted beans, exposed through the lang:std element in XML. Supports e.g. JavaScript and JRuby. (Note: JRubyScriptFactory and lang:jruby are deprecated now, in favor of using JSR-223.)

## 5.2 Data Access Improvements
5.2 数据访问的改进
* AspectJ现在支持javax.transactional.Transactional。
javax.transaction.Transactional is now supported via AspectJ.

* SimpleJdbcCallOperations现在支持命名绑定。
SimpleJdbcCallOperations now supports named binding.

* 全面支持Hibernate ORM 5.0，作为JPA提供者（自动适配），也支持其原生API（被新的org.springframework.orm.hibernate5包覆盖）。
Full support for Hibernate ORM 5.0: as a JPA provider (automatically adapted) as well as through its native API (covered by the new org.springframework.orm.hibernate5 package).

* 嵌入的数据库现在可以被自动赋值不同的（unique）名字，且<jdbc:embedded-database>支持新的属性database-name。参考下面的“测试的改进”部分。
Embedded databases can now be automatically assigned unique names, and <jdbc:embedded-database> supports a new database-name attribute. See "Testing Improvements" below for further details.

## 5.3 JMS的改进
5.3 JMS Improvements
* autoStartup属性可以通过JmsListenerContainerFactory控制。
* 每个监听器容器都能配置应答Destination的类型。
* @SendTo注解的值现在可以使用SpEL表达式。
* 响应目标可以使用JmsResponse在运行时计算。
* @JmsListener是一个可重复性的注解，可以在同一个方法上声明多个JMS容器（如果你还没有使用Java 8，请使用新引入的@JmsListeners）。

* The autoStartup attribute can be controlled via JmsListenerContainerFactory.
* The type of the reply Destination can now be configured per listener container.
* The value of the @SendTo annotation can now use a SpEL expression.
* The response destination can be computed at runtime using JmsResponse
* @JmsListener is now a repeatable annotation to declare several JMS containers on the same method (use the newly introduced @JmsListeners if you’re not using Java8 yet).

## 5.4 Web的改进
5.4 Web Improvements

* 支持HTTP流和服务器发送事件。参考HTTP流。
HTTP Streaming and Server-Sent Events support, see the section called “HTTP Streaming”.

* 支持内置CORS的全局（MVC Java配置和XML命名空间）和局部（例如，@CrossOrign）配置。参考26 CORS支持。
Built-in support for CORS including global (MVC Java config and XML namespace) and local (e.g. @CrossOrigin) configuration. See Chapter 27, CORS Support for details.

* HTTP缓存更新： 
HTTP caching updates:
  * 新的创建者CacheControl，嵌入到ResponseEntity, WebContentGenerator, ResourceHttpRequestHandler中。
new CacheControl builder; plugged into ResponseEntity, WebContentGenerator, ResourceHttpRequestHandler.

  * 在WebRequest中改进了ETag/Last-Modified的支持。
improved ETag/Last-Modified support in WebRequest.

* 自定义映射注解，使用@RequestMapping作为元注解。
Custom mapping annotations, using @RequestMapping as a meta-annotation.

* AbstractHandlerMethodMapping中的公共方法用于在运行时注册和取消注册请求映射。
Public methods in AbstractHandlerMethodMapping to register and unregister request mappings at runtime.

* AbstractDispatcherServletInitializer中的保护方法createDispatcherServlet进一步自定义DispatcherServlet的实例。
Protected createDispatcherServlet method in AbstractDispatcherServletInitializer to further customize the DispatcherServlet instance to use.

* HandlerMethod作为@ExceptionHandler方法的参数，特别在@ControllerAdvice组件中非常便利。
HandlerMethod as a method argument on @ExceptionHandler methods, especially handy in @ControllerAdvice components.

* java.util.concurrent.CompletableFuture作为@Controller方法的返回类型。
java.util.concurrent.CompletableFuture as an @Controller method return value type.

* HttpHeaders支持字节范围的请求，并提供静态资源。
Byte-range request support in HttpHeaders and for serving static resources.

* @ResponseStatus检测嵌套异常。
@ResponseStatus detected on nested exceptions.

* RestTemplate中的UriTemplateHandler扩展点。 
UriTemplateHandler extension point in the RestTemplate.
  * DefaultUriTemplateHandler暴露了baseUrl属性和路径段编码选项。
DefaultUriTemplateHandler exposes baseUrl property and path segment encoding options.
  * 此扩展点可嵌入到URI模板库中。
the extension point can also be used to plug in any URI template library.
* OkHTTP与RestTemplate集成。
OkHTTP integration with the RestTemplate.

* 自定义的baseUrl可以替代MvcUriComponentsBuilder中的方法。
Custom baseUrl alternative for methods in MvcUriComponentsBuilder.

* 序列化/反序列化的异常信息在WARN级别被记录。
Serialization/deserialization exception messages are now logged at WARN level.

* 默认的JSON前缀从“{}&&”改成了更安全的”)]}’,”中的一个（译者注：此处可能官方文档有误）。
Default JSON prefix has been changed from "{} && " to the safer ")]}', " one.

* 新的扩展点RequestBodyAdvice和内置实现支持@RequestBody方法参数上的Jackson的@JsonView。
New RequestBodyAdvice extension point and built-in implementation to support Jackson’s @JsonView on @RequestBody method arguments.

* 使用GSON或Jackson 2.6+时，处理器方法的返回类型被用于改进参数化类型的序列化，比如List<Foo>。
When using GSON or Jackson 2.6+, the handler method return type is used to improve serialization of parameterized types like List<Foo>.

* 引入了ScriptTemplateView作为JSR-223用于处理脚本web视图的机制，主要关注于Nashorn（JDK 8）上的JavaScript视图模板。
Introduced ScriptTemplateView as a JSR-223 based mechanism for scripted web views, with a focus on JavaScript view templating on Nashorn (JDK 8).

## 5.5 WebSocket消息处理的改进
5.5 WebSocket Messaging Improvements
* 暴露关于已连接用户和订阅存在的信息。 
Expose presence information about connected users and subscriptions:

  * 新的SimpUserRegistry暴露为叫作“userRegistry”的bean。
new SimpUserRegistry exposed as a bean named "userRegistry".
  * 在服务器集群间共享已存在的信息（参考代理中继配置选项）。
sharing of presence information across cluster of servers (see broker relay config options).

* 解决用户在服务器集群中的目的地（参考代理中继配置选项）。
Resolve user destinations across cluster of servers (see broker relay config options).

* StompSubProtocolErrorHandler扩展点用来定制和控制STOMP错误帧到客户端。
StompSubProtocolErrorHandler extension point to customize and control STOMP ERROR frames to clients.

* 通过@ControllerAdvice组件声明的全局方法@MessageExceptionHandler。
Global @MessageExceptionHandler methods via @ControllerAdvice components.

* SpEL表达式“selector”头用于SimpleBrokerMessageHandler的订阅。
Heart-beats and a SpEL expression 'selector' header for subscriptions with SimpleBrokerMessageHandler.

* 通过TCP和WebSocket使用STOMP客户端。参考25.4.13 STOMP客户端。
STOMP client for use over TCP and WebSocket; see Section 26.4.15, “STOMP Client”.

* @SendTo和@SendToUser可以包含多个占位符。
@SendTo and @SendToUser can contain destination variable placeholders.

* Jackson的@JsonView支持在@MessageMapping和@SubscribeMapping方法上返回值。
Jackson’s @JsonView supported for return values on @MessageMapping and @SubscribeMapping methods.

* ListenableFuture和CompletableFuture可以作为@MessageMapping和@SubscribeMapping方法的返回值类型。
ListenableFuture and CompletableFuture as return value types from @MessageMapping and @SubscribeMapping methods.

* MarshallingMessageConverter用于XML负载。
MarshallingMessageConverter for XML payloads.

## 5.6 测试的改进
5.6 Testing Improvements

* 基于JUnit的集成测试现在可以使用JUnit规则执行而不是SpringJUnit4ClassRunner。这使得基于Spring的集成测试可以使用替代runner运行，比如JUnit的Parameterized或第三方的runner如MockitoJUnitRunner。参考Spring JUnit规则。
JUnit-based integration tests can now be executed with JUnit rules instead of the SpringJUnit4ClassRunner. This allows Spring-based integration tests to be run with alternative runners like JUnit’s Parameterized or third-party runners such as the MockitoJUnitRunner.
See the section called “Spring JUnit 4 Rules” for details.

* Spring MVC测试框架现在对HtmlUnit提供了一流的支持，包括集成Selenium的WebDriver，允许基于页面的web应用测试不再需要部署到一个Servlet容器上。参考14.6.2, HtmlUnit的集成。
The Spring MVC Test framework now provides first-class support for HtmlUnit, including integration with Selenium’s WebDriver, allowing for page-based web application testing without the need to deploy to a Servlet container.
See Section 15.6.2, “HtmlUnit Integration” for details.

* AopTestUtils是一个新的测试工具类，它允许开发者可以获取到底层的隐藏在一个或多个Spring代理类下的目标对象。参考13.2.1 通用测试工具类。
AopTestUtils is a new testing utility that allows developers to obtain a reference to the underlying target object hidden behind one or more Spring proxies.
See Section 14.2.1, “General testing utilities” for details.

* ReflectionTestUtils现在支持为static字段设值和取值，包括常量。
ReflectionTestUtils now supports setting and getting static fields, including constants.

* 通过@ActiveProfiles声明的bean定义配置文件的原始顺序现在保留了，这是为了使用一些案例，比如Spring Boot的ConfigFileApplicationListener，它通过有效的名称来加载配置文件。
The original ordering of bean definition profiles declared via @ActiveProfiles is now retained in order to support use cases such as Spring Boot’s ConfigFileApplicationListener which loads configuration files based on the names of active profiles.

* @DirtiesContext现在支持新的模式BEFORE_METHOD, BEFORE_CLASS和BEFORE_EACH_TEST_METHOD用于在测试之前关闭ApplicationContext——例如，在大型测试套件中的一些劣质的测试毁坏了对ApplicationContext的原始配置。
@DirtiesContext supports new BEFORE_METHOD, BEFORE_CLASS, and BEFORE_EACH_TEST_METHOD modes for closing the ApplicationContext before a test — for example, if some rogue (i.e., yet to be determined) test within a large test suite has corrupted the original configuration for the ApplicationContext.

* @Commit这个新注解可以直接替代@Rollback(false)。
@Commit is a new annotation that may be used as a direct replacement for @Rollback(false).

* @Rollback现在可以用来配置类级别默认的回滚语义。 
@Rollback may now be used to configure class-level default rollback semantics.

  * 因此，@TransactionConfiguration现在过时了，并且会在后续版本中移除。
Consequently, @TransactionConfiguration is now deprecated and will be removed in a subsequent release.

* 通过statements这个新的属性@Sql现在支持内联SQL语句的执行。
@Sql now supports execution of inlined SQL statements via a new statements attribute.

* 用于在测试期间缓存应用上下文的ContextCache现在是公共的API，它有默认的实现，可以替代自定义的缓存需求。
The ContextCache that is used for caching ApplicationContexts between tests is now a public API with a default implementation that can be replaced for custom caching needs.

* DefaultTestContext, DefaultBootstrapContext和DefaultCacheAwareContextLoaderDelegate现在是support子包下的公共类，允许自定义扩展。
DefaultTestContext, DefaultBootstrapContext, and DefaultCacheAwareContextLoaderDelegate are now public classes in the support subpackage, allowing for custom extensions.

* TestContextBootstrappers现在负责创建TestContext。
TestContextBootstrappers are now responsible for building the TestContext.

* 在Spring MVC测试框架中，MvcResult的详细日志现在可以在DEBUG级别被打印，或者写出到自定义的OutputStream或Writer中。参考MockMvcResultHanlder中的新方法log(), print(OutputStream)和print(Writer)。
In the Spring MVC Test framework, MvcResult details can now be logged at DEBUG level or written to a custom OutputStream or Writer. See the new log(), print(OutputStream), and print(Writer) methods in MockMvcResultHandlers for details.

* JDBC XML的命名空间支持一个新的属性database-name，位于<jdbc:embedded-database>中，允许开发者为嵌入的数据库设置不同的名字——例如，通过SpEl表达式或者被当前 有效bean定义 配置文件 影响的属性文件占位符。
The JDBC XML namespace supports a new database-name attribute in <jdbc:embedded-database>, allowing developers to set unique names for embedded databases –- for example, via a SpEL expression or a property placeholder that is influenced by the current active bean definition profiles.

* 嵌入的数据库现在可以被自动赋予不同的名字，允许在同一测试套件不同的应用上下文中重复使用通用的测试数据库配置。参考18.8.6 为嵌入的数据库生成不同的名字。
Embedded databases can now be automatically assigned a unique name, allowing common test database configuration to be reused in different ApplicationContexts within a test suite.
See Section 19.8.6, “Generating unique names for embedded databases” for details.

* MockHttpServletRequest和MockHttpServletResponse现在通过getDateHeader和setDateHeader方法提供了更好的支持用于格式化日期头。
MockHttpServletRequest and MockHttpServletResponse now provide better support for date header formatting via the getDateHeader and setDateHeader methods.

# 6. Spring 4.3的新特性和增强功能
# 6. New Features and Enhancements in Spring Framework 4.3
Version 4.3 included a number of improvements, as described in the following sections:

Section 6.1, “Core Container Improvements”
Section 6.2, “Data Access Improvements”
Section 6.3, “Caching Improvements”
Section 6.4, “JMS Improvements”
Section 6.5, “Web Improvements”
Section 6.6, “WebSocket Messaging Improvements”
Section 6.7, “Testing Improvements”
Section 6.8, “Support for new library and server generations”

## 6.1 Core Container Improvements

* 核心容器提供了更丰富的元数据用于编程式评估。
* Java8的默认方法可以作为bean属性的getter/setter方法被检测。
* 如果目标bean仅仅定义了一个构造方法，就不必指定@Autowired注解了。
* @Configuration类支持构造方法注入。
* 任何用于指定@EventLIstener条件的SpEL表达式现在可以引用bean了（例如，@beanName.method()）。
* 组合注解现在可以重写元注解的数组属性。例如，@RequestMapping的String[] path可以使用组合注解的String path重写。
* @Scheduled和@Schedules可以作为元注解，用来创造组合注解并可重写其属性。
* @Scheduled支持任何作用域的bean。

Core container exceptions provide richer metadata to evaluate programmatically.
Java 8 default methods get detected as bean property getters/setters.
Lazy candidate beans are not being created in case of injecting a primary bean.
It is no longer necessary to specify the @Autowired annotation if the target bean only defines one constructor.
@Configuration classes support constructor injection.
Any SpEL expression used to specify the condition of an @EventListener can now refer to beans (e.g. @beanName.method()).
Composed annotations can now override array attributes in meta-annotations with a single element of the component type of the array. For example, the String[] path attribute of @RequestMapping can be overridden with String path in a composed annotation.
@PersistenceContext/@PersistenceUnit selects a primary EntityManagerFactory bean if declared as such.
@Scheduled and @Schedules may now be used as meta-annotations to create custom composed annotations with attribute overrides.
@Scheduled is properly supported on beans of any scope.


## 6.2 Data Access Improvements
jdbc:initialize-database和jdbc:embedded-database支持一个可配置的分隔符应用于任何脚本。
jdbc:initialize-database and jdbc:embedded-database support a configurable separator to be applied to each script.

## 6.3 Caching Improvements
spring 4.3 允许并发调用给定的key，从而使得值只被计算一次。这是一项可选功能，通过@Cacheable的新属性sync启用。这项功能也使Cache接口做了重大改变，增加了get(Object key, Callable<T> valueLoader)方法。

Spring 4.3 allows concurrent calls on a given key to be synchronized so that the value is only computed once. This is an opt-in feature that should be enabled via the new sync attribute on @Cacheable. This features introduces a breaking change in the Cache interface as a get(Object key, Callable<T> valueLoader) method has been added.

spring 4.3 也改进了以下缓存方面的内容：

Spring 4.3 also improves the caching abstraction as follows:

缓存相关的注解中的SpEL表达式现在可以引用bean了（比如，@beanName.method()）。
* ConcurrentMapCacheManager和ConcurrentMapCache现在可以通过新的属性storeByValue序列化缓存的entry。
* @Cacheable, @CacheEvict, @CachePut和@Caching现在可以作为元注解，用来创造组合注解并可重写其属性。
SpEL expressions in caches-related annotations can now refer to beans (i.e. @beanName.method()).
ConcurrentMapCacheManager and ConcurrentMapCache now support the serialization of cache entries via a new storeByValue attribute.
@Cacheable, @CacheEvict, @CachePut, and @Caching may now be used as meta-annotations to create custom composed annotations with attribute overrides.

## 6.4 JMS Improvements
* @SendTo现在可应用于类级别上，以便共享共同的目标。
* @JmsListener和@JmsListeners现在可作为元注解，用来创造组合注解并可重写其属性。

@SendTo can now be specified at the class level to share a common reply 
destination.
@JmsListener and @JmsListeners may now be used as meta-annotations to create custom composed annotations with attribute overrides.

## 6.5 Web Improvements
* 内置了对Http头和Http选项的支持。
* 新的组合注解@GetMapping, @PostMapping, @PutMapping, @DeleteMapping和@PatchMapping，它们来源于@RequestMapping。 
  * 参考@RequestMapping的变种。
* 新的组合注解@RequestScope, @SessionScope和@ApplicationScope用于web作用域。 
  * 参考Request scope, Session scope和Application scope。
* 新的注解@RestControllerAdvice，它是@ControllerAdvice和@ResponseBody的组合体。
* @ResponseStatus现在可用于类级别并可以被所有方法继承。
* 新的@SessionAttribute注解用于访问session的属性。
* 新的@RequestAttribute注解用于访问request的属性。
* @ModelAttribute可以设置其属性binding=false阻止数据绑定。
* 错误和自定义的异常可一致地暴露给MVC的异常处理器。
* Http消息转换器中一致地处理字符集，默认地使用UTF-8处理多部分文本内容。
* 使用已配置的ContentNegotiationManager处理媒体类型等静态资源。

* RestTemplate和AsyncRestTemplate可通过DefaultUriTemplateHandler支持严格的URI编码。
AsyncRestTemplate支持请求拦截。

Built-in support for HTTP HEAD and HTTP OPTIONS.
New @GetMapping, @PostMapping, @PutMapping, @DeleteMapping, and @PatchMapping composed annotations for @RequestMapping.

See Composed @RequestMapping Variants for details.
New @RequestScope, @SessionScope, and @ApplicationScope composed annotations for web scopes.

See Request scope, Session scope, and Application scope for details.
New @RestControllerAdvice annotation with combined @ControllerAdvice with @ResponseBody semantics.
@ResponseStatus is now supported at the class level and inherited by all methods.
New @SessionAttribute annotation for access to session attributes (see example).
New @RequestAttribute annotation for access to request attributes (see example).
@ModelAttribute allows preventing data binding via binding=false attribute (see reference).
@PathVariable may be declared as optional (for use on @ModelAttribute methods).
Consistent exposure of Errors and custom Throwables to MVC exception handlers.
Consistent charset handling in HTTP message converters, including a UTF-8 default for multipart text content.
Static resource handling uses the configured ContentNegotiationManager for media type determination.
RestTemplate and AsyncRestTemplate support strict URI variable encoding via DefaultUriTemplateHandler.
AsyncRestTemplate supports request interception.

## 6.6 WebSocket Messaging Improvements
@SendTo和@SendToUser现在可应用于类级别上，以便共享共同的目标。
@SendTo and @SendToUser can now be specified at class-level to share a common destination.
## 6.7 Testing Improvements
* spring测试上下文中的JUnit现在需要 4.12 及其更高版本。
* SpringJUnit4ClassRunner的新别名SpringRunner。
* 测试相关的注解现在可用于接口上，从而可以使用Java 8 中接口的默认方法。
* 空声明的@ContextConfiguration现在可以完全省略了，如果默认的XML文件、Groovy脚本或@Configuration类被检测到。
* @Transactional测试方法不再必需是public了（例如，在TestNG和JUnit 5 中）。
* @BeforeTransaction和AfterTransaction方法不再必需是public了，并且现在也可能用在Java 8 接口的默认方法上。
* spring测试上下文中的ApplicationContext缓存现在是有界的，默认最大值为32，并按最近最少原则回收。其最大值可以通过JVM的系统属性或spring的属性spring.test.context.cache.maxSize进行设置。
* 用于自定义测试ApplicationContext的新API ContextCustomizer在bean定义之后且上下文刷新之前被加载到上下文中。Customizer可以通过第三方注册，但需要实现自定义的ContextLoader。
* @Sql和@SqlGroup现在可作为元注解，用来创造组合注解并可重写其属性。
* ReflectionTestUtils现在会自动解除代理当set或get一个字段时。
* 服务器端的springmvc测试支持响应头带有多个值。
* 服务器端的springmvc测试解析表单数据请求内容并填充请求参数。
* 服务器端的springmvc测试支持对已调用的处理器方法模拟断言。
* 客户端的REST测试允许指明希望发送多少次请求并决定是否请求的顺序可被忽略。
* 客户端的REST测试支持在请求体中添加表单数据。

The JUnit support in the Spring TestContext Framework now requires JUnit 4.12 or higher.
New SpringRunner alias for the SpringJUnit4ClassRunner.
Test related annotations may now be declared on interfaces — for example, for use with test interfaces that make use of Java 8 based interface default methods.
An empty declaration of @ContextConfiguration can now be completely omitted if default XML files, Groovy scripts, or @Configuration classes are detected.
@Transactional test methods are no longer required to be public (e.g., in TestNG and JUnit 5).
@BeforeTransaction and @AfterTransaction methods are no longer required to be public and may now be declared on Java 8 based interface default methods.
The ApplicationContext cache in the Spring TestContext Framework is now bounded with a default maximum size of 32 and a least recently used eviction policy. The maximum size can be configured by setting a JVM system property or Spring property called spring.test.context.cache.maxSize.
New ContextCustomizer API for customizing a test ApplicationContext after bean definitions have been loaded into the context but before the context has been refreshed. Customizers can be registered globally by third parties, foregoing the need to implement a custom ContextLoader.
@Sql and @SqlGroup may now be used as meta-annotations to create custom composed annotations with attribute overrides.
ReflectionTestUtils now automatically unwraps proxies when setting or getting a field.
Server-side Spring MVC Test supports expectations on response headers with multiple values.
Server-side Spring MVC Test parses form data request content and populates request parameters.
Server-side Spring MVC Test supports mock-like assertions for invoked handler methods.
Client-side REST test support allows indicating how many times a request is expected and whether the order of declaration for expectations should be ignored (see Section 15.6.3, “Client-Side REST Tests”).
Client-side REST Test supports expectations for form data in the request body.

## 6.8 Support for new library and server generations
6.8 支持新库和服务器
* Hibernate ORM 5.2（仍然能很好地支持4.2/4.3和5.0/5.1，但是3.6已经过时了）
* Jackson 2.8（至少需要2.6以上版本）
* OkHttp 3.x（同时仍然支持OkHttp 2.x）
* Netty 4.1
* Undertow 1.4
* Tomcat 8.5.2 及 9.0 M6

Hibernate ORM 5.2 (still supporting 4.2/4.3 and 5.0/5.1 as well, with 3.6 deprecated now)
Hibernate Validator 5.3 (minimum remains at 4.3)
Jackson 2.8 (minimum raised to Jackson 2.6+ as of Spring 4.3)
OkHttp 3.x (still supporting OkHttp 2.x side by side)
Tomcat 8.5 as well as 9.0 milestones
Netty 4.1
Undertow 1.4
WildFly 10.1

另外，spring 4.3的spring-core.jar中集成了更新的ASM 5.1和Objenesis 2.4。
Furthermore, Spring Framework 4.3 embeds the updated ASM 5.1, CGLIB 3.2.4, and Objenesis 2.4 in spring-core.jar.

# 参考：
https://blog.csdn.net/tangtong1/article/details/51326887