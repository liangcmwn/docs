# Part I. Spring 框架概述

Part I. Overview of Spring Framework

Spring框架是一个轻量级解决方案,也是潜在的开发企业级应用的一站式解决方案.而且Spring也是模块化的,它允许你只用需要的部分,不需要的部分可以舍弃.你可以使用IoC容器,上层使用其他web框架,但你依然可以仅使用 Hibernate integration code或者 JDBC abstraction layer. Spring框架支持声明式事务管理,通过RMI或者web服务远程访问你的逻辑代码,及多选的持久化数据方式.它提供一个完整特性的 MVC framework, 且使你能在不影响其它代码的前提下把 AOP 整合进你的应用.

The Spring Framework is a lightweight solution and a potential one-stop-shop for building your enterprise-ready applications. However, Spring is modular, allowing you to use only those parts that you need, without having to bring in the rest. You can use the IoC container, with any web framework on top, but you can also use only the Hibernate integration code or the JDBC abstraction layer. The Spring Framework supports declarative transaction management, remote access to your logic through RMI or web services, and various options for persisting your data. It offers a full-featured MVC framework, and enables you to integrate AOP transparently into your software.

Spring被设计为非侵入式的,意思是你的域逻辑代码通常和框架本身是没依赖的,在你的整合层（比如数据访问层）,会存在一些对数据访问技术和Spring库文件的依赖,但它可以轻易地从你的其它代码中独立出来.

Spring is designed to be non-intrusive, meaning that your domain logic code generally has no dependencies on the framework itself. In your integration layer (such as the data access layer), some dependencies on the data access technology and the Spring libraries will exist. However, it should be easy to isolate these dependencies from the rest of your code base.

