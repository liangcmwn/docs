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

The existing support for resource handling based on the ResourceHttpRequestHandler has been expanded with new abstractions ResourceResolver, ResourceTransformer, and ResourceUrlProvider. A number of built-in implementations provide support for versioned resource URLs (for effective HTTP caching), locating gzipped resources, generating an HTML 5 AppCache manifests, and more. See Section 16.16.7, “资源服务”.
JDK 1.8’s java.util.Optional is now supported for @RequestParam, @RequestHeader, and @MatrixVariable controller method arguments.
ListenableFuture is supported as a return value alternative to DeferredResult where an underlying service (or perhaps a call to AsyncRestTemplate) already returns ListenableFuture.
@ModelAttribute methods are now invoked in an order that respects inter-dependencies. See SPR-6299.
Jackson’s @JsonView is supported directly on @ResponseBody and ResponseEntity controller methods for serializing different amounts of detail for the same POJO (e.g. summary vs. detail page). This is also supported with View-based rendering by adding the serialization view type as a model attribute under a special key. See the section called “支持 Jackson 序列化视图” for details.
JSONP is now supported with Jackson. See the section called “支持 Jackson JSONP”.
A new lifecycle option is available for intercepting @ResponseBody and ResponseEntity methods just after the controller method returns and before the response is written. To take advantage declare an @ControllerAdvice bean that implements ResponseBodyAdvice. The built-in support for @JsonView and JSONP take advantage of this. See Section 16.4.1, “使用 HandlerInterceptor 拦截请求”.
There are three new HttpMessageConverter options:

Gson — lighter footprint than Jackson; has already been in use in Spring Android.
Google Protocol Buffers — efficient and effective as an inter-service communication data protocol within an enterprise but can also be exposed as JSON and XML for browsers.
Jackson based XML serialization is now supported through the jackson-dataformat-xml extension. When using @EnableWebMvc or <mvc:annotation-driven/>, this is used by default instead of JAXB2 if jackson-dataformat-xml is in the classpath.
Views such as JSPs can now build links to controllers by referring to controller mappings by name. A default name is assigned to every @RequestMapping. For example FooController with method handleFoo is named "FC#handleFoo". The naming strategy is pluggable. It is also possible to name an @RequestMapping explicitly through its name attribute. A new mvcUrl function in the Spring JSP tag library makes this easy to use in JSP pages. See Section 16.7.2, “Building URIs to Controllers and methods from views”.
ResponseEntity provides a builder-style API to guide controller methods towards the preparation of server-side responses, e.g. ResponseEntity.ok().
RequestEntity is a new type that provides a builder-style API to guide client-side REST code towards the preparation of HTTP requests.
MVC Java config and XML namespace:

View resolvers can now be configured including support for content negotiation, see Section 16.16.6, “视图解析器”.
View controllers now have built-in support for redirects and for setting the response status. An application can use this to configure redirect URLs, render 404 responses with a view, send "no content" responses, etc. Some use cases are listed here.
Path matching customizations are frequently used and now built-in. See Section 16.16.9, “Path Matching”.
Groovy markup template support (based on Groovy 2.3). See the GroovyMarkupConfigurer and respecitve ViewResolver and ‘View’ implementations.
4.4 WebSocket STOMP消息改进
SockJS (Java) client-side support. See SockJsClient and classes in same package.
New application context events SessionSubscribeEvent and SessionUnubscribeEvent published when STOMP clients subscribe and unsubscribe.
New "websocket" scope. See Section 20.4.13, “WebSocket Scope”.
@SendToUser can target only a single session and does not require an authenticated user.
@MessageMapping methods can use dot "." instead of slash "/" as path separator. See SPR-11660.
STOMP/WebSocket monitoring info collected and logged. See Section 20.4.15, “Runtime Monitoring”.
Significantly optimized and improved logging that should remain very readable and compact even at DEBUG level.
Optimized message creation including support for temporary message mutability and avoiding automatic message id and timestamp creation. See Javadoc of MessageHeaderAccessor.
STOMP/WebSocket connections that have not activity 60 seconds after the WebSocket session is established. See SPR-11884.
4.5 测试改进
Groovy scripts can now be used to configure the ApplicationContext loaded for integration tests in the TestContext framework.

See the section called “使用Groovy脚本配置上下文” for details.
Test-managed transactions can now be programmatically started and ended within transactional test methods via the new TestTransaction API.

See the section called “Programmatic transaction management” for details.
SQL script execution can now be configured declaratively via the new @Sql and @SqlConfig annotations on a per-class or per-method basis.

See the section called “Executing SQL scripts” for details.
Test property sources which automatically override system and application property sources can be configured via the new @TestPropertySource annotation.

See the section called “测试属性源上下文配置” for details.
Default TestExecutionListeners can now be automatically discovered.

See the section called “默认 TestExecutionListeners的自动发现” for details.
Custom TestExecutionListeners can now be automatically merged with the default listeners.

See the section called “合并 TestExecutionListeners” for details.
The documentation for transactional testing support in the TestContext framework has been improved with more thorough explanations and additional examples.

See the section called “Transaction management” for details.
Various improvements to MockServletContext, MockHttpServletRequest, and other Servlet API mocks.
AssertThrows has been refactored to support Throwable instead of Exception.
In Spring MVC Test, JSON responses can be asserted with JSON Assert as an extra option to using JSONPath much like it has been possible to do for XML with XMLUnit.
MockMvcBuilder recipes can now be created with the help of MockMvcConfigurer. This was added to make it easy to apply Spring Security setup but can be used to encapsulate common setup for any 3rd party framework or within a project.
MockRestServiceServer now supports the AsyncRestTemplate for client-side testing.