这篇文档是作为Spring框架特性的参考向导.如果对该文档有任何要求,意见或者问题,请发邮件到到 user mailing list. 如果对框架本身有疑问请前往StackOverflow提问 (见 https://spring.io/questions).

This document is a reference guide to Spring Framework features. If you have any requests, comments, or questions on this document, please post them on the user mailing list. Questions on the Framework itself should be asked on StackOverflow (see https://spring.io/questions).

## 1. 开启Spring之旅

1. Getting Started with Spring

这篇参考向导提供了关于Spring框架的详细信息.它提供了所有特性的全面介绍,以及一些Spring包含的基础概念的背景 (liru "Dependency Injection").

This reference guide provides detailed information about the Spring Framework. It provides comprehensive documentation for all features, as well as some background about the underlying concepts (such as "Dependency Injection") that Spring has embraced.

If you are just getting started with Spring, you may want to begin using the Spring Framework by creating a Spring Boot based application. Spring Boot provides a quick (and opinionated) way to create a production-ready Spring based application. It is based on the Spring Framework, favors convention over configuration, and is designed to get you up and running as quickly as possible.

You can use `start.spring.io` to generate a basic project or follow one of the "Getting Started" guides like the Getting Started Building a RESTful Web Service one. As well as being easier to digest, these guides are very task focused, and most of them are based on Spring Boot. They also cover other projects from the Spring portfolio that you might want to consider when solving a particular problem.

## 2. Spring框架概述
2. Introduction to the Spring Framework

Spring框架是一个基于Java的框架平台，并且为实现Java应用程序提供了全面的基础架构支持。 你可以专注于你的应用，让Spring来帮助你处理基础架构的问题。

The Spring Framework is a Java platform that provides comprehensive infrastructure support for developing Java applications. Spring handles the infrastructure so you can focus on your application.

Spring使你能够用"plain old Java objects" (POJOs，简单普通的Java对象)来构建应用，并且将企业服务 用POJO来实现。你可以在Java SE编程模型、全部或者部分的Java EE编程模型中应用它。

Spring enables you to build applications from "plain old Java objects" (POJOs) and to apply enterprise services non-invasively to POJOs. This capability applies to the Java SE programming model and to full and partial Java EE.

作为一个应用开发者，以下例子告诉你如何使用Spring平台的优点：

Examples of how you, as an application developer, can benefit from the Spring platform:

* 创建一个Java方法，无需关注事务API就执行数据库事务。 

Make a Java method execute in a database transaction without having to deal with transaction APIs.
* 创建一个本地Java方法，无需关注remote API从而进行远程操作。

Make a local Java method an HTTP endpoint without having to deal with the Servlet API.
* 创建一个本地Java方法，无需关注JMX API就能实现管理操作。 

Make a local Java method a message handler without having to deal with the JMS API.
* 创建一个本地Java方法，无需关注JMS API就能实现消息处理。

Make a local Java method a management operation without having to deal with the JMX API.

## 2.1 依赖注入和控制反转
## 2.1 Dependency Injection and Inversion of Control

Java应用 — 运行着小到各种受限的小程序，大到n层结构的服务器端企业级应用 — 包含着相互协作的对象，从而创建正确的应用。 因此，程序里的对象彼此之间相互依赖。
A Java application — a loose term that runs the gamut from constrained, embedded applications to n-tier, server-side enterprise applications — typically consists of objects that collaborate to form the application proper. Thus the objects in an application have dependencies on each other.

尽管Java平台提供的很多功能性的应用程序，但是他缺少将这些基础组件组织成一个整体的方法，最终把这些整合工作交给了架构师或是开发者。 你可以使用设计模式，例如工厂，抽象工厂，建造者，装饰和服务定位来将这些不同的类和示例对象组合起来，从而构建一个应用。 但是，这些模式仅仅只是：一个被给予名字的最佳实践，说明了该模式做什么，怎样应用，解决了什么问题等等。 模式是形式化的，你必须在你的应用中去实现它。

Although the Java platform provides a wealth of application development functionality, it lacks the means to organize the basic building blocks into a coherent whole, leaving that task to architects and developers. Although you can use design patterns such as Factory, Abstract Factory, Builder, Decorator, and Service Locator to compose the various classes and object instances that make up an application, these patterns are simply that: best practices given a name, with a description of what the pattern does, where to apply it, the problems it addresses, and so forth. Patterns are formalized best practices that you must implement yourself in your application.

Spring中的控制反转 (IoC)部分解决了这个问题，通过提供一种有效的方式将各个分开的组件组合成一个完全可供使用的应用。 Spring框架用函数化的形式实现了形式化的设计模式，这样你就可以在你的应用中继承它们。 很多组织个机构正在使用Spring框架来设计健壮的，便于维护的应用。

The Spring Framework Inversion of Control (IoC) component addresses this concern by providing a formalized means of composing disparate components into a fully working application ready for use. The Spring Framework codifies formalized design patterns as first-class objects that you can integrate into your own application(s). Numerous organizations and institutions use the Spring Framework in this manner to engineer robust, maintainable applications.

#### Background
#### 背景

"那么问题来了，它们在反转着哪些方面" Martin Fowler于2004年在他的站点上提出了这个有关控制反转(IoC)的问题。 Fowler建议重新说明本质以便让它变得更容易理解并且让人联想起依赖注入。
"The question is, what aspect of control are [they] inverting?" Martin Fowler posed this question about Inversion of Control (IoC) on his site in 2004. Fowler suggested renaming the principle to make it more self-explanatory and came up with Dependency Injection.


如果想要了解IoC和DI，可以在 http://martinfowler.com/articles/injection.html参考Fowler的文章。

## 2.2 模块
## 2.2 Framework Modules

Spring框架的各个特性被组织成20个模块。这些模块被分组成Core Container（核心容器）, Data Access/Integration（数据访问/集成）, Web（网络端）, AOP (Aspect Oriented Programming，切面编程), Instrumentation, Messaging（消息）,和Test（测试）， 以下图片显示的就是Spring的各个模块：

The Spring Framework consists of features organized into about 20 modules. These modules are grouped into Core Container, Data Access/Integration, Web, AOP (Aspect Oriented Programming), Instrumentation, Messaging, and Test, as shown in the following diagram.

Figure 2.1. Spring框架总览
![Overview of the Spring Framework](https://docs.spring.io/spring/docs/4.3.20.BUILD-SNAPSHOT/spring-framework-reference/htmlsingle/images/spring-overview.png)

接下来的章节列出了每个特性的可用模块，同时也说明了组件名称和涵盖的主题。 组件名称和在 Dependency Management tools 中使用的 artifact IDs 相互关联。

The following sections list the available modules for each feature along with their artifact names and the topics they cover. Artifact names correlate to artifact IDs used in Dependency Management tools.

### 2.2.1 核心容器
2.2.1 Core Container
核心容器 包含了 spring-core, spring-beans, spring-context, and spring-expression (Spring表达式语言) 四个模块。

The Core Container consists of the spring-core, spring-beans, spring-context, spring-context-support, and spring-expression (Spring Expression Language) modules.

spring-core和spring-beans模块提供了整个框架最基础的部分, 包括了IoC（控制反转）和Dependency Injection（依赖注入）特性。 BeanFactory实现了工厂模式。 它让你不必再去计划实现单例模式，并且能让你将各个Bean配置和依赖声明从你的实际的程序逻辑中分离开来。

The spring-core and spring-beans modules provide the fundamental parts of the framework, including the IoC and Dependency Injection features. The BeanFactory is a sophisticated implementation of the factory pattern. It removes the need for programmatic singletons and allows you to decouple the configuration and specification of dependencies from your actual program logic.

Context (spring-context)模块建立在Core and Beans模块提供的基础之上: 它提供了框架式访问对象的方式，类似于JNDI注册。 Context模块从Beans模块中继承了它的特性并且为国际化(例如使用资源包), 事件传播, 资源加载和创建上下文，例如Servlet容器。 Context模块也支持Java EE特性，例如EJB, JMX,和基础远程. ApplicationContext接口是Context模块的焦点所在.

The Context (spring-context) module builds on the solid base provided by the Core and Beans modules: it is a means to access objects in a framework-style manner that is similar to a JNDI registry. The Context module inherits its features from the Beans module and adds support for internationalization (using, for example, resource bundles), event propagation, resource loading, and the transparent creation of contexts by, for example, a Servlet container. The Context module also supports Java EE features such as EJB, JMX, and basic remoting. The ApplicationContext interface is the focal point of the Context module. spring-context-support provides support for integrating common third-party libraries into a Spring application context for caching (EhCache, Guava, JCache), mailing (JavaMail), scheduling (CommonJ, Quartz) and template engines (FreeMarker, JasperReports, Velocity).

spring-expression模块提供了一种强大的用于在运行时查询操作对象的表达式语言。他是对于在JSP2.1规范中所声明的unified expression语言(统一表达式语言)的扩展。 这种语言支持对属性值, 属性参数, 方法调用, 获得数组内容, 收集器和索引, 算术和逻辑运算, 变量命名和从Spring IoC容器中根据名称获得对象。它也为列表映射和选择提供了支持，就像常见的列表操作一样。

The spring-expression module provides a powerful Expression Language for querying and manipulating an object graph at runtime. It is an extension of the unified expression language (unified EL) as specified in the JSP 2.1 specification. The language supports setting and getting property values, property assignment, method invocation, accessing the content of arrays, collections and indexers, logical and arithmetic operators, named variables, and retrieval of objects by name from Spring’s IoC container. It also supports list projection and selection as well as common list aggregations.

### 2.2.2 AOP和Instrumentation
2.2.2 AOP and Instrumentation
* spring-aop模块提供了AOP(联盟编程) 面向切面的编程实现，允许你定义，例如， 将拦截器方法和切入点的代码完全分离开来。 利用源码中的元数据, 你可以将行为信息加入到你的代码中, 一定程度上类似于.NET属性。
The spring-aop module provides an AOP Alliance-compliant aspect-oriented programming implementation allowing you to define, for example, method interceptors and pointcuts to cleanly decouple code that implements functionality that should be separated. Using source-level metadata functionality, you can also incorporate behavioral information into your code, in a manner similar to that of .NET attributes.

* 分离的spring-aspects模块集成了AspectJ。
The separate spring-aspects module provides integration with AspectJ.

* spring-instrument模块提供了类instrumentation支持和使用在某些应用服务器上的类加载器实现。
The spring-instrument module provides class instrumentation support and classloader implementations to be used in certain application servers. The spring-instrument-tomcat module contains Spring’s instrumentation agent for Tomcat.

### 2.2.3 Messaging
Spring 4 包含的spring-messaging模块是从Spring集成项目的关键抽象中提取出来的，这些项目包括Message、MessageChannel、MessageHandler和其它服务于消息处理的项目。这个模块也包含一系列的注解用于映射消息到方法，这类似于Spring MVC基于编码模型的注解。

Spring Framework 4 includes a spring-messaging module with key abstractions from the Spring Integration project such as Message, MessageChannel, MessageHandler, and others to serve as a foundation for messaging-based applications. The module also includes a set of annotations for mapping messages to methods, similar to the Spring MVC annotation based programming model.

2.2.4 数据访问与集成
### 2.2.4 Data Access/Integration
数据访问与集成层包含JDBC、ORM、OXM、JMS和事务模块。 
（译者注：JDBC=Java Data Base Connectivity，ORM=Object Relational Mapping，OXM=Object XML Mapping，JMS=Java Message Service）
The Data Access/Integration layer consists of the JDBC, ORM, OXM, JMS, and Transaction modules.

* spring-jdbc模块提供了JDBC抽象层，它消除了冗长的JDBC编码和对数据库供应商特定错误代码的解析。 
The spring-jdbc module provides a JDBC-abstraction layer that removes the need to do tedious JDBC coding and parsing of database-vendor specific error codes.
* spring-tx模块支持编程式事务和声明式事务，可用于实现了特定接口的类和所有的POJO对象。 
（译者注：编程式事务需要自己写beginTransaction()、commit()、rollback()等事务管理方法，声明式事务是通过注解或配置由spring自动处理，编程式事务粒度更细）
The spring-tx module supports programmatic and declarative transaction management for classes that implement special interfaces and for all your POJOs (Plain Old Java Objects).
* spring-orm模块提供了对流行的对象关系映射API的集成，包括JPA、JDO和Hibernate等。通过此模块可以让这些ORM框架和spring的其它功能整合，比如前面提及的事务管理。
The spring-orm module provides integration layers for popular object-relational mapping APIs, including JPA, JDO, and Hibernate. Using the spring-orm module you can use all of these O/R-mapping frameworks in combination with all of the other features Spring offers, such as the simple declarative transaction management feature mentioned previously.
* spring-oxm模块提供了对OXM实现的支持，比如JAXB、Castor、XML Beans、JiBX、XStream等。
The spring-oxm module provides an abstraction layer that supports Object/XML mapping implementations such as JAXB, Castor, XMLBeans, JiBX and XStream.
* spring-jms模块（Java消息服务）包含生成和消费消息的功能。从Spring 4.1开始，集成了spring-messaging模块。  
The spring-jms module (Java Messaging Service) contains features for producing and consuming messages. Since Spring Framework 4.1, it provides integration with the spring-messaging module.

### 2.2.5 Web
Web层包括spring-web、spring-webmvc、spring-websocket、spring-webmvc-portlet等模块。
The Web layer consists of the spring-web, spring-webmvc, spring-websocket, and spring-webmvc-portlet modules.

spring-web模块提供面向web的基本功能和面向web的应用上下文，比如多部分（multipart）文件上传功能、使用Servlet监听器初始化IoC容器等。它还包括HTTP客户端以及Spring远程调用中与web相关的部分。 
The spring-web module provides basic web-oriented integration features such as multipart file upload functionality and the initialization of the IoC container using Servlet listeners and a web-oriented application context. It also contains an HTTP client and the web-related parts of Spring’s remoting support.

spring-webmvc模块（即Web-Servlet模块）为web应用提供了模型视图控制（MVC）和REST Web服务的实现。Spring的MVC框架可以使领域模型代码和web表单完全地分离，且可以与Spring框架的其它所有功能进行集成。

The spring-webmvc module (also known as the Web-Servlet module) contains Spring’s model-view-controller (MVC) and REST Web Services implementation for web applications. Spring’s MVC framework provides a clean separation between domain model code and web forms and integrates with all of the other features of the Spring Framework.

spring-webmvc-portlet模块（即Web-Portlet模块）提供了用于Portlet环境的MVC实现，并反映了spring-webmvc模块的功能。
The spring-webmvc-portlet module (also known as the Web-Portlet module) provides the MVC implementation to be used in a Portlet environment and mirrors the functionality of the Servlet-based spring-webmvc module.

### 2.2.6 Test
spring-test模块通过JUnit或者TestNG来对Spring的模块进行单元测试和集成测试。它提供一致的Spring 的ApplicationContexts 和context的缓存。它还提供mock对象让你测试你的代码。  
The spring-test module supports the unit testing and integration testing of Spring components with JUnit or TestNG. It provides consistent loading of Spring ApplicationContexts and caching of those contexts. It also provides mock objects that you can use to test your code in isolation.

## 2.3 使用场景
## 2.3 Usage scenarios

前面提及的构建模块使得Spring在很多场景成为一种合理的选择，不管是资源受限的嵌入式应用还是使用了事务管理和web集成框架的成熟的企业级应用。
The building blocks described previously make Spring a logical choice in many scenarios, from embedded applications that run on resource-constrained devices to full-fledged enterprise applications that use Spring’s transaction management functionality and web framework integration.


图2.2. 典型的成熟的Spring web应用程序 
Figure 2.2. Typical full-fledged Spring web application
![](https://docs.spring.io/spring/docs/4.3.20.BUILD-SNAPSHOT/spring-framework-reference/htmlsingle/images/overview-full.png)
overview full

Spring的声明式事务管理可以使web应用完成事务化，就像使用EJB容器管理的事务。所有客制的业务逻辑都可以使用简单的POJO实现，并用Spring的IoC容器进行管理。另外，还包括发邮件和验证功能，其中验证功能是从web层分离的，由你决定何处执行验证。Spring的ORM可以集成JPA、Hibernate和JDO等，比如，使用Hibernate时，可以继续使用已存在的映射文件和标准的Hibernate的SessionFactory配置。表单控制器无缝地把web层和领域模型集成在一起，移除了ActionForms和其它把HTTP参数转换成领域模型的类。
Spring’s declarative transaction management features make the web application fully transactional, just as it would be if you used EJB container-managed transactions. All your custom business logic can be implemented with simple POJOs and managed by Spring’s IoC container. Additional services include support for sending email and validation that is independent of the web layer, which lets you choose where to execute validation rules. Spring’s ORM support is integrated with JPA, Hibernate and JDO; for example, when using Hibernate, you can continue to use your existing mapping files and standard Hibernate SessionFactory configuration. Form controllers seamlessly integrate the web-layer with the domain model, removing the need for ActionForms or other classes that transform HTTP parameters to values for your domain model.

图2.3. 使用第三方web框架的Spring中间件 
Figure 2.3. Spring middle-tier using a third-party web framework
![](https://docs.spring.io/spring/docs/4.3.20.BUILD-SNAPSHOT/spring-framework-reference/htmlsingle/images/overview-thirdparty-web.png)
overview thirdparty web

一些场景可能不允许你完全切换到另一个框架。然而，Spring框架不强制你使用它所有的东西，它不是非此即彼（all-or-nothing）的解决方案。前端使用Struts、Tapestry、JSF或别的UI框架可以和Spring中间件集成，从而使用Spring的事务管理功能。仅仅只需要使用ApplicationContext连接业务逻辑，并使用WebApplicationContext集成web层即可。
Sometimes circumstances do not allow you to completely switch to a different framework. The Spring Framework does not force you to use everything within it; it is not an all-or-nothing solution. Existing front-ends built with Struts, Tapestry, JSF or other UI frameworks can be integrated with a Spring-based middle-tier, which allows you to use Spring transaction features. You simply need to wire up your business logic using an ApplicationContext and use a WebApplicationContext to integrate your web layer.

图2.4. 远程调用使用场景 
Figure 2.4. Remoting usage scenario
![](https://docs.spring.io/spring/docs/4.3.20.BUILD-SNAPSHOT/spring-framework-reference/htmlsingle/images/overview-remoting.png)
overview remoting

当需要通过web服务访问现有代码时，可以使用Spring的Hessian-，Burlap-，Rmi-或者JaxRpcProxyFactory类，远程访问现有的应用并非难事。

When you need to access existing code through web services, you can use Spring’s Hessian-, Burlap-, Rmi- or JaxRpcProxyFactory classes. Enabling remote access to existing applications is not difficult.

图2.5. EJB-包装现有的POJO 
Figure 2.5. EJBs - Wrapping existing POJOs
![](https://docs.spring.io/spring/docs/4.3.20.BUILD-SNAPSHOT/spring-framework-reference/htmlsingle/images/overview-ejb.png)
overview ejb

Spring框架也为EJB提供了访问抽象层，可以重新使用现有的POJO并把它们包装到无状态的会话bean中，以使其用于可扩展的安全的web应用中。
The Spring Framework also provides an access and abstraction layer for Enterprise JavaBeans, enabling you to reuse your existing POJOs and wrap them in stateless session beans for use in scalable, fail-safe web applications that might need declarative security.

### 2.3.1 依赖管理和命名约定
### 2.3.1 Dependency Management and Naming Conventions
依赖管理和依赖注入是两码事。为了让应用程序拥有这些Spring的非常棒的功能（如依赖注入），需要导入所需的全部jar包，并在运行时放在classpath下，有可能的话编译期也应该放在classpath下。这些依赖并不是被注入的虚拟组件，而是文件系统上典型的物理资源。依赖管理的处理过程涉及到定位这些资源、存储并添加它们到classpath下。依赖可能是直接的（比如运行时依赖于Spring），也可能是间接的（比如依赖于commons-dbcp，commons-dbcp又依赖于commons-pool）。间接的依赖又被称作“传递”，它们是最难识别和管理的。

Dependency management and dependency injection are different things. To get those nice features of Spring into your application (like dependency injection) you need to assemble all the libraries needed (jar files) and get them onto your classpath at runtime, and possibly at compile time. These dependencies are not virtual components that are injected, but physical resources in a file system (typically). The process of dependency management involves locating those resources, storing them and adding them to classpaths. Dependencies can be direct (e.g. my application depends on Spring at runtime), or indirect (e.g. my application depends on commons-dbcp which depends on commons-pool). The indirect dependencies are also known as "transitive" and it is those dependencies that are hardest to identify and manage.

如果准备使用Spring，则需要拷贝一份所需模块的Spring的jar包。为了便于使用，Spring被打包成一系列的模块以尽可能地减少依赖，比如，如果不是在写一个web应用，那就没必要引入spring-web模块。这篇文档中涉及到的Spring模块，我们使用spring-*或spring-*.jar的命名约定，其中，*代表模块的短名字（比如，spring-core、spring-webmvc、spring-jms等等）。实际使用的jar包正常情况下都是带有版本号的（比如，spring-core-4.3.0.RELEASE.jar）。

If you are going to use Spring you need to get a copy of the jar libraries that comprise the pieces of Spring that you need. To make this easier Spring is packaged as a set of modules that separate the dependencies as much as possible, so for example if you don’t want to write a web application you don’t need the spring-web modules. To refer to Spring library modules in this guide we use a shorthand naming convention spring-* or spring-*.jar, where * represents the short name for the module (e.g. spring-core, spring-webmvc, spring-jms, etc.). The actual jar file name that you use is normally the module name concatenated with the version number (e.g. spring-core-4.3.20.BUILD-SNAPSHOT.jar).

每个版本的Spring都会在以下地方发布artifact：
Each release of the Spring Framework will publish artifacts to the following places:
 * Maven中央仓库，默认的Maven查询仓库，并且不需要特殊的配置就可以使用。许多Spring依赖的公共库也可以从Maven中央仓库获得，并且大部分的Spring社区也使用Maven作为依赖管理工具，所以很方便。Maven中的jar包命名格式为spring-*-<version>.jar，其groupId是org.springframework。
Maven Central, which is the default repository that Maven queries, and does not require any special configuration to use. Many of the common libraries that Spring depends on also are available from Maven Central and a large section of the Spring community uses Maven for dependency management, so this is convenient for them. The names of the jars here are in the form spring-*-<version>.jar and the Maven groupId is org.springframework.
* 特别为托管Spring的公共的Maven仓库。除了最终的GA版本，这个仓库也托管了开发的快照版本和里程碑版本。jar包和Maven中央仓库中的命名一致，所以这也是一个获取Spring的开发版本的有用地方，可以和Maven中央仓库中部署的其它库一起使用。这个仓库也包含了捆绑了所有Spring jar包的发行版的zip文件，以便于下载。
In a public Maven repository hosted specifically for Spring. In addition to the final GA releases, this repository also hosts development snapshots and milestones. The jar file names are in the same form as Maven Central, so this is a useful place to get development versions of Spring to use with other libraries deployed in Maven Central. This repository also contains a bundle distribution zip file that contains all Spring jars bundled together for easy download.

因此，首先要做的事就是决定如何管理依赖关系，我们一般推荐使用自动管理的系统，比如Maven、Gradle或Ivy，当然你也可以手动下载所有的jar包。
So the first thing you need to decide is how to manage your dependencies: we generally recommend the use of an automated system like Maven, Gradle or Ivy, but you can also do it manually by downloading all the jars yourself.

下面列出了Spring的artifact，每个模块更完整的描述，参考 2.2 模块 章节。
Below you will find the list of Spring artifacts. For a more complete description of each module, see Section 2.2, “Framework Modules”.

表2.1. Spring框架的artifact
#### Table 2.1. Spring Framework Artifacts

GroupId | ArtifactId | Description
------------ | ------------- | ------
org.springframework | spring-aop | Proxy-based AOP support
-| | 基于代理的AOP支持
org.springframework |spring-aspects|AspectJ based aspects
-| | 基于AspectJ 的切面编程
org.springframework |spring-beans |Beans support, including Groovy
-| | Beans的支持, 包括 Groovy
org.springframework |spring-context |Application context runtime, including scheduling and remoting abstractions
-| | 应用程序运行时上下文,包括调度和远程抽象
org.springframework |spring-context-support |Support classes for integrating common third-party libraries into a Spring application context
-| | 提供第三方库到Spring的支持
org.springframework |spring-core|Core utilities, used by many other Spring modules
-| | 核心工具类，被其他模块使用
org.springframework |spring-expression|Spring Expression Language (SpEL)
-| | Spring 表达式语言 (SpEL)
org.springframework |spring-instrument|Instrumentation agent for JVM bootstrapping
-| | 	代理JVM相关的
org.springframework |spring-instrument-tomcat |Instrumentation agent for Tomcat
-| | Tomcat的插件
org.springframework |spring-jdbc|JDBC support package, including DataSource setup and JDBC access support
-| | 提供jdbc的支持，包括数据源和JDBC访问
org.springframework |spring-jms|JMS support package, including helper classes to send/receive JMS messages
-| | JMS支持包，包括接收和发送消息类的支持
org.springframework |spring-messaging|Support for messaging architectures and protocols
-| | 对消息传递体系结构和协议的支持
org.springframework |spring-orm|Object/Relational Mapping, including JPA and Hibernate support
-| | 对象/关系  映射, 拨快对JPA 和 Hibernate的支持
org.springframework |spring-oxm |Object/XML Mapping
-| | 对象/XML 映射的支持
org.springframework |spring-test |Support for unit testing and integration testing Spring components
-| | 对Spring组件单元测试和集成测试的支持
org.springframework |spring-tx |Transaction infrastructure, including DAO support and JCA integration
-| | 事物支持，包块DAO支持和JCA集成
org.springframework |spring-web |Foundational web support, including web client and web-based remoting
-| | Web 支持，包括客户端和Web访问
org.springframework |spring-webmvc |HTTP-based Model-View-Controller and REST endpoints for Servlet stacks
-| | 一个MVC框架
org.springframework |spring-webmvc-portlet |MVC implementation to be used in a Portlet environment
-| | 用于Portlet环境的MVC实现
org.springframework |spring-websocket |WebSocket and SockJS infrastructure, including STOMP messaging support
-| | WebSocket 和SockJS 的实现，包括对STOMP的支持

#### Spring Dependencies and Depending on Spring  
Spring的依赖和被依赖

Spring对大部分企业和其它外部工具提供了集成和支持，把强制性的外部依赖降到了最低，这样就不需要为了简单地使用Spring而去寻找和下载大量的jar包了。基本的依赖注入只有一个强制性的外部依赖，那就是日志管理（参考下面关于日志管理选项的详细描述）。

Although Spring provides integration and support for a huge range of enterprise and other external tools, it intentionally keeps its mandatory dependencies to an absolute minimum: you shouldn’t have to locate and download (even automatically) a large number of jar libraries in order to use Spring for simple use cases. For basic dependency injection there is only one mandatory external dependency, and that is for logging (see below for a more detailed description of logging options).

下面列出依赖于Spring的应用的基本配置步骤，首先使用Maven，然后Gradle，最后Ivy。在所有案例中，如果有什么不清楚的地方，参考所用的依赖管理系统的文档或查看一些范例代码——Spring构建时本身使用Gradle管理依赖，所以我们的范例大部分使用Gradle或Maven。

Next we outline the basic steps needed to configure an application that depends on Spring, first with Maven and then with Gradle and finally using Ivy. In all cases, if anything is unclear, refer to the documentation of your dependency management system, or look at some sample code - Spring itself uses Gradle to manage dependencies when it is building, and our samples mostly use Gradle or Maven.

#### Maven Dependency Management  
If you are using Maven for dependency management you don’t even need to supply the logging dependency explicitly. For example, to create an application context and use dependency injection to configure an application, your Maven dependencies will look like this:
```xml
<dependencies>
    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-context</artifactId>
        <version>4.3.20.BUILD-SNAPSHOT</version>
        <scope>runtime</scope>
    </dependency>
</dependencies>
```
That’s it. Note the scope can be declared as runtime if you don’t need to compile against Spring APIs, which is typically the case for basic dependency injection use cases.

The example above works with the Maven Central repository. To use the Spring Maven repository (e.g. for milestones or developer snapshots), you need to specify the repository location in your Maven configuration. For full releases:
```xml
<repositories>
    <repository>
        <id>io.spring.repo.maven.release</id>
        <url>http://repo.spring.io/release/</url>
        <snapshots><enabled>false</enabled></snapshots>
    </repository>
</repositories>
```
For milestones:
```xml
<repositories>
    <repository>
        <id>io.spring.repo.maven.milestone</id>
        <url>http://repo.spring.io/milestone/</url>
        <snapshots><enabled>false</enabled></snapshots>
    </repository>
</repositories>
```
And for snapshots:
```xml
<repositories>
    <repository>
        <id>io.spring.repo.maven.snapshot</id>
        <url>http://repo.spring.io/snapshot/</url>
        <snapshots><enabled>true</enabled></snapshots>
    </repository>
</repositories>
```

#### Maven "Bill Of Materials" Dependency
It is possible to accidentally mix different versions of Spring JARs when using Maven. For example, you may find that a third-party library, or another Spring project, pulls in a transitive dependency to an older release. If you forget to explicitly declare a direct dependency yourself, all sorts of unexpected issues can arise.

To overcome such problems Maven supports the concept of a "bill of materials" (BOM) dependency. You can import the spring-framework-bom in your dependencyManagement section to ensure that all spring dependencies (both direct and transitive) are at the same version.
```xml
<dependencyManagement>
    <dependencies>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-framework-bom</artifactId>
            <version>4.3.20.BUILD-SNAPSHOT</version>
            <type>pom</type>
            <scope>import</scope>
        </dependency>
    </dependencies>
</dependencyManagement>
```
An added benefit of using the BOM is that you no longer need to specify the <version> attribute when depending on Spring Framework artifacts:
```xml
<dependencies>
    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-context</artifactId>
    </dependency>
    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-web</artifactId>
    </dependency>
<dependencies>
```
#### Gradle Dependency Management
To use the Spring repository with the Gradle build system, include the appropriate URL in the repositories section:
```
repositories {
    mavenCentral()
    // and optionally...
    maven { url "http://repo.spring.io/release" }
}
```
You can change the repositories URL from /release to /milestone or /snapshot as appropriate. Once a repository has been configured, you can declare dependencies in the usual Gradle way:
```
dependencies {
    compile("org.springframework:spring-context:4.3.20.BUILD-SNAPSHOT")
    testCompile("org.springframework:spring-test:4.3.20.BUILD-SNAPSHOT")
}
```
#### Ivy Dependency Management
If you prefer to use Ivy to manage dependencies then there are similar configuration options.

To configure Ivy to point to the Spring repository add the following resolver to your ivysettings.xml:
```xml
<resolvers>
    <ibiblio name="io.spring.repo.maven.release"
            m2compatible="true"
            root="http://repo.spring.io/release/"/>
</resolvers>
```
You can change the root URL from /release/ to /milestone/ or /snapshot/ as appropriate.

Once configured, you can add dependencies in the usual way. For example (in ivy.xml):
```xml
<dependency org="org.springframework"
    name="spring-core" rev="4.3.20.BUILD-SNAPSHOT" conf="compile->runtime"/>
```
#### Distribution Zip Files
Although using a build system that supports dependency management is the recommended way to obtain the Spring Framework, it is still possible to download a distribution zip file.

Distribution zips are published to the Spring Maven Repository (this is just for our convenience, you don’t need Maven or any other build system in order to download them).

To download a distribution zip open a web browser to http://repo.spring.io/release/org/springframework/spring and select the appropriate subfolder for the version that you want. Distribution files end -dist.zip, for example spring-framework-{spring-version}-RELEASE-dist.zip. Distributions are also published for milestones and snapshots.

### 2.3.2 Logging
2.3.2 日志记录
对于Spring来说日志管理是非常重要的依赖关系，因为a）它是唯一的强制性外部依赖，b）每个人都喜欢从他们使用的工具看到一些输出，c）Spring集成了许多其它工具，它们都选择了日志管理的依赖。应用程序开发者的目标之一通常是在整个应用程序（包括所有的外部组件）的中心位置统一配置日志管理，这是非常困难的因为现在有很多日志管理的框架可供选择。  
Logging is a very important dependency for Spring because a) it is the only mandatory external dependency, b) everyone likes to see some output from the tools they are using, and c) Spring integrates with lots of other tools all of which have also made a choice of logging dependency. One of the goals of an application developer is often to have unified logging configured in a central place for the whole application, including all external components. This is more difficult than it might have been since there are so many choices of logging framework.

Spring中强制的日志管理依赖是Jakarta Commons Logging API（JCL）。我们编译了JCL，并使JCL的Log对象对继承了Spring框架的类可见。对用户来说所有版本的Spring使用相同的日志管理库很重要：迁移很简单因为Spring保存了向后兼容，即使对于扩展了Spring的应用也能向后兼容。我们是怎么做到的呢？我们让Spring的一个模块明确地依赖于commons-logging（JCL的典型实现），然后让所有其它模块都在编译期依赖于这个模块。例如，使用Maven，你想知道哪里依赖了commons-logging，其实是Spring确切地说是其核心模块spring-core依赖了。

The mandatory logging dependency in Spring is the Jakarta Commons Logging API (JCL). We compile against JCL and we also make JCL Log objects visible for classes that extend the Spring Framework. It’s important to users that all versions of Spring use the same logging library: migration is easy because backwards compatibility is preserved even with applications that extend Spring. The way we do this is to make one of the modules in Spring depend explicitly on commons-logging (the canonical implementation of JCL), and then make all the other modules depend on that at compile time. If you are using Maven for example, and wondering where you picked up the dependency on commons-logging, then it is from Spring and specifically from the central module called spring-core.

commons-logging的优点是不需要其它任何东西就可以使应用程序运转起来。它拥有一个运行时发现算法用于在classpath中寻找其它日志管理框架并且适当地选择一个使用（或者告诉它使用哪个）。如果不需要其它的功能了，你就可以从JDK（java.util.logging或JUL）得到一份看起来很漂亮的日志了。你会发现大多数情况下你的Spring应用程序工作得很好且日志很好地输出到了控制台，这很重要。

The nice thing about commons-logging is that you don’t need anything else to make your application work. It has a runtime discovery algorithm that looks for other logging frameworks in well known places on the classpath and uses one that it thinks is appropriate (or you can tell it which one if you need to). If nothing else is available you get pretty nice looking logs just from the JDK (java.util.logging or JUL for short). You should find that your Spring application works and logs happily to the console out of the box in most situations, and that’s important.

#### Using Log4j 1.2 or 2.x
[Note]
Log4j 1.2 is EOL in the meantime. Also, Log4j 2.3 is the last Java 6 compatible release, with newer Log4j 2.x releases requiring Java 7+.

Many people use Log4j as a logging framework for configuration and management purposes. It is efficient and well-established, and in fact it is what we use at runtime when we build Spring. Spring also provides some utilities for configuring and initializing Log4j, so it has an optional compile-time dependency on Log4j in some modules.

To make Log4j 1.2 work with the default JCL dependency (commons-logging) all you need to do is put Log4j on the classpath, and provide it with a configuration file (log4j.properties or log4j.xml in the root of the classpath). So for Maven users this is your dependency declaration:
```xml
<dependencies>
    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-core</artifactId>
        <version>4.3.20.BUILD-SNAPSHOT</version>
    </dependency>
    <dependency>
        <groupId>log4j</groupId>
        <artifactId>log4j</artifactId>
        <version>1.2.17</version>
    </dependency>
</dependencies>```
And here’s a sample log4j.properties for logging to the console:

```properties
log4j.rootCategory=INFO, stdout

log4j.appender.stdout=org.apache.log4j.ConsoleAppender
log4j.appender.stdout.layout=org.apache.log4j.PatternLayout
log4j.appender.stdout.layout.ConversionPattern=%d{ABSOLUTE} %5p %t %c{2}:%L - %m%n

log4j.category.org.springframework.beans.factory=DEBUG
```
To use Log4j 2.x with JCL, all you need to do is put Log4j on the classpath and provide it with a configuration file (log4j2.xml, log4j2.properties, or other supported configuration formats). For Maven users, the minimal dependencies needed are:
```xml
<dependencies>
    <dependency>
        <groupId>org.apache.logging.log4j</groupId>
        <artifactId>log4j-core</artifactId>
        <version>2.6.2</version>
    </dependency>
    <dependency>
        <groupId>org.apache.logging.log4j</groupId>
        <artifactId>log4j-jcl</artifactId>
        <version>2.6.2</version>
    </dependency>
</dependencies>```
If you also wish to enable SLF4J to delegate to Log4j, e.g. for other libraries which use SLF4J by default, the following dependency is also needed:
```xml
<dependencies>
  <dependency>
    <groupId>org.apache.logging.log4j</groupId>
    <artifactId>log4j-slf4j-impl</artifactId>
    <version>2.6.2</version>
  </dependency>
</dependencies>```
Here is an example log4j2.xml for logging to the console:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<Configuration status="WARN">
  <Appenders>
    <Console name="Console" target="SYSTEM_OUT">
      <PatternLayout pattern="%d{HH:mm:ss.SSS} [%t] %-5level %logger{36} - %msg%n"/>
    </Console>
  </Appenders>
  <Loggers>
    <Logger name="org.springframework.beans.factory" level="DEBUG"/>
    <Root level="error">
      <AppenderRef ref="Console"/>
    </Root>
  </Loggers>
</Configuration>
```
__不使用Commons Logging__
Avoiding Commons Logging

不幸的是，commons-logging的运行时发现算法虽然对于终端用户很方便，但存在一定的问题。如果你想避免JCL标准查找，有两种方式关掉commons-logging：

Unfortunately, the runtime discovery algorithm in the standard commons-logging API, while convenient for the end-user, can be problematic. If you’d like to avoid JCL’s standard lookup, there are basically two ways to switch it off:


Exclude the dependency from the spring-core module (as it is the only module that explicitly depends on commons-logging)
Depend on a special commons-logging dependency that replaces the library with an empty jar (more details can be found in the SLF4J FAQ)

在dependencyManagement中添加部分代码就可以排除掉commons-logging了：
To exclude commons-logging, add the following to your dependencyManagement section:

```xml
<dependencies>
    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-core</artifactId>
        <version>4.3.20.BUILD-SNAPSHOT</version>
        <exclusions>
            <exclusion>
                <groupId>commons-logging</groupId>
                <artifactId>commons-logging</artifactId>
            </exclusion>
        </exclusions>
    </dependency>
</dependencies>
```
现在这个应用可能是残缺的，因为在classpath上没有JCL API的实现，所以需要提供一个新的去修复它。下个章节我们将以SLF4J为例子为JCL提供一个替代实现。

Now this application is currently broken because there is no implementation of the JCL API on the classpath, so to fix it a new one has to be provided. In the next section we show you how to provide an alternative implementation of JCL using SLF4J.

__使用SLF4j与Log4j或者Logback__
#### Using SLF4J with Log4j or Logback

The Simple Logging Facade for Java (SLF4J) is a popular API used by other libraries commonly used with Spring. It is typically used with Logback which is a native implementation of the SLF4J API.

SLF4J provides bindings to many common logging frameworks, including Log4j, and it also does the reverse: bridges between other logging frameworks and itself. So to use SLF4J with Spring you need to replace the commons-logging dependency with the SLF4J-JCL bridge. Once you have done that then logging calls from within Spring will be translated into logging calls to the SLF4J API, so if other libraries in your application use that API, then you have a single place to configure and manage logging.

A common choice might be to bridge Spring to SLF4J, and then provide explicit binding from SLF4J to Log4j. You need to supply several dependencies (and exclude the existing commons-logging): the JCL bridge, the SLF4j binding to Log4j, and the Log4j provider itself. In Maven you would do that like this
```xml
<dependencies>
    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-core</artifactId>
        <version>4.3.20.BUILD-SNAPSHOT</version>
        <exclusions>
            <exclusion>
                <groupId>commons-logging</groupId>
                <artifactId>commons-logging</artifactId>
            </exclusion>
        </exclusions>
    </dependency>
    <dependency>
        <groupId>org.slf4j</groupId>
        <artifactId>jcl-over-slf4j</artifactId>
        <version>1.7.21</version>
    </dependency>
    <dependency>
        <groupId>org.slf4j</groupId>
        <artifactId>slf4j-log4j12</artifactId>
        <version>1.7.21</version>
    </dependency>
    <dependency>
        <groupId>log4j</groupId>
        <artifactId>log4j</artifactId>
        <version>1.2.17</version>
    </dependency>
</dependencies>
```
A more common choice amongst SLF4J users, which uses fewer steps and generates fewer dependencies, is to bind directly to Logback. This removes the extra binding step because Logback implements SLF4J directly, so you only need to depend on just two libraries, namely jcl-over-slf4j and logback):

```xml
<dependencies>
    <dependency>
        <groupId>org.slf4j</groupId>
        <artifactId>jcl-over-slf4j</artifactId>
        <version>1.7.21</version>
    </dependency>
    <dependency>
        <groupId>ch.qos.logback</groupId>
        <artifactId>logback-classic</artifactId>
        <version>1.1.7</version>
    </dependency>
</dependencies>
```

#### Using JUL (java.util.logging)
Commons Logging will delegate to java.util.logging by default, provided that no Log4j is detected on the classpath. So there is no special dependency to set up: just use Spring with no external dependency for log output to java.util.logging, either in a standalone application (with a custom or default JUL setup at the JDK level) or with an application server’s log system (and its system-wide JUL setup).

#### Commons Logging on WebSphere
Spring applications may run on a container that itself provides an implementation of JCL, e.g. IBM’s WebSphere Application Server (WAS). This does not cause issues per se but leads to two different scenarios that need to be understood:

In a "parent first" ClassLoader delegation model (the default on WAS), applications will always pick up the server-provided version of Commons Logging, delegating to the WAS logging subsystem (which is actually based on JUL). An application-provided variant of JCL, whether standard Commons Logging or the JCL-over-SLF4J bridge, will effectively be ignored, along with any locally included log provider.

With a "parent last" delegation model (the default in a regular Servlet container but an explicit configuration option on WAS), an application-provided Commons Logging variant will be picked up, enabling you to set up a locally included log provider, e.g. Log4j or Logback, within your application. In case of no local log provider, regular Commons Logging will delegate to JUL by default, effectively logging to WebSphere’s logging subsystem like in the "parent first" scenario.

All in all, we recommend deploying Spring applications in the "parent last" model since it naturally allows for local providers as well as the server’s log subsystem.

## 参考：
https://blog.csdn.net/tangtong1/article/details/51326887