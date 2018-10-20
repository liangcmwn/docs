# 三、核心技术
Part III. Core Technologies

这部分的文档覆盖了Spring完整的技术。

This part of the reference documentation covers all of those technologies that are absolutely integral to the Spring Framework.

在这些技术中最重要的要属Spring的控制反转（IoC）容器了，紧随其后的是全面覆盖的面向切面编程（AOP）技术。Spring有它自己的AOP框架，它很容易理解，而且成功解决了Java企业编程中80%的AOP需求。

Foremost amongst these is the Spring Framework’s Inversion of Control (IoC) container. A thorough treatment of the Spring Framework’s IoC container is closely followed by comprehensive coverage of Spring’s Aspect-Oriented Programming (AOP) technologies. The Spring Framework has its own AOP framework, which is conceptually easy to understand, and which successfully addresses the 80% sweet spot of AOP requirements in Java enterprise programming.

Spring也集成了AspectsJ（目前在Java领域使用最丰富最成熟的AOP实现 ）。
Coverage of Spring’s integration with AspectJ (currently the richest - in terms of features - and certainly most mature AOP implementation in the Java enterprise space) is also provided.

* 7.IoC容器
Chapter 7, The IoC container
* 8.资源
Chapter 8, Resources
* 9.数据检验、数据绑定、类型转换
Chapter 9, Validation, Data Binding, and Type Conversion
* 10.Spring表达式语言
Chapter 10, Spring Expression Language (SpEL)
* 11.Spring的面向切面编程
Chapter 11, Aspect Oriented Programming with Spring
* 12.Spring AOP的API
Chapter 12, Spring AOP APIs

# 7. IoC容器
7. The IoC container
## 7.1 Spring的IoC容器和bean的简介
7.1 Introduction to the Spring IoC container and beans

本章主要介绍Spring关于控制反转的实现。控制反转（IoC）又被称作依赖注入（DI）。它是一个对象定义其依赖的过程，它的依赖也就是与它一起合作的其它对象，这个过程只能通过构造方法参数、工厂方法参数、或者被构造或从工厂方法返回后通过settter方法设置其属性来实现。然后容器在创建bean时注入这些依赖关系。这个过程本质上是反过来的，由bean本身控制实例化，或者直接通过类的结构或Service定位器模式定位它自己的依赖，因此得其名曰控制反转。

This chapter covers the Spring Framework implementation of the Inversion of Control (IoC) [1] principle. IoC is also known as dependency injection (DI). It is a process whereby objects define their dependencies, that is, the other objects they work with, only through constructor arguments, arguments to a factory method, or properties that are set on the object instance after it is constructed or returned from a factory method. The container then injects those dependencies when it creates the bean. This process is fundamentally the inverse, hence the name Inversion of Control (IoC), of the bean itself controlling the instantiation or location of its dependencies by using direct construction of classes, or a mechanism such as the Service Locator pattern.

org.springframework.beans和org.springframework.context两个包是Spring IoC容器的基础。BeanFactory接口提供了一种先进的管理任何类型对象的配置机制。ApplicationContext是BeanFactory的子接口，它使得与Spring AOP特性集成更简单，添加了消息资源处理（用于国际化）、事件发布，还添加了应用层特定的上下文，比如用于web应用的WebApplicationContext。

The org.springframework.beans and org.springframework.context packages are the basis for Spring Framework’s IoC container. The BeanFactory interface provides an advanced configuration mechanism capable of managing any type of object. ApplicationContext is a sub-interface of BeanFactory. It adds easier integration with Spring’s AOP features; message resource handling (for use in internationalization), event publication; and application-layer specific contexts such as the WebApplicationContext for use in web applications.

简而言之，BeanFactory提供了配置框架和基础功能，ApplicationContext在此基础上添加了更多的企业级特定功能。ApplicationContext是BeanFactory的完整超集，且仅用于本章描述Spring的IoC容器。更多使用BeanFactory代替ApplicationContext的信息请参考7.16 BeanFactory“。

In short, the BeanFactory provides the configuration framework and basic functionality, and the ApplicationContext adds more enterprise-specific functionality. The ApplicationContext is a complete superset of the BeanFactory, and is used exclusively in this chapter in descriptions of Spring’s IoC container. For more information on using the BeanFactory instead of the ApplicationContext, refer to Section 7.16, “The BeanFactory”.

在Spring中，形成应用主干且被Spring IoC容器管理的对象称为beans。一个bean就是被Spring IoC容器实例化、装配和管理的对象。简单来说，一个bean就是你的应用中众多对象中一个。Beans和它们之间的依赖被容器中配置的元数据反射。

In Spring, the objects that form the backbone of your application and that are managed by the Spring IoC container are called beans. A bean is an object that is instantiated, assembled, and otherwise managed by a Spring IoC container. Otherwise, a bean is simply one of many objects in your application. Beans, and the dependencies among them, are reflected in the configuration metadata used by a container.

## 7.2 容器概述
7.2 Container overview

`org.springframework.context.ApplicationContext`代表了`Spring`的IoC容器，并且负责实例化、配置和装配前面提及的bean。容器通过读取配置元数据获取指令来决定哪些对象将被实例化、配置和装配。配置元数据反映在XML、Java注解或者Java代码中。它允许你表达组合成应用的对象及它们之间丰富的依赖关系。

The interface org.springframework.context.ApplicationContext represents the Spring IoC container and is responsible for instantiating, configuring, and assembling the aforementioned beans. The container gets its instructions on what objects to instantiate, configure, and assemble by reading configuration metadata. The configuration metadata is represented in XML, Java annotations, or Java code. It allows you to express the objects that compose your application and the rich interdependencies between such objects.

Spring提供了几个可以直接使用的`ApplicationContext`接口的实现。在独立的应用中通常创建`ClassPathXmlApplicationContext`或者`FileSystemXmlApplicationContext`的实例。XML是定义配置元数据的传统形式，也可以通过添加一小段XML配置让容器声明式地支持Java注解或Java代码配置元数据的形式。

Several implementations of the ApplicationContext interface are supplied out-of-the-box with Spring. In standalone applications it is common to create an instance of ClassPathXmlApplicationContext or FileSystemXmlApplicationContext. While XML has been the traditional format for defining configuration metadata you can instruct the container to use Java annotations or code as the metadata format by providing a small amount of XML configuration to declaratively enable support for these additional metadata formats.

在大部分应用场景中，显式的代码不需要实例化一个或多个Spring IoC容器。例如，在web应用中，在web.xml中引用简单的八行（大约）样板XML描述符足够了（参考7.15.4 web应用中方便地实例化ApplicationContext](#context-create)）。如果使用Spring工具套件Eclipse开发环境，样板配置只需要简单地点击几次鼠标或键盘就可以被创建。

In most application scenarios, explicit user code is not required to instantiate one or more instances of a Spring IoC container. For example, in a web application scenario, a simple eight (or so) lines of boilerplate web descriptor XML in the web.xml file of the application will typically suffice (see Section 7.15.4, “Convenient ApplicationContext instantiation for web applications”). If you are using the Spring Tool Suite Eclipse-powered development environment this boilerplate configuration can be easily created with few mouse clicks or keystrokes.

下面的图表展示了Spring是怎么工作的。应用程序的类文件和配置元数据是绑定在一起的，所以在ApplicationContext被创建和初始化后，你将拥有完整的已配置好的和可执行的系统或应用。 

The following diagram is a high-level view of how Spring works. Your application classes are combined with configuration metadata so that after the ApplicationContext is created and initialized, you have a fully configured and executable system or application.

图7.1 Spring的IoC容器 
Figure 7.1. The Spring IoC container
![](https://docs.spring.io/spring/docs/4.3.20.BUILD-SNAPSHOT/spring-framework-reference/htmlsingle/images/container-magic.png)

container magic

7.2.1 配置元数据
### 7.2.1 Configuration metadata
如前述图表所述，Spring IoC容器是消费配置元数据的一种形式，配置元数据代表开发者告诉Spring容器怎么去实例化、配置和装配应用中的对象。

As the preceding diagram shows, the Spring IoC container consumes a form of configuration metadata; this configuration metadata represents how you as an application developer tell the Spring container to instantiate, configure, and assemble the objects in your application.

配置元数据习惯上使用简单直观的XML形式，本章大部分地方也使用这种形式来传达Spring IoC容器的关键概念和特性。

Configuration metadata is traditionally supplied in a simple and intuitive XML format, which is what most of this chapter uses to convey key concepts and features of the Spring IoC container.

[Note]
* 基于XML的元数据不是配置元数据的唯一方式，Spring IoC容器本身是与实际写配置元数据的形式完全解耦的，现在许多开发者选择基于Java的配置形式来配置Spring应用程序。  
XML-based metadata is not the only allowed form of configuration metadata. The Spring IoC container itself is totally decoupled from the format in which this configuration metadata is actually written. These days many developers choose Java-based configuration for their Spring applications.

关于使用其它形式配置元数据，参考：

For information about using other forms of metadata with the Spring container, see:

* 基于注解的配置，Spring 2.5引入了基于注解配置元数据的形式。  
Annotation-based configuration: Spring 2.5 introduced support for annotation-based configuration metadata.

* 基于Java的配置，从Spring 3.0开始，Spring JavaConfig项目提供的许多特性成为了Spring框架核心的一部分。因引，可以在应用程序类的外部通过Java定义bean而不是在XML文件中。使用这些新特性，请参考@Configuration, @Bean, @Import和@DepensOn注解。  
Java-based configuration: Starting with Spring 3.0, many features provided by the Spring JavaConfig project became part of the core Spring Framework. Thus you can define beans external to your application classes by using Java rather than XML files. To use these new features, see the @Configuration, @Bean, @Import and @DependsOn annotations.

Spring配置包含了至少一个必须容器管理的bean定义。基于XML的配置元数据显示为被顶级<beans/>元素包含的<bean/>元素。Java配置典型地使用@Bean注解@Configuration类中方法。

Spring configuration consists of at least one and typically more than one bean definition that the container must manage. XML-based configuration metadata shows these beans configured as <bean/> elements inside a top-level <beans/> element. Java configuration typically uses @Bean annotated methods within a @Configuration class.

这些bean定义与组成应用的实际对象通信。典型地，可以定义service层对象、数据访问对象（DAO）、诸如Struts Action实例的视图层对象、基础对象如Hibernate SessionFactories、JMS Queues，等等，也不需要在容器中定义细粒度的领域对象，因为通常由DAO层和业务逻辑来创建和加载领域对象。然而，可以使用与AspectJ的集成来配置在IoC容器控制之外创建的对象。参考使用AspectJ依赖注入领域对象。

These bean definitions correspond to the actual objects that make up your application. Typically you define service layer objects, data access objects (DAOs), presentation objects such as Struts Action instances, infrastructure objects such as Hibernate SessionFactories, JMS Queues, and so forth. Typically one does not configure fine-grained domain objects in the container, because it is usually the responsibility of DAOs and business logic to create and load domain objects. However, you can use Spring’s integration with AspectJ to configure objects that have been created outside the control of an IoC container. See Using AspectJ to dependency-inject domain objects with Spring.

下面展示了XML配置的基础结构：
The following example shows the basic structure of XML-based configuration metadata:
```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://www.springframework.org/schema/beans
        http://www.springframework.org/schema/beans/spring-beans.xsd">

    <bean id="..." class="...">
        <!-- collaborators and configuration for this bean go here -->
    </bean>

    <bean id="..." class="...">
        <!-- collaborators and configuration for this bean go here -->
    </bean>

    <!-- more bean definitions go here -->

</beans>
```

id属性是一个字符串，它唯一标识一个bean的定义，class属性定义了bean的类型并且需要使用全路径。id属性的值指向了合作的对象。XML指定合作者XML并没有显示在上述例子中，具体可参考依赖。

The id attribute is a string that you use to identify the individual bean definition. The class attribute defines the type of the bean and uses the fully qualified classname. The value of the id attribute refers to collaborating objects. The XML for referring to collaborating objects is not shown in this example; see Dependencies for more information.

### 7.2.2 实例化容器
7.2.2 Instantiating a container

实例一个Spring IoC容器很简单，提供给ApplicationContext构造方法的一个或多个路径是实际的资源字符串，这使得容器可以从不同的外部文件加载配置元数据，比如本地的文件系统，Java的CLASSPATH，等等。

Instantiating a Spring IoC container is straightforward. The location path or paths supplied to an ApplicationContext constructor are actually resource strings that allow the container to load configuration metadata from a variety of external resources such as the local file system, from the Java CLASSPATH, and so on.

`ApplicationContext context = new ClassPathXmlApplicationContext("services.xml", "daos.xml");`
[Note]
* 在学习Spring IoC容器之后，你可能想更多地了解Spring的Resource概念，它提供了一种很便利的机制以URI语法的形式读取输入流，参考7 资源。特别地，Resource路径可被用于构造应用上下文，请参考7.7 应用上下文与资源路径。  
After you learn about Spring’s IoC container, you may want to know more about Spring’s Resource abstraction, as described in Chapter 8, Resources, which provides a convenient mechanism for reading an InputStream from locations defined in a URI syntax. In particular, Resource paths are used to construct applications contexts as described in Section 8.7, “Application contexts and Resource paths”.

下面的例子展示了service层的对象配置文件（services.xml）：  
The following example shows the service layer objects (services.xml) configuration file:
```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://www.springframework.org/schema/beans
        http://www.springframework.org/schema/beans/spring-beans.xsd">

    <!-- services -->

    <bean id="petStore" class="org.springframework.samples.jpetstore.services.PetStoreServiceImpl">
        <property name="accountDao" ref="accountDao"/>
        <property name="itemDao" ref="itemDao"/>
        <!-- additional collaborators and configuration for this bean go here -->
    </bean>

    <!-- more bean definitions for services go here -->

</beans>
```
下面的例子展示了数据访问对象daos.xml文件：
The following example shows the data access objects daos.xml file:
```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://www.springframework.org/schema/beans
        http://www.springframework.org/schema/beans/spring-beans.xsd">

    <bean id="accountDao"
        class="org.springframework.samples.jpetstore.dao.jpa.JpaAccountDao">
        <!-- additional collaborators and configuration for this bean go here -->
    </bean>

    <bean id="itemDao" class="org.springframework.samples.jpetstore.dao.jpa.JpaItemDao">
        <!-- additional collaborators and configuration for this bean go here -->
    </bean>

    <!-- more bean definitions for data access objects go here -->

</beans>
```
在前面的例子中，service层包含类PetStoreServiceImpl和两个数据访问对象JpaAccountDao和JpaItemDao（基于JPA 对象关系映射标准）。property元素的name属性指向了JavaBean属性的名字，ref属性指向另一个bean定义的名字。id和ref之间的链接表达了两个合作者间的依赖关系。详细的配置和对象依赖请参考依赖。

In the preceding example, the service layer consists of the class PetStoreServiceImpl, and two data access objects of the type JpaAccountDao and JpaItemDao (based on the JPA Object/Relational mapping standard). The property name element refers to the name of the JavaBean property, and the ref element refers to the name of another bean definition. This linkage between id and ref elements expresses the dependency between collaborating objects. For details of configuring an object’s dependencies, see Dependencies.

__组合基于XML的配置元数据__
Composing XML-based configuration metadata

跨越多个XML文件配置bean定义是很有用的，通常一个独立的XML配置文件代表架构中的一个逻辑层或模块。

It can be useful to have bean definitions span multiple XML files. Often each individual XML configuration file represents a logical layer or module in your architecture.

可以使用ApplicationContext的构造方法加载所有这些XML片段的bean定义。构造方法传入多个Resource路径即可，就像前面章节介绍的那样。替代方案，也可以使用一个或多个<import/>元素从其它文件加载bean定义。例如：

You can use the application context constructor to load bean definitions from all these XML fragments. This constructor takes multiple Resource locations, as was shown in the previous section. Alternatively, use one or more occurrences of the <import/> element to load bean definitions from another file or files. For example:
```xml
<beans>
    <import resource="services.xml"/>
    <import resource="resources/messageSource.xml"/>
    <import resource="/resources/themeSource.xml"/>

    <bean id="bean1" class="..."/>
    <bean id="bean2" class="..."/>
</beans>
```
上面的例子中，外部的bean定义从三个文件中加载进来：services.xml, messageSource.xml和themeSource.xml。所有的路径对于当前文件都是相对路径，所以services.xml必须在同一个目录或当前路径所在的classpath下，而messageSource.xml和themeSource.xml则必须在resources路径下，resources必须当前文件目录的下一级。如你所见，开头的斜杠被忽略了，即使给了路径也是相对的，不使用开头的斜杠格式还更好点。被导入文件的内容，包括顶级的<beans/>元素，必须是Spring Schema验证通过的XML bean定义。

In the preceding example, external bean definitions are loaded from three files: services.xml, messageSource.xml, and themeSource.xml. All location paths are relative to the definition file doing the importing, so services.xml must be in the same directory or classpath location as the file doing the importing, while messageSource.xml and themeSource.xml must be in a resources location below the location of the importing file. As you can see, a leading slash is ignored, but given that these paths are relative, it is better form not to use the slash at all. The contents of the files being imported, including the top level <beans/> element, must be valid XML bean definitions according to the Spring Schema.

[Note]
* 可以使用“../”路径的形式引用父目录的文件，但这是不推荐的。这样做会在当前应用之外的文件上创建一个依赖。尤其是这种引用不推荐放在“classpath:”URL中（例如，“classpath:../services.xml”），在运行时会选择最近的classpath的根目录，然后再在其父目录中寻找。classpath配置改变可能会导致选择了不同的错误的目录。   
It is possible, but not recommended, to reference files in parent directories using a relative "../" path. Doing so creates a dependency on a file that is outside the current application. In particular, this reference is not recommended for "classpath:" URLs (for example, "classpath:../services.xml"), where the runtime resolution process chooses the "nearest" classpath root and then looks into its parent directory. Classpath configuration changes may lead to the choice of a different, incorrect directory.


可以总是使用全路径定位资源代替相对路径，例如“file:C:/config/services.xml”或“classpath:/config/services.xml”。然而，注意这样会把应用程序的配置与特定的绝对路径耦合了。一般地，可以间接配置那个绝对路径，例如，通过JVM系统属性“${…}”占位符在运行时加载。

You can always use fully qualified resource locations instead of relative paths: for example, "file:C:/config/services.xml" or "classpath:/config/services.xml". However, be aware that you are coupling your application’s configuration to specific absolute locations. It is generally preferable to keep an indirection for such absolute locations, for example, through "${…​}" placeholders that are resolved against JVM system properties at runtime.

The import directive is a feature provided by the beans namespace itself. Further configuration features beyond plain bean definitions are available in a selection of XML namespaces provided by Spring, e.g. the "context" and the "util" namespace.

The Groovy Bean Definition DSL
As a further example for externalized configuration metadata, bean definitions can also be expressed in Spring’s Groovy Bean Definition DSL, as known from the Grails framework. Typically, such configuration will live in a ".groovy" file with a structure as follows:
```groovy
beans {
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
This configuration style is largely equivalent to XML bean definitions and even supports Spring’s XML configuration namespaces. It also allows for importing XML bean definition files through an "importBeans" directive.

### 7.2.3 使用容器
7.2.3 Using the container
ApplicationContext接口是一个先进的工厂，用于维护不同的bean及其依赖关系的注册。使用T getBean(String name, Class requiredType)方法可以获取bean实例。

The ApplicationContext is the interface for an advanced factory capable of maintaining a registry of different beans and their dependencies. Using the method T getBean(String name, Class<T> requiredType) you can retrieve instances of your beans.

ApplicationContext允许你可以像下面这样读取bean定义并访问它们：  
The ApplicationContext enables you to read bean definitions and access them as follows:
```java
// create and configure beans
ApplicationContext context = new ClassPathXmlApplicationContext("services.xml", "daos.xml");

// retrieve configured instance
PetStoreService service = context.getBean("petStore", PetStoreService.class);

// use configured instance
List<String> userList = service.getUsernameList();
```
With Groovy configuration, bootstrapping looks very similar, just a different context implementation class which is Groovy-aware (but also understands XML bean definitions):

`ApplicationContext context = new GenericGroovyApplicationContext("services.groovy", "daos.groovy");`
The most flexible variant is GenericApplicationContext in combination with reader delegates, e.g. with XmlBeanDefinitionReader for XML files:

```java
GenericApplicationContext context = new GenericApplicationContext();
new XmlBeanDefinitionReader(context).loadBeanDefinitions("services.xml", "daos.xml");
context.refresh();
```
Or with GroovyBeanDefinitionReader for Groovy files:
```java
GenericApplicationContext context = new GenericApplicationContext();
new GroovyBeanDefinitionReader(context).loadBeanDefinitions("services.groovy", "daos.groovy");
context.refresh();
```
Such reader delegates can be mixed and matched on the same ApplicationContext, reading bean definitions from diverse configuration sources, if desired.

使用getBean()获取bean实例。ApplicationContext接口还有几个其它方法用于获取bean，但是理想状态下应用程序代码应该从来都不会用到它们。应用程序代码的解应该不会调用getBean()方法，而且不应该依赖于Spring的API。例如，Spring的web框架集成为不同的web框架类提供了依赖注入，比如controller和JSF管理的bean。

You can then use getBean to retrieve instances of your beans. The ApplicationContext interface has a few other methods for retrieving beans, but ideally your application code should never use them. Indeed, your application code should have no calls to the getBean() method at all, and thus no dependency on Spring APIs at all. For example, Spring’s integration with web frameworks provides dependency injection for various web framework components such as controllers and JSF-managed beans, allowing you to declare a dependency on a specific bean through metadata (e.g. an autowiring annotation).

## 7.3 Bean概述
7.3 Bean overview

Spring IoC容器管理了至少一个bean，这些bean通过提供给容器的配置元数据来创建，例如，XML形式的<bean/>定义。  
A Spring IoC container manages one or more beans. These beans are created with the configuration metadata that you supply to the container, for example, in the form of XML <bean/> definitions.

在容器本身内部，这些bean定义表示为BeanDefinition对象，它包含以下元数据：  
Within the container itself, these bean definitions are represented as BeanDefinition objects, which contain (among other information) the following metadata:

* 包限定的类名：被定义bean的实际实现类。  
A package-qualified class name: typically the actual implementation class of the bean being defined.
* bean行为的配置元素，在容器中bean以哪种状态表现（scope，lifecycle，callback，等等）。  
Bean behavioral configuration elements, which state how the bean should behave in the container (scope, lifecycle callbacks, and so forth).
* 需要一起工作的其它对象的引用，这些引用又被称作合作者（collaborator）或依赖。  
References to other beans that are needed for the bean to do its work; these references are also called collaborators or dependencies.
* 设置到新创建对象中的其它配置，例如，在bean中使用的连接数，用于管理连接池或池的大小。  
Other configuration settings to set in the newly created object, for example, the number of connections to use in a bean that manages a connection pool, or the size limit of the pool.

元数据转化成了一系列的属性，组成了每个bean的定义。   
This metadata translates to a set of properties that make up each bean definition.

表 7.1. bean定义
__Table 7.1. The bean definition__

Property |	Explained in…​
--|--
class | Section 7.3.2, “Instantiating beans” 	7.3.2 实例化bean
name | Section 7.3.1, “Naming beans” 7.3.1 命名bean
scope |Section 7.5, “Bean scopes” 	7.5 bean的作用域
constructor arguments 构造方法的参数 |Section 7.4.1, “Dependency Injection” 	7.4.1 依赖注入
properties 属性 |Section 7.4.1, “Dependency Injection” 7.4.1 依赖注入
autowiring mode 自动装配的模式 |Section 7.4.5, “Autowiring collaborators” 7.4.5 自动装配合作者
lazy-initialization mode 延迟初始化的模式 |Section 7.4.4, “Lazy-initialized beans” 7.4.4 延迟初始化bean
initialization method 初始化的模式 |the section called “Initialization callbacks” 初始化回调
destruction method 销毁方法 |the section called “Destruction callbacks” 销毁回调

除了包含创建指定bean的信息的bean定义外，ApplicationContext实现也允许注册用户在容器外创建的已存在的对象。可以通过ApplicationContext的getBeanFactory()方法获取其BeanFactory的实现DefaultListableBeanFactory。DefaultListableBeanFactory通过registerSingleton(…)和registerBeanDefinition(..)方法支持这样的注册。然而，典型的应用都是通过元数据定义bean。

In addition to bean definitions that contain information on how to create a specific bean, the ApplicationContext implementations also permit the registration of existing objects that are created outside the container, by users. This is done by accessing the ApplicationContext’s BeanFactory via the method getBeanFactory() which returns the BeanFactory implementation DefaultListableBeanFactory. DefaultListableBeanFactory supports this registration through the methods registerSingleton(..) and registerBeanDefinition(..). However, typical applications work solely with beans defined through metadata bean definitions.

[Note]
* bean元数据和手动提供的单例实例需要尽早注册，为了容器在自动装配时正确推断它们和其它内省的步骤。在一定程度上支持重写已存在的元数据和已存在的单例实例，然而在运行时注册新bean（并发访问工厂）并不正式地支持，并且可能在容器中导致并发访问异常和/或不一致的状态。  
Bean metadata and manually supplied singleton instances need to be registered as early as possible, in order for the container to properly reason about them during autowiring and other introspection steps. While overriding of existing metadata and existing singleton instances is supported to some degree, the registration of new beans at runtime (concurrently with live access to factory) is not officially supported and may lead to concurrent access exceptions and/or inconsistent state in the bean container.

### 7.3.1 命名bean
7.3.1 Naming beans  
每一个bean都有一个或多个标识符。这些标识符在托管bean的容器中必须唯一。一个bean通常只有一个标识符，但如果需要更多标识符，可以通过别名来实现。

Every bean has one or more identifiers. These identifiers must be unique within the container that hosts the bean. A bean usually has only one identifier, but if it requires more than one, the extra ones can be considered aliases.

在XML中，可以使用id和/或name属性来指定bean的标识符。id属性允许显式地指定一个id。按照约定，这些名字是字母数字的（’myBean’, ‘fooService’, 等），但是也可以包含一些特殊字符。如果你想引入其它别名，也可以在name属性中指定，用逗号（,）、分号（;）或空格分割。作为历史记录，Spring 3.1之前的版本，id属性被定义为xsd:ID，它会限制一些可能的字符。3.1开始，它被定义为xsd:string类型。注意id的唯一性是被容器强制执行的，而不再是XML解析器。

In XML-based configuration metadata, you use the id and/or name attributes to specify the bean identifier(s). The id attribute allows you to specify exactly one id. Conventionally these names are alphanumeric ('myBean', 'fooService', etc.), but may contain special characters as well. If you want to introduce other aliases to the bean, you can also specify them in the name attribute, separated by a comma (,), semicolon (;), or white space. As a historical note, in versions prior to Spring 3.1, the id attribute was defined as an xsd:ID type, which constrained possible characters. As of 3.1, it is defined as an xsd:string type. Note that bean id uniqueness is still enforced by the container, though no longer by XML parsers.

给bean指定一个名字或id并不是必需的。如果没有显式地指定名字或id，容器会为那个bean生成一个唯一的名字。但是，如果要根据名字引用那个bean，比如通过ref元素或Service定位器查找，那么必须指定一个名字。不提供名字一般用于内部bean和自动装配合作者。

You are not required to supply a name or id for a bean. If no name or id is supplied explicitly, the container generates a unique name for that bean. However, if you want to refer to that bean by name, through the use of the ref element or Service Locator style lookup, you must provide a name. Motivations for not supplying a name are related to using inner beans and autowiring collaborators.
* bean命名的约定   
Bean Naming Conventions  
命名bean时采用标准Java对字段的命名约定。bean名字以小写字母开头，然后是驼峰式。例如，（不带引号）‘accountManager’, ‘accountService’, ‘userDao’, ‘loginController’, 等等。   
The convention is to use the standard Java convention for instance field names when naming beans. That is, bean names start with a lowercase letter, and are camel-cased from then on. Examples of such names would be (without quotes) 'accountManager', 'accountService', 'userDao', 'loginController', and so forth.  
按统一的规则命名bean更易于阅读和理解，而且，如果使用Spring AOP，当通过名字应用advice到一系列bean上将会非常有帮助。  
Naming beans consistently makes your configuration easier to read and understand, and if you are using Spring AOP it helps a lot when applying advice to a set of beans related by name.

[Note]
With component scanning in the classpath, Spring generates bean names for unnamed components, following the rules above: essentially, taking the simple class name and turning its initial character to lower-case. However, in the (unusual) special case when there is more than one character and both the first and second characters are upper case, the original casing gets preserved. These are the same rules as defined by java.beans.Introspector.decapitalize (which Spring is using here).

__在bean定义外给bean起别名__
Aliasing a bean outside the bean definition  
在bean的定义中，可以为其提供多个名字，通过与id属性指定的名字或name属性里的其它名字组合起来。这些名字对同一个bean是等价的，在有些情况下很有用，比如，允许应用中的每个组件通过其本身指定的名字引用共同的依赖。

In a bean definition itself, you can supply more than one name for the bean, by using a combination of up to one name specified by the id attribute, and any number of other names in the name attribute. These names can be equivalent aliases to the same bean, and are useful for some situations, such as allowing each component in an application to refer to a common dependency by using a bean name that is specific to that component itself.

在bean定义的地方指定所有的别名并不足够。有时候希望在其它地方为bean指定一个别名。这在大型系统中很常见，它们的配置被很多子系统分割，每个子系统有它自己的一系列bean定义。在XML配置中，可以使用<alias/>元素指定别名。

Specifying all aliases where the bean is actually defined is not always adequate, however. It is sometimes desirable to introduce an alias for a bean that is defined elsewhere. This is commonly the case in large systems where configuration is split amongst each subsystem, each subsystem having its own set of object definitions. In XML-based configuration metadata, you can use the <alias/> element to accomplish this.

`<alias name="fromName" alias="toName"/>`

在上述这个案例中，同一个容器中这个bean叫fromName，在使用了别名定义也可以叫toName。  
In this case, a bean in the same container which is named fromName, may also, after the use of this alias definition, be referred to as toName.

例如，子系统A中的配置元数据通过subsystemA-datasource引用一个数据源，子系统B中的配置元数据通过subsystemB-datasource引用这个数据源，使用这两个子系统的主应用通过myApp-datasource引用这个数据源。为了使用这三个名字引用相同的对象，可以在MyApp配置中添加如下的别名定义：  
For example, the configuration metadata for subsystem A may refer to a DataSource via the name subsystemA-dataSource. The configuration metadata for subsystem B may refer to a DataSource via the name subsystemB-dataSource. When composing the main application that uses both these subsystems the main application refers to the DataSource via the name myApp-dataSource. To have all three names refer to the same object you add to the MyApp configuration metadata the following aliases definitions:
```xml
<alias name="subsystemA-dataSource" alias="subsystemB-dataSource"/>
<alias name="subsystemA-dataSource" alias="myApp-dataSource" />
```
现在每个组件和主应用都可以通过不同的名字引用这个数据源了，并且可以保证不会与任何其它的定义冲突（有效地创建了一个命名空间），它们还引用了同一个bean。

Now each component and the main application can refer to the dataSource through a name that is unique and guaranteed not to clash with any other definition (effectively creating a namespace), yet they refer to the same bean.

__Java配置__
Java-configuration  
如果使用Java配置，@Bean注解可以用于提供别名，参考7.12.3 使用@Bean注解。  
If you are using Java-configuration, the @Bean annotation can be used to provide aliases see Section 7.12.3, “Using the @Bean annotation” for details.

### 7.3.2 实例化bean
7.3.2 Instantiating beans

一个bean的定义本质上就是创建一个或多个对象的食谱。容器查看这个食谱并使用被bean定义封装的配置元数据来创建（或获取）实际的对象。  
A bean definition essentially is a recipe for creating one or more objects. The container looks at the recipe for a named bean when asked, and uses the configuration metadata encapsulated by that bean definition to create (or acquire) an actual object.

如果使用XML配置，在<bean/>元素的class属性中指定被实例化的对象的类型（或类）即可。class属性通常是必需的，它实际是BeanDefinition实例的Class属性（例外，参考使用实例的工厂方法实例化和7.7 Bean定义继承）。有两种使用Class属性的方式：  
If you use XML-based configuration metadata, you specify the type (or class) of object that is to be instantiated in the class attribute of the <bean/> element. This class attribute, which internally is a Class property on a BeanDefinition instance, is usually mandatory. (For exceptions, see the section called “Instantiation using an instance factory method” and Section 7.7, “Bean definition inheritance”.) You use the Class property in one of two ways:

* 典型地，容器直接通过反射调用bean的构造方法来创建一个bean，这个Java代码中使用new操作符是等价的。  
Typically, to specify the bean class to be constructed in the case where the container itself directly creates the bean by calling its constructor reflectively, somewhat equivalent to Java code using the new operator.  
* 容器通过调用工厂类中的static工厂方法来创建一个bean。通过调用static工厂方法返回的对象类型可能是同一个类，也可能是完全不同的另一个类。   
To specify the actual class containing the static factory method that will be invoked to create the object, in the less common case where the container invokes a static factory method on a class to create the bean. The object type returned from the invocation of the static factory method may be the same class or another class entirely.
  * 内部类名字。如果为一个static嵌套类配置bean定义，必须使用嵌套类的二进制名字。  
Inner class names.  If you want to configure a bean definition for a static nested class, you have to use the binary name of the nested class.
 
例如，如果在com.example包中有一个类叫Foo，并且Foo类中有一个static嵌套类叫Bar，那么bean定义中的class属性的值应该是`com.example.Foo$Bar`。   
For example, if you have a class called Foo in the com.example package, and this Foo class has a static nested class called Bar, the value of the 'class' attribute on a bean definition would be…​

`com.example.Foo$Bar`

注意使用`$`字符分割嵌套类和外部类。  
Notice the use of the $ character in the name to separate the nested class name from the outer class name.

__使用构造方法实例化__
Instantiation with a constructor  
用构造方法创建bean的方式，在Spring中所有正常的类都可以使用并兼容。也就是说，被开发的类不需要实现任何特定的接口或以特定的形式编码。仅仅指定bean类就足够了。但是，依靠什么样的类型来指定bean，你可能需要默认的空构造方法。  
When you create a bean by the constructor approach, all normal classes are usable by and compatible with Spring. That is, the class being developed does not need to implement any specific interfaces or to be coded in a specific fashion. Simply specifying the bean class should suffice. However, depending on what type of IoC you use for that specific bean, you may need a default (empty) constructor.


Spring的IoC窗口几乎可以管理所有的类，并不仅限于真的JavaBean。大部分用户更喜欢带有默认（无参）构造方法和适当setter/getter方法的JavaBean。你也可以拥有不同的非bean风格的类。例如，如果你需要使用不是JavaBean格式的连接池，Spring一样可以管理它。

The Spring IoC container can manage virtually any class you want it to manage; it is not limited to managing true JavaBeans. Most Spring users prefer actual JavaBeans with only a default (no-argument) constructor and appropriate setters and getters modeled after the properties in the container. You can also have more exotic non-bean-style classes in your container. If, for example, you need to use a legacy connection pool that absolutely does not adhere to the JavaBean specification, Spring can manage it as well.

在XML中，可以像下面这样指定bean类：  
With XML-based configuration metadata you can specify your bean class as follows:
```xml
<bean id="exampleBean" class="examples.ExampleBean"/>

<bean name="anotherExample" class="examples.ExampleBeanTwo"/>
```
关于如何为构造方法提供参数（如果需要的话）及在对象被构造之后为其设置属性，请参考注入依赖。

For details about the mechanism for supplying arguments to the constructor (if required) and setting object instance properties after the object is constructed, see Injecting Dependencies.

__使用静态工厂方法实例化__
Instantiation with a static factory method  
当使用静态工厂方法创建一个bean时，需要使用class属性指定那个包含静态工厂方法的类，并使用factory-method属性指定工厂方法的名字。应该可以调用这个方法（带参数的稍后讨论）并返回一个有效的对象，之后它就像用构造方法创建的对象一样对待。一种这样的bean定义的使用方法是在代码调用静态工厂。

When defining a bean that you create with a static factory method, you use the class attribute to specify the class containing the static factory method and an attribute named factory-method to specify the name of the factory method itself. You should be able to call this method (with optional arguments as described later) and return a live object, which subsequently is treated as if it had been created through a constructor. One use for such a bean definition is to call static factories in legacy code.

下面的bean定义指定了通过调用工厂方法创建这个bean。这个定义没有指定返回类型，仅仅指定了包含工厂方法的类。在这个例子中，createInstance()方法必须是静态的。  
The following bean definition specifies that the bean will be created by calling a factory-method. The definition does not specify the type (class) of the returned object, only the class containing the factory method. In this example, the createInstance() method must be a static method.
```xml
<bean id="clientService"
    class="examples.ClientService"
    factory-method="createInstance"/>
```
```java
public class ClientService {
    private static ClientService clientService = new ClientService();
    private ClientService() {}

    public static ClientService createInstance() {
        return clientService;
    }
}
```
关于如何为工厂方法提供参数及在对象从工厂返回之后为其设置属性，请参考依赖与配置详解。

For details about the mechanism for supplying (optional) arguments to the factory method and setting object instance properties after the object is returned from the factory, see Dependencies and configuration in detail.

__使用实例的工厂方法实例化（非静态）__
Instantiation using an instance factory method

与使用静态工厂方法实例化类似，使用实例的工厂方法实例化是调用一个已存在的bean的非静态方法来创建一个新的bean。为了使用这种机制，请把class属性置空，在factory-bean属性中指定被调用的用来创建对象的包含工厂方法的那个bean的名字，并factory-method属性中设置工厂方法的名字。

Similar to instantiation through a static factory method, instantiation with an instance factory method invokes a non-static method of an existing bean from the container to create a new bean. To use this mechanism, leave the class attribute empty, and in the factory-bean attribute, specify the name of a bean in the current (or parent/ancestor) container that contains the instance method that is to be invoked to create the object. Set the name of the factory method itself with the factory-method attribute.
```xml
<!-- the factory bean, which contains a method called createInstance() -->
<bean id="serviceLocator" class="examples.DefaultServiceLocator">
    <!-- inject any dependencies required by this locator bean -->
</bean>

<!-- the bean to be created via the factory bean -->
<bean id="clientService"
    factory-bean="serviceLocator"
    factory-method="createClientServiceInstance"/>
```

```java
public class DefaultServiceLocator {

    private static ClientService clientService = new ClientServiceImpl();

    public ClientService createClientServiceInstance() {
        return clientService;
    }
}
```
一个工厂类可以拥有多个工厂方法，如下：  
One factory class can also hold more than one factory method as shown here:
```xml
<bean id="serviceLocator" class="examples.DefaultServiceLocator">
    <!-- inject any dependencies required by this locator bean -->
</bean>

<bean id="clientService"
    factory-bean="serviceLocator"
    factory-method="createClientServiceInstance"/>

<bean id="accountService"
    factory-bean="serviceLocator"
    factory-method="createAccountServiceInstance"/>
```
```java
public class DefaultServiceLocator {

    private static ClientService clientService = new ClientServiceImpl();

    private static AccountService accountService = new AccountServiceImpl();

    public ClientService createClientServiceInstance() {
        return clientService;
    }

    public AccountService createAccountServiceInstance() {
        return accountService;
    }
}
```
工厂bean本身也可以通过依赖注入管理和配置，参考依赖与配置详解。  
This approach shows that the factory bean itself can be managed and configured through dependency injection (DI). See Dependencies and configuration in detail.

[Note]
* 在Spring的文档中，工厂bean引用Spring容器配置的通过实例或静态工厂方法创建对象的bean。相比之下，FactoryBean引用的是Spring特定的FactoryBean。  
In Spring documentation, factory bean refers to a bean that is configured in the Spring container that will create objects through an instance or static factory method. By contrast, FactoryBean (notice the capitalization) refers to a Spring-specific FactoryBean.

## 7.4 依赖
7.4 Dependencies

一个典型的企业级应用包含了不止一个对象（或者用Spring的说法叫bean）。即使是最简单的应用也需要几个对象一起工作以展现给最终用户看到的连贯应用。下一节介绍怎么样定义一系列独立的bean使它们相互合作实现一个共同的目标。

A typical enterprise application does not consist of a single object (or bean in the Spring parlance). Even the simplest application has a few objects that work together to present what the end-user sees as a coherent application. This next section explains how you go from defining a number of bean definitions that stand alone to a fully realized application where objects collaborate to achieve a goal.

### 7.4.1 依赖注入
7.4.1 Dependency Injection

依赖注入是一个对象定义其依赖的过程，它的依赖也就是与它一起合作的其它对象，这个过程只能通过构造方法参数、工厂方法参数、或者被构造或从工厂方法返回后通过setter方法设置其属性来实现。然后容器在创建这个bean时注入这些依赖。这个过程本质上是反过来的，由bean本身控制实例化，或者直接通过类的结构或Service定位器模式定位它自己的依赖，因此得其名曰控制反转。

Dependency injection (DI) is a process whereby objects define their dependencies, that is, the other objects they work with, only through constructor arguments, arguments to a factory method, or properties that are set on the object instance after it is constructed or returned from a factory method. The container then injects those dependencies when it creates the bean. This process is fundamentally the inverse, hence the name Inversion of Control (IoC), of the bean itself controlling the instantiation or location of its dependencies on its own by using direct construction of classes, or the Service Locator pattern.

使用依赖注入原则代码更干净，并且当对象提供了它们的依赖时解耦更有效。对象不查找它的依赖，且不知道依赖的位置或类。比如，类变得更容易测试，尤其是当依赖是基于接口或抽象基类时，这允许在单元测试时模拟实现。

Code is cleaner with the DI principle and decoupling is more effective when objects are provided with their dependencies. The object does not look up its dependencies, and does not know the location or class of the dependencies. As such, your classes become easier to test, in particular when the dependencies are on interfaces or abstract base classes, which allow for stub or mock implementations to be used in unit tests.

依赖注入有两种主要的方式，基于构造方法的依赖注入和基于setter方法的依赖注入。

DI exists in two major variants, Constructor-based dependency injection and Setter-based dependency injection.

__基于构造方法的依赖注入__
Constructor-based dependency injection

基于构造方法的依赖注入，由容器调用带有参数的构造方法来完成，每个参数代表一个依赖。调用带有特定参数的静态工厂方法创建bean几乎是一样的，这里把构造方法的参数与静态工厂方法的参数同等对待。下面的例子展示了一个只能通过构造方法注入依赖的类。注意，这个类并没有什么特殊的地方，它仅仅只是一个没有依赖容器的特定接口、基类或注解的POJO。

Constructor-based DI is accomplished by the container invoking a constructor with a number of arguments, each representing a dependency. Calling a static factory method with specific arguments to construct the bean is nearly equivalent, and this discussion treats arguments to a constructor and to a static factory method similarly. The following example shows a class that can only be dependency-injected with constructor injection. Notice that there is nothing special about this class, it is a POJO that has no dependencies on container specific interfaces, base classes or annotations.
```java
public class SimpleMovieLister {

    // the SimpleMovieLister has a dependency on a MovieFinder
    private MovieFinder movieFinder;

    // a constructor so that the Spring container can inject a MovieFinder
    public SimpleMovieLister(MovieFinder movieFinder) {
        this.movieFinder = movieFinder;
    }

    // business logic that actually uses the injected MovieFinder is omitted...
}
```
__构造方法参数的解决__
Constructor argument resolution

构造方法参数的解决匹配时使用参数的类型。如果没有潜在的歧义存在于bean定义的构造方法参数中，那么在bean实例化时bean定义中参数的顺序与构造方法中的顺序保持一致。例如，下面这个类：

Constructor argument resolution matching occurs using the argument’s type. If no potential ambiguity exists in the constructor arguments of a bean definition, then the order in which the constructor arguments are defined in a bean definition is the order in which those arguments are supplied to the appropriate constructor when the bean is being instantiated. Consider the following class:
```java
package x.y;

public class Foo {

    public Foo(Bar bar, Baz baz) {
        // ...
    }
}
```
没有潜在的歧义，假设Bar和Baz类没有继承关系。然后下面这样配置就可以了，不需要在<construct-arg/>中显式地指定参数的索引或类型。  
No potential ambiguity exists, assuming that Bar and Baz classes are not related by inheritance. Thus the following configuration works fine, and you do not need to specify the constructor argument indexes and/or types explicitly in the <constructor-arg/> element.
```xml
<beans>
    <bean id="foo" class="x.y.Foo">
        <constructor-arg ref="bar"/>
        <constructor-arg ref="baz"/>
    </bean>

    <bean id="bar" class="x.y.Bar"/>

    <bean id="baz" class="x.y.Baz"/>
</beans>
```
当另一个bean被引用时，类型是已知的并且匹配可能出现（像上例一样）。 
当使用简单类型时，比如<value>true</value>，Spring无法判断值的类型，所以没有类型就没办法匹配。例如，下面这个类：  
When another bean is referenced, the type is known, and matching can occur (as was the case with the preceding example). When a simple type is used, such as <value>true</value>, Spring cannot determine the type of the value, and so cannot match by type without help. Consider the following class:
```java
package examples;

public class ExampleBean {

    // Number of years to calculate the Ultimate Answer
    private int years;

    // The Answer to Life, the Universe, and Everything
    private String ultimateAnswer;

    public ExampleBean(int years, String ultimateAnswer) {
        this.years = years;
        this.ultimateAnswer = ultimateAnswer;
    }
}
```
在上述场景中，如果使用type属性指定了参数的类型则容器会使用类型匹配。例如： 
（译者注：如果不指定类型，spring分不清把7500000赋给year还是42赋给year，因为constructor-arg并不是按它们出现的顺序与参数匹配）  
In the preceding scenario, the container can use type matching with simple types if you explicitly specify the type of the constructor argument using the type attribute. For example:
```xml
<bean id="exampleBean" class="examples.ExampleBean">
    <constructor-arg type="int" value="7500000"/>
    <constructor-arg type="java.lang.String" value="42"/>
</bean>
```
使用index属性显式地指定参数的顺序。例如：  
Use the index attribute to specify explicitly the index of constructor arguments. For example:
```xml
<bean id="exampleBean" class="examples.ExampleBean">
    <constructor-arg index="0" value="7500000"/>
    <constructor-arg index="1" value="42"/>
</bean>
```
除了解决多个简单值的起义，使用索引还可以解决构造方法中存在多个相同类型的参数的问题。注意索引从0开始。

In addition to resolving the ambiguity of multiple simple values, specifying an index resolves ambiguity where a constructor has two arguments of the same type. Note that the index is 0 based.

也可以使用参数的名字来消除起义：  
You can also use the constructor parameter name for value disambiguation:
```xml
<bean id="exampleBean" class="examples.ExampleBean">
    <constructor-arg name="years" value="7500000"/>
    <constructor-arg name="ultimateAnswer" value="42"/>
</bean>
```
注意，为了使其可以立即使用，代码必须开启调试标记来编译，这样Spring才能从构造方法中找到参数的名字。如果没有开启调试标记（或不想）编译，可以使用JDK的注解@ConstructorProperties显式地指定参数的名字。类看起来不得不像下面这样：  
Keep in mind that to make this work out of the box your code must be compiled with the debug flag enabled so that Spring can look up the parameter name from the constructor. If you can’t compile your code with debug flag (or don’t want to) you can use @ConstructorProperties JDK annotation to explicitly name your constructor arguments. The sample class would then have to look as follows:
```java
package examples;

public class ExampleBean {

    // Fields omitted

    @ConstructorProperties({"years", "ultimateAnswer"})
    public ExampleBean(int years, String ultimateAnswer) {
        this.years = years;
        this.ultimateAnswer = ultimateAnswer;
    }
}
```
__基于setter方法的依赖注入__
Setter-based dependency injection

基于setter方法的依赖注入，由容器在调用无参构造方法或无参静态工厂方法之后调用setter方法来实例化bean。

Setter-based DI is accomplished by the container calling setter methods on your beans after invoking a no-argument constructor or no-argument static factory method to instantiate your bean.

下面的例子展示了一个只能通过纯净的setter方法注入依赖的类。这个类符合Java的约定，它是一个没有依赖容器的特定接口、基类或注解的POJO。  
The following example shows a class that can only be dependency-injected using pure setter injection. This class is conventional Java. It is a POJO that has no dependencies on container specific interfaces, base classes or annotations.
```java
public class SimpleMovieLister {

    // the SimpleMovieLister has a dependency on the MovieFinder
    private MovieFinder movieFinder;

    // a setter method so that the Spring container can inject a MovieFinder
    public void setMovieFinder(MovieFinder movieFinder) {
        this.movieFinder = movieFinder;
    }

    // business logic that actually uses the injected MovieFinder is omitted...
}
```
ApplicationContext对它管理的bean支持基于构造方法和基于setter方法的依赖注入，也支持在使用构造方法注入依赖之后再使用setter方法注入依赖。以BeanDefinition的形式配置依赖，可以与PropertyEditor实例一起把属性从一种形式转化为另一种形式。但是，大多数Spring用户不直接（编程式地）使用这些类，而是使用XML bean定义、注解的组件（例如，以@Component、@Controller注解的类）或基于Java的@Configuration类的@Bean方法。这些资源然后都被内部转化为了BeanDefinition的实例，并用于加载完整的Spring IoC容器的实例。

The ApplicationContext supports constructor-based and setter-based DI for the beans it manages. It also supports setter-based DI after some dependencies have already been injected through the constructor approach. You configure the dependencies in the form of a BeanDefinition, which you use in conjunction with PropertyEditor instances to convert properties from one format to another. However, most Spring users do not work with these classes directly (i.e., programmatically) but rather with XML bean definitions, annotated components (i.e., classes annotated with @Component, @Controller, etc.), or @Bean methods in Java-based @Configuration classes. These sources are then converted internally into instances of BeanDefinition and used to load an entire Spring IoC container instance.

* 基于构造方法或基于setter方法的依赖注入？ 
Constructor-based or setter-based DI?

因为可以混合使用基于构造方法和基于setter方法的依赖注入，所以使用构造方法注入强制依赖并使用setter方法或配置方法注入可选依赖是一个不错的原则。注意，在setter方法上使用@Required注解可以使属性变成必需的依赖。 

Since you can mix constructor-based and setter-based DI, it is a good rule of thumb to use constructors for mandatory dependencies and setter methods or configuration methods for optional dependencies. Note that use of the @Required annotation on a setter method can be used to make the property a required dependency.

Spring团队一般倡导使用构造方法注入，因为它会使应用程序的组件实现为不可变的对象，并保证必需的依赖不为null。另外，构造方法注入的组件总是以完全初始化的状态返回给客户端(调用)代码。注意，大量的构造方法参数是代码的坏味道，这意味着类可能有很多责任，应该被重构为合适的部分以实现关注点的分离。   
The Spring team generally advocates constructor injection as it enables one to implement application components as immutable objects and to ensure that required dependencies are not null. Furthermore constructor-injected components are always returned to client (calling) code in a fully initialized state. As a side note, a large number of constructor arguments is a bad code smell, implying that the class likely has too many responsibilities and should be refactored to better address proper separation of concerns.

setter方法应该仅仅只用于可选依赖，这些可选依赖应该在类中被赋值合理的默认值。否则，在使用这项依赖的任何地方都要做非null检查。setter方法注入的好处之一是可以使那个类的对象稍后重新配置或重新注入。使用JMX MBeans的管理是使用setter注入很好的案例。   
Setter injection should primarily only be used for optional dependencies that can be assigned reasonable default values within the class. Otherwise, not-null checks must be performed everywhere the code uses the dependency. One benefit of setter injection is that setter methods make objects of that class amenable to reconfiguration or re-injection later. Management through JMX MBeans is therefore a compelling use case for setter injection.

特定的类选择最合适的依赖注入方式。有时你并没有第三方类的源码，你就需要选择使用哪种方式。例如，如果一个第三方类没有暴露任何setter方法，那么只能选择构造方法注入了。  
Use the DI style that makes the most sense for a particular class. Sometimes, when dealing with third-party classes for which you do not have the source, the choice is made for you. For example, if a third-party class does not expose any setter methods, then constructor injection may be the only available form of DI.

__依赖注入的过程__
Dependency resolution process  
容器按如下方式处理依赖注入：  
The container performs bean dependency resolution as follows:

* ApplicationContext被创建并初始化描述所有bean的配置元数据。配置元数据可以是XML、Java代码或注解。  
The ApplicationContext is created and initialized with configuration metadata that describes all the beans. Configuration metadata can be specified via XML, Java code, or annotations.

* 对于每一个bean，它的依赖以属性、构造方法参数或静态工厂方法参数的形式表示。这些依赖在bean实际创建时被提供给它。  
For each bean, its dependencies are expressed in the form of properties, constructor arguments, or arguments to the static-factory method if you are using that instead of a normal constructor. These dependencies are provided to the bean, when the bean is actually created.

* 每一个属性或构造方法参数都是将被设置的值的实际定义，或容器中另一个bean的引用。  
Each property or constructor argument is an actual definition of the value to set, or a reference to another bean in the container.

* 每一个值类型的属性或构造方法参数都会从特定的形式转化为它的实际类型。默认地，Spring可以把字符串形式的值转化为所有的内置类型，比如int, long, String, boolean，等等。  

Each property or constructor argument which is a value is converted from its specified format to the actual type of that property or constructor argument. By default Spring can convert a value supplied in string format to all built-in types, such as int, long, String, boolean, etc.

Spring容器在创建时会验证每个bean的配置。但是，bean的属性本身直到bean实际被创建时才会设置。单例作用域的或被设置为预先实例化（默认）的bean会在容器创建时被创建。作用域的定义请参考7.5 bean的作用域。否则，bean只在它被请求的时候才会被创建。创建一个bean会潜在地引起一系列的bean被创建，因为bean的依赖及其依赖的依赖（等等）会被创建并赋值。注意，那些不匹配的依赖可能稍后创建，比如，受影响的bean的首次创建（译者注：这句可能翻译的不到位，有兴趣的自己翻译下，原文为 Note that resolution mismatches among those dependencies may show up late, i.e. on first creation of the affected bean）。

The Spring container validates the configuration of each bean as the container is created. However, the bean properties themselves are not set until the bean is actually created. Beans that are singleton-scoped and set to be pre-instantiated (the default) are created when the container is created. Scopes are defined in Section 7.5, “Bean scopes”. Otherwise, the bean is created only when it is requested. Creation of a bean potentially causes a graph of beans to be created, as the bean’s dependencies and its dependencies' dependencies (and so on) are created and assigned. Note that resolution mismatches among those dependencies may show up late, i.e. on first creation of the affected bean.

  * 循环依赖 
Circular dependencies  
如果你主要使用构造方法注入，很有可能创建一个无法解决的循环依赖场景。   
If you use predominantly constructor injection, it is possible to create an unresolvable circular dependency scenario.

例如，类A使用构造方法注入时需要类B的一个实例，类B使用构造方法注入时需要类A的一个实例。如果为类A和B配置bean互相注入，Spring IoC容器会在运行时检测出循环引用，并抛出异常BeanCurrentlyInCreationException。  
For example: Class A requires an instance of class B through constructor injection, and class B requires an instance of class A through constructor injection. If you configure beans for classes A and B to be injected into each other, the Spring IoC container detects this circular reference at runtime, and throws a BeanCurrentlyInCreationException.
 
一种解决方法是把一些类配置为使用setter方法注入而不是构造方法注入。作为替代方案，避免构造方法注入，而只使用setter方法注入。换句话说，尽管不推荐，但是可以通过setter方法注入配置循环依赖。   
One possible solution is to edit the source code of some classes to be configured by setters rather than constructors. Alternatively, avoid constructor injection and use setter injection only. In other words, although it is not recommended, you can configure circular dependencies with setter injection.

不像典型的案例（没有循环依赖），A和B之间的循环依赖使得一个bean在它本身完全初始化之前被注入了另一个bean（经典的先有鸡/先有蛋问题）。  
Unlike the typical case (with no circular dependencies), a circular dependency between bean A and bean B forces one of the beans to be injected into the other prior to being fully initialized itself (a classic chicken/egg scenario).

你通常可以相信Spring做正确的事。它会在容器加载时检测配置问题，比如引用不存在的bean和循环依赖。Spring尽可能晚地设置属性和解决依赖，在bean实际被创建之后。这意味着Spring正确加载了，之后如果在创建对象或它的依赖时出现问题那么Spring又会产生异常。例如，因为一个缺少或失效的属性导致bean抛出了异常。这潜在地会推迟一些配置问题的可视性，这就是为什么ApplicationContext的实现类默认预先实例化单例bean。在bean实际需要之前会花费一些时间和内存成本，因此在ApplicationContext创建时会发现配置问题，而不是之后。你也可以重写默认的行为让单例bean延迟初始化，而不是预先实例化。

You can generally trust Spring to do the right thing. It detects configuration problems, such as references to non-existent beans and circular dependencies, at container load-time. Spring sets properties and resolves dependencies as late as possible, when the bean is actually created. This means that a Spring container which has loaded correctly can later generate an exception when you request an object if there is a problem creating that object or one of its dependencies. For example, the bean throws an exception as a result of a missing or invalid property. This potentially delayed visibility of some configuration issues is why ApplicationContext implementations by default pre-instantiate singleton beans. At the cost of some upfront time and memory to create these beans before they are actually needed, you discover configuration issues when the ApplicationContext is created, not later. You can still override this default behavior so that singleton beans will lazy-initialize, rather than be pre-instantiated.

如果不存在循环依赖，当一个或多个合作的bean被注入依赖的bean时，每个合作的bean都将在注入依赖的bean之前被完全实例化。这意味着如果A依赖于B，那么在调用A的setter方法注入B之前会完全实例化B。换句话说，bean被实例化（如果不是预先实例化的单例），它的依赖被设置，然后相关的生命周期方法被调用（比如，配置的初始化方法或初始化bean的回调方法）。  
If no circular dependencies exist, when one or more collaborating beans are being injected into a dependent bean, each collaborating bean is totally configured prior to being injected into the dependent bean. This means that if bean A has a dependency on bean B, the Spring IoC container completely configures bean B prior to invoking the setter method on bean A. In other words, the bean is instantiated (if not a pre-instantiated singleton), its dependencies are set, and the relevant lifecycle methods (such as a configured init method or the InitializingBean callback method) are invoked.

__依赖注入的示例__
Examples of dependency injection  
下面的例子使用XML配置setter方法注入。XML的部分配置如下：  
The following example uses XML-based configuration metadata for setter-based DI. A small part of a Spring XML configuration file specifies some bean definitions:
```xml
<bean id="exampleBean" class="examples.ExampleBean">
    <!-- setter injection using the nested ref element -->
    <property name="beanOne">
        <ref bean="anotherExampleBean"/>
    </property>

    <!-- setter injection using the neater ref attribute -->
    <property name="beanTwo" ref="yetAnotherBean"/>
    <property name="integerProperty" value="1"/>
</bean>

<bean id="anotherExampleBean" class="examples.AnotherBean"/>
<bean id="yetAnotherBean" class="examples.YetAnotherBean"/>
```
```java
public class ExampleBean {

    private AnotherBean beanOne;

    private YetAnotherBean beanTwo;

    private int i;

    public void setBeanOne(AnotherBean beanOne) {
        this.beanOne = beanOne;
    }

    public void setBeanTwo(YetAnotherBean beanTwo) {
        this.beanTwo = beanTwo;
    }

    public void setIntegerProperty(int i) {
        this.i = i;
    }
}
```
上面的例子中，XML中setter方法与属性匹配，下面的例子使用构造方法注入：  
In the preceding example, setters are declared to match against the properties specified in the XML file. The following example uses constructor-based DI:
```xml
<bean id="exampleBean" class="examples.ExampleBean">
    <!-- constructor injection using the nested ref element -->
    <constructor-arg>
        <ref bean="anotherExampleBean"/>
    </constructor-arg>

    <!-- constructor injection using the neater ref attribute -->
    <constructor-arg ref="yetAnotherBean"/>

    <constructor-arg type="int" value="1"/>
</bean>

<bean id="anotherExampleBean" class="examples.AnotherBean"/>
<bean id="yetAnotherBean" class="examples.YetAnotherBean"/>
```
```java
public class ExampleBean {

    private AnotherBean beanOne;

    private YetAnotherBean beanTwo;

    private int i;

    public ExampleBean(
        AnotherBean anotherBean, YetAnotherBean yetAnotherBean, int i) {
        this.beanOne = anotherBean;
        this.beanTwo = yetAnotherBean;
        this.i = i;
    }
}
```
bean定义中指定的构造方法参数将用作ExampleBean的构造方法参数。  
The constructor arguments specified in the bean definition will be used as arguments to the constructor of the ExampleBean.

现在考虑使用另外一种方式，调用静态工厂方法代替构造方法返回这个对象的实例：  
Now consider a variant of this example, where instead of using a constructor, Spring is told to call a static factory method to return an instance of the object:
```xml
<bean id="exampleBean" class="examples.ExampleBean" factory-method="createInstance">
    <constructor-arg ref="anotherExampleBean"/>
    <constructor-arg ref="yetAnotherBean"/>
    <constructor-arg value="1"/>
</bean>

<bean id="anotherExampleBean" class="examples.AnotherBean"/>
<bean id="yetAnotherBean" class="examples.YetAnotherBean"/>
```
```java
public class ExampleBean {

    // a private constructor
    private ExampleBean(...) {
        ...
    }

    // a static factory method; the arguments to this method can be
    // considered the dependencies of the bean that is returned,
    // regardless of how those arguments are actually used.
    public static ExampleBean createInstance (
        AnotherBean anotherBean, YetAnotherBean yetAnotherBean, int i) {

        ExampleBean eb = new ExampleBean (...);
        // some other operations...
        return eb;
    }
}
```
通过<constructor-arg/>元素为静态工厂方法提供参数，与实际使用构造方法时一样。工厂方法返回的类型不必与包含工厂方法的类的类型一致，即使在这个例子中是一样的。实例（非静态）工厂方法完全一样的使用方法（除了使用factory-bean代替class属性），所以这里就不再赘述了。

Arguments to the static factory method are supplied via <constructor-arg/> elements, exactly the same as if a constructor had actually been used. The type of the class being returned by the factory method does not have to be of the same type as the class that contains the static factory method, although in this example it is. An instance (non-static) factory method would be used in an essentially identical fashion (aside from the use of the factory-bean attribute instead of the class attribute), so details will not be discussed here.

### 7.4.2 依赖与配置详解
7.4.2 Dependencies and configuration in detail

如前所述，可以定义bean的属性和构造方法的参数引用其它的bean（合作者），或设置值。在XML配置中，可以使用<property/>或<constructor-arg/>的属性达到这个目的。  
As mentioned in the previous section, you can define bean properties and constructor arguments as references to other managed beans (collaborators), or as values defined inline. Spring’s XML-based configuration metadata supports sub-element types within its <property/> and <constructor-arg/> elements for this purpose.

直接设置值（原始类型，String，等等）  
Straight values (primitives, Strings, and so on)

<property/>的value属性可以为属性或构造方法参数指定人类可读的字符串形式的值。Spring的转换器service会把这些值转换成属性或参数的实际类型。  
The value attribute of the <property/> element specifies a property or constructor argument as a human-readable string representation. Spring’s conversion service is used to convert these values from a String to the actual type of the property or argument.
```xml
<bean id="myDataSource" class="org.apache.commons.dbcp.BasicDataSource" destroy-method="close">
    <!-- results in a setDriverClassName(String) call -->
    <property name="driverClassName" value="com.mysql.jdbc.Driver"/>
    <property name="url" value="jdbc:mysql://localhost:3306/mydb"/>
    <property name="username" value="root"/>
    <property name="password" value="masterkaoli"/>
</bean>
```
下面的例子使用p命名空间可以更简洁的表示：  
The following example uses the p-namespace for even more succinct XML configuration.
```xml
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns:p="http://www.springframework.org/schema/p"
    xsi:schemaLocation="http://www.springframework.org/schema/beans
    http://www.springframework.org/schema/beans/spring-beans.xsd">

    <bean id="myDataSource" class="org.apache.commons.dbcp.BasicDataSource"
        destroy-method="close"
        p:driverClassName="com.mysql.jdbc.Driver"
        p:url="jdbc:mysql://localhost:3306/mydb"
        p:username="root"
        p:password="masterkaoli"/>

</beans>
```
上述XML更简洁了，但是错误只能在运行时被发现，而不是设计的时候，除非使用类似IntelliJ IDEA或Spring工具套件（STS）的开发工具，它们可以在创建bean定义时自动完成属性的拼写。强烈推荐使用这类IDE。

The preceding XML is more succinct; however, typos are discovered at runtime rather than design time, unless you use an IDE such as IntelliJ IDEA or the Spring Tool Suite (STS) that support automatic property completion when you create bean definitions. Such IDE assistance is highly recommended.

也可以像下面这样配置java.util.Properties：  
You can also configure a java.util.Properties instance as:
```xml
<bean id="mappings"
    class="org.springframework.beans.factory.config.PropertyPlaceholderConfigurer">

    <!-- typed as a java.util.Properties -->
    <property name="properties">
        <value>
            jdbc.driver.className=com.mysql.jdbc.Driver
            jdbc.url=jdbc:mysql://localhost:3306/mydb
        </value>
    </property>
</bean>
```
Spring容器会使用JavaBean的PropertyEditor机制把<value/>中的内容转换成java.util.Properties实例。这样更简短，Spring团队也更喜欢使用内置的<value/>元素代替value属性。

The Spring container converts the text inside the <value/> element into a java.util.Properties instance by using the JavaBeans PropertyEditor mechanism. This is a nice shortcut, and is one of a few places where the Spring team do favor the use of the nested <value/> element over the value attribute style.

__idref元素__
The idref element

idref元素是一种很简单的错误检查方式，可以把容器中另一个bean的id（字符串值-不是引用）传递给<constructor-arg/>或<property/>元素。  
The idref element is simply an error-proof way to pass the id (string value - not a reference) of another bean in the container to a <constructor-arg/> or <property/> element.
```xml
<bean id="theTargetBean" class="..."/>

<bean id="theClientBean" class="...">
    <property name="targetName">
        <idref bean="theTargetBean"/>
    </property>
</bean>
```
上面的片段在运行时与下面的片段完成一样：  
The above bean definition snippet is exactly equivalent (at runtime) to the following snippet:
```xml
<bean id="theTargetBean" class="..." />

<bean id="client" class="...">
    <property name="targetName" value="theTargetBean"/>
</bean>
```
第一种形式比第二种形式更好，因为使用idref标签时容器会在部署时验证引用的命名的bean是否实际存在。第二种形式不会对client bean的targetName属性的值执行任何验证。错误只能在client bean实际实例化时才能发现（可能导致致命的结果）。如果client bean是一个prototype类型的bean。那么错误很可能会在容器启动很久之后才能发现。

The first form is preferable to the second, because using the idref tag allows the container to validate at deployment time that the referenced, named bean actually exists. In the second variation, no validation is performed on the value that is passed to the targetName property of the client bean. Typos are only discovered (with most likely fatal results) when the client bean is actually instantiated. If the client bean is a prototype bean, this typo and the resulting exception may only be discovered long after the container is deployed.

[Note]
* idref元素的local属性在4.0版本的xsd中不再支持了，因为它不提供对普通bean的引用。在升级的4.0的schema时只要把已存在的idref local改成idref bean即可。（译者注：idref local只能引用同一个文件中的bean，所以不建议使用了，改成idref bean即可。）  
The local attribute on the idref element is no longer supported in the 4.0 beans xsd since it does not provide value over a regular bean reference anymore. Simply change your existing idref local references to idref bean when upgrading to the 4.0 schema.

A common place (at least in versions earlier than Spring 2.0) where the <idref/> element brings value is in the configuration of AOP interceptors in a ProxyFactoryBean bean definition. Using <idref/> elements when you specify the interceptor names prevents you from misspelling an interceptor id.

__引用其它bean（合作者）__
References to other beans (collaborators)  
ref是<constructor-arg/>或<property/>中的最后一个元素。可以使用它让一个bean的指定属性引用另一个bean（合作者）。被引用的bean就是这个bean的一个依赖，并且会在此属性设置前被初始化（如果此合作者是单例的，它可能已经被初始化了）。所有的引用最终都是对另一个对象的引用。作用域和检验由通过bean, local或parent属性指定的另一个对象的id或name决定。

The ref element is the final element inside a <constructor-arg/> or <property/> definition element. Here you set the value of the specified property of a bean to be a reference to another bean (a collaborator) managed by the container. The referenced bean is a dependency of the bean whose property will be set, and it is initialized on demand as needed before the property is set. (If the collaborator is a singleton bean, it may be initialized already by the container.) All references are ultimately a reference to another object. Scoping and validation depend on whether you specify the id/name of the other object through the bean, local, or parent attributes.

通过bean的<ref/>标签指定目标bean是最常用的形式，并且可以引用同一个或父容器中的任何bean，不论是否在同一个XML文件中。引用的值可能是目标bean的id属性，或name属性中的一个（译者注：因为name属性可以指定多个name）。

Specifying the target bean through the bean attribute of the <ref/> tag is the most general form, and allows creation of a reference to any bean in the same container or parent container, regardless of whether it is in the same XML file. The value of the bean attribute may be the same as the id attribute of the target bean, or as one of the values in the name attribute of the target bean.

`<ref bean="someBean"/>`

通过parent属性可以指定对当前容器的父容器中的bean的引用。parent属性的值可能是目标bean的id属性或name属性中的一个，而且目标bean必须在当前容器的父容器中。这种引用形式主要出现在有容器继承并且想把父容器中存在的bean包装成同样名字的代理用于子容器的时候。

Specifying the target bean through the parent attribute creates a reference to a bean that is in a parent container of the current container. The value of the parent attribute may be the same as either the id attribute of the target bean, or one of the values in the name attribute of the target bean, and the target bean must be in a parent container of the current one. You use this bean reference variant mainly when you have a hierarchy of containers and you want to wrap an existing bean in a parent container with a proxy that will have the same name as the parent bean.
```xml
<!-- in the parent context -->
<bean id="accountService" class="com.foo.SimpleAccountService">
    <!-- insert dependencies as required as here -->
</bean>
<!-- in the child (descendant) context -->
<bean id="accountService" <!-- bean name is the same as the parent bean -->
    class="org.springframework.aop.framework.ProxyFactoryBean">
    <property name="target">
        <ref parent="accountService"/> <!-- notice how we refer to the parent bean -->
    </property>
    <!-- insert other configuration and dependencies as required here -->
</bean>
```
[Note]
* ref元素的local属性在4.0版本的xsd中不再支持了，因为它不提供对普通bean的引用。在升级的4.0的schema时只要把已存在的ref local改成ref bean即可。（译者注：ref local只能引用同一个文件中的bean，所以不建议使用了，改成ref bean即可。）  
The local attribute on the ref element is no longer supported in the 4.0 beans xsd since it does not provide value over a regular bean reference anymore. Simply change your existing ref local references to ref bean when upgrading to the 4.0 schema.

__内部bean__
Inner beans  
在<property/>或<constructor-arg/>元素内部定义的bean就是所谓的内部bean。  
A <bean/> element inside the <property/> or <constructor-arg/> elements defines a so-called inner bean.
```xml
<bean id="outer" class="...">
    <!-- instead of using a reference to a target bean, simply define the target bean inline -->
    <property name="target">
        <bean class="com.example.Person"> <!-- this is the inner bean -->
            <property name="name" value="Fiona Apple"/>
            <property name="age" value="25"/>
        </bean>
    </property>
</bean>
```
内部bean不需要定义id或name，即使指定了，容器也不会使用它作为标识符。容器在创建内部bean时也会忽略其作用域标志，内部bean总是匿名的且总是随着外部bean一起创建。不可能把内部bean注入到除封闭bean以外的合作bean，也不可能单独访问它们。

An inner bean definition does not require a defined id or name; if specified, the container does not use such a value as an identifier. The container also ignores the scope flag on creation: Inner beans are always anonymous and they are always created with the outer bean. It is not possible to inject inner beans into collaborating beans other than into the enclosing bean or to access them independently.

有一种边界情况，可以从自定义作用域中接收销毁方法的回调，例如，对于包含在一个单例bean中的request作用域的内部bean，它的创建依赖于包含它的bean，但是销毁方法的回调允许它参与到request作用域的生命周期中。这并不是一种常见的情况，内部bean一般共享包含它的bean的作用域。  
As a corner case, it is possible to receive destruction callbacks from a custom scope, e.g. for a request-scoped inner bean contained within a singleton bean: The creation of the inner bean instance will be tied to its containing bean, but destruction callbacks allow it to participate in the request scope’s lifecycle. This is not a common scenario; inner beans typically simply share their containing bean’s scope.

__集合__
Collections

在<list/>, <set/>, <map/>和<props/>元素中，可以分别设置Java集合类型List, Set, Map和Properties的属性和参数。  
In the <list/>, <set/>, <map/>, and <props/> elements, you set the properties and arguments of the Java Collection types List, Set, Map, and Properties, respectively.
```xml
<bean id="moreComplexObject" class="example.ComplexObject">
    <!-- results in a setAdminEmails(java.util.Properties) call -->
    <property name="adminEmails">
        <props>
            <prop key="administrator">administrator@example.org</prop>
            <prop key="support">support@example.org</prop>
            <prop key="development">development@example.org</prop>
        </props>
    </property>
    <!-- results in a setSomeList(java.util.List) call -->
    <property name="someList">
        <list>
            <value>a list element followed by a reference</value>
            <ref bean="myDataSource" />
        </list>
    </property>
    <!-- results in a setSomeMap(java.util.Map) call -->
    <property name="someMap">
        <map>
            <entry key="an entry" value="just some string"/>
            <entry key ="a ref" value-ref="myDataSource"/>
        </map>
    </property>
    <!-- results in a setSomeSet(java.util.Set) call -->
    <property name="someSet">
        <set>
            <value>just some string</value>
            <ref bean="myDataSource" />
        </set>
    </property>
</bean>
```
map的键值或set的值也可以是以下任何元素：  
The value of a map key or value, or a set value, can also again be any of the following elements:

bean | ref | idref | list | set | map | props | value | null

__集合的合并__
Collection merging

Spring容器同样支持集合的合并。开发者可以定义一个父类型的<list/>, <set/>, <map/>或<props/>元素，然后让子类型继承它，也可以重写父集合里的值。也就是说，子集合的值是父集合与子集合值合并的结果，且子集合的元素重写了父集合中同样的元素。

The Spring container also supports the merging of collections. An application developer can define a parent-style <list/>, <map/>, <set/> or <props/> element, and have child-style <list/>, <map/>, <set/> or <props/> elements inherit and override values from the parent collection. That is, the child collection’s values are the result of merging the elements of the parent and child collections, with the child’s collection elements overriding values specified in the parent collection.

这节主要讨论父子集合的合并机制，读者如果对父子bean的定义不太熟悉，可以先读相关章节再继续。

This section on merging discusses the parent-child bean mechanism. Readers unfamiliar with parent and child bean definitions may wish to read the relevant section before continuing.

下面的例子展示了集合的合并：  
The following example demonstrates collection merging:
```xml
<beans>
    <bean id="parent" abstract="true" class="example.ComplexObject">
        <property name="adminEmails">
            <props>
                <prop key="administrator">administrator@example.com</prop>
                <prop key="support">support@example.com</prop>
            </props>
        </property>
    </bean>
    <bean id="child" parent="parent">
        <property name="adminEmails">
            <!-- the merge is specified on the child collection definition -->
            <props merge="true">
                <prop key="sales">sales@example.com</prop>
                <prop key="support">support@example.co.uk</prop>
            </props>
        </property>
    </bean>
<beans>
```
注意子bean的adminEmails属性上的props元素的merge=true。当child被实例化时，它拥有一个adminEmails Properties集合，这个集合包含了父子两个集合adminEmails合并的结果。

Notice the use of the merge=true attribute on the <props/> element of the adminEmails property of the child bean definition. When the child bean is resolved and instantiated by the container, the resulting instance has an adminEmails Properties collection that contains the result of the merging of the child’s adminEmails collection with the parent’s adminEmails collection.
```
administrator=administrator@example.com
sales=sales@example.com
support=support@example.co.uk
```
子集合Properties的值集继承了所有父集合的元素，并且子集合中的support值重写了父集合的值。

The child Properties collection’s value set inherits all property elements from the parent <props/>, and the child’s value for the support value overrides the value in the parent collection.

<list/>, <map/>和 <set/>集合的合并行为也是类似的。在<list/>元素合并的时候，因为List集合维护的是有序的值，所以父集合的值在子集合值的前面。在Map, Set和Properties中的值则不存在顺序。没有顺序的集合类型更有效，因此容器内部更倾向于使用Map, Set和Properties。

This merging behavior applies similarly to the <list/>, <map/>, and <set/> collection types. In the specific case of the <list/> element, the semantics associated with the List collection type, that is, the notion of an ordered collection of values, is maintained; the parent’s values precede all of the child list’s values. In the case of the Map, Set, and Properties collection types, no ordering exists. Hence no ordering semantics are in effect for the collection types that underlie the associated Map, Set, and Properties implementation types that the container uses internally.

__集合合并的局限性__
Limitations of collection merging

不能合并不同的集合类型（比如，Map和List），如果试图这么做将会抛出异常。merge属性必须指定在子集合的定义上，在父集合上指定merge属性将是多余的且不会合并集合。

You cannot merge different collection types (such as a Map and a List), and if you do attempt to do so an appropriate Exception is thrown. The merge attribute must be specified on the lower, inherited, child definition; specifying the merge attribute on a parent collection definition is redundant and will not result in the desired merging.

__强类型集合__
Strongly-typed collection

Java5引入了泛型类型，所以可以使用强类型的集合。也就是说，例如，可以声明一个只能包含String元素的集合类型。如果使用Spring把强类型的集合注入到一个bean，可以利用Spring的类型转换以便元素在被添加到集合之前转换成合适的类型。  
With the introduction of generic types in Java 5, you can use strongly typed collections. That is, it is possible to declare a Collection type such that it can only contain String elements (for example). If you are using Spring to dependency-inject a strongly-typed Collection into a bean, you can take advantage of Spring’s type-conversion support such that the elements of your strongly-typed Collection instances are converted to the appropriate type prior to being added to the Collection.
```java
public class Foo {

    private Map<String, Float> accounts;

    public void setAccounts(Map<String, Float> accounts) {
        this.accounts = accounts;
    }
}
```
```xml
<beans>
    <bean id="foo" class="x.y.Foo">
        <property name="accounts">
            <map>
                <entry key="one" value="9.99"/>
                <entry key="two" value="2.75"/>
                <entry key="six" value="3.99"/>
            </map>
        </property>
    </bean>
</beans>
```
当foo的accounts属性准备注入的时候，会通过反射获得强类型Map<String, Float>的泛型信息。然后Spring的类型转换机制识别到value的类型为Float，并把String类型的值9.99， 2.75和3.99转换成实际的Float类型。

When the accounts property of the foo bean is prepared for injection, the generics information about the element type of the strongly-typed Map<String, Float> is available by reflection. Thus Spring’s type conversion infrastructure recognizes the various value elements as being of type Float, and the string values 9.99, 2.75, and 3.99 are converted into an actual Float type.

__null和空字符串值__
Null and empty string values

Spring把对属性的空参数作为空字符串。下面的XML片段把email属性的值设置成了空字符串值（”“）。  
Spring treats empty arguments for properties and the like as empty Strings. The following XML-based configuration metadata snippet sets the email property to the empty String value ("").
```xml
<bean class="ExampleBean">
    <property name="email" value=""/>
</bean>
```
上面的例子与下面的Java代码是等价的：
The preceding example is equivalent to the following Java code:

`exampleBean.setEmail("");`
<null/>元素处理null值。例如：
The <null/> element handles null values. For example:
```xml
<bean class="ExampleBean">
    <property name="email">
        <null/>
    </property>
</bean>
```
上面的配置与下面的Java代码等价：
The above configuration is equivalent to the following Java code:

`exampleBean.setEmail(null);`

__使用p命名空间简写XML__
XML shortcut with the p-namespace  
p命名空间使你可以使用bean元素的属性，而不是内置的<property/>元素，来描述属性值和合作的bean。

The p-namespace enables you to use the bean element’s attributes, instead of nested <property/> elements, to describe your property values and/or collaborating beans.

Spring支持使用命名空间扩展配置，这是基于XML的schema定义的。本章讨论的bean的配置形式是定义在XML schema文档中的。然而，p命名空间不是定义在XSD文件中的，它存在于Spring的核心包中。

Spring supports extensible configuration formats with namespaces, which are based on an XML Schema definition. The beans configuration format discussed in this chapter is defined in an XML Schema document. However, the p-namespace is not defined in an XSD file and exists only in the core of Spring.

下面例子中的两段XML是一样的结果，第一段使用标准的XML形式，第二段使用p命名空间形式。  
The following example shows two XML snippets that resolve to the same result: The first uses standard XML format and the second uses the p-namespace.
```xml
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns:p="http://www.springframework.org/schema/p"
    xsi:schemaLocation="http://www.springframework.org/schema/beans
        http://www.springframework.org/schema/beans/spring-beans.xsd">

    <bean name="classic" class="com.example.ExampleBean">
        <property name="email" value="foo@bar.com"/>
    </bean>

    <bean name="p-namespace" class="com.example.ExampleBean"
        p:email="foo@bar.com"/>
</beans>
```
这个例子展示了p命名空间中一个叫做email的属性。这会告诉Spring包含了一个属性声明。如前所述，p命名空间没有schema定义，所以可以把xml的属性名设置成java的属性名。

The example shows an attribute in the p-namespace called email in the bean definition. This tells Spring to include a property declaration. As previously mentioned, the p-namespace does not have a schema definition, so you can set the name of the attribute to the property name.

下面的例子包含两个引用了其它对象的bean定义：  
This next example includes two more bean definitions that both have a reference to another bean:
```xml
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns:p="http://www.springframework.org/schema/p"
    xsi:schemaLocation="http://www.springframework.org/schema/beans
        http://www.springframework.org/schema/beans/spring-beans.xsd">

    <bean name="john-classic" class="com.example.Person">
        <property name="name" value="John Doe"/>
        <property name="spouse" ref="jane"/>
    </bean>

    <bean name="john-modern"
        class="com.example.Person"
        p:name="John Doe"
        p:spouse-ref="jane"/>

    <bean name="jane" class="com.example.Person">
        <property name="name" value="Jane Doe"/>
    </bean>
</beans>
```
如你所见，此例不仅使用p命名空间定义属性值，还使用特定的形式声明了属性的引用。第一个bean定义使用<property name=”spouse” ref=”jane”/>创建了从john到jane的引用，第二个bean定义使用p:spouse-ref=”jane”作为属性做了完全相同的事。这个例子中spouse是属性名，凭借-ref表明这不是直接的值而是对另一个对象的引用。

As you can see, this example includes not only a property value using the p-namespace, but also uses a special format to declare property references. Whereas the first bean definition uses <property name="spouse" ref="jane"/> to create a reference from bean john to bean jane, the second bean definition uses p:spouse-ref="jane" as an attribute to do the exact same thing. In this case spouse is the property name, whereas the -ref part indicates that this is not a straight value but rather a reference to another bean.

[Note]
* p命名空间并没有标准的XML形式灵活。例如，属性如果以Ref结尾则会与属性的引用冲突，标准的XML形式就不会有这样的问题。我们推荐仔细地选择自己的方式，并和团队成员交流，以避免XML文档中同时出现三种不同的方式。  
The p-namespace is not as flexible as the standard XML format. For example, the format for declaring property references clashes with properties that end in Ref, whereas the standard XML format does not. We recommend that you choose your approach carefully and communicate this to your team members, to avoid producing XML documents that use all three approaches at the same time.

__使用c命名空间简写XML__
XML shortcut with the c-namespace  
与使用p命名空间简写XML类似，c命名空间是Spring3.1新引入的，允许使用内联属性配置构造方法的参数，而不用嵌套constructor-arg元素。

Similar to the the section called “XML shortcut with the p-namespace”, the c-namespace, newly introduced in Spring 3.1, allows usage of inlined attributes for configuring the constructor arguments rather then nested constructor-arg elements.

让我们一起回顾下基于构造方法的依赖注入这节，使用c: 命名空间如下：  
Let’s review the examples from the section called “Constructor-based dependency injection” with the c: namespace:
```xml
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns:c="http://www.springframework.org/schema/c"
    xsi:schemaLocation="http://www.springframework.org/schema/beans
        http://www.springframework.org/schema/beans/spring-beans.xsd">

    <bean id="bar" class="x.y.Bar"/>
    <bean id="baz" class="x.y.Baz"/>

    <!-- traditional declaration -->
    <bean id="foo" class="x.y.Foo">
        <constructor-arg ref="bar"/>
        <constructor-arg ref="baz"/>
        <constructor-arg value="foo@bar.com"/>
    </bean>

    <!-- c-namespace declaration -->
    <bean id="foo" class="x.y.Foo" c:bar-ref="bar" c:baz-ref="baz" c:email="foo@bar.com"/>

</beans>
```
c: 命名空间与p: 命名空间使用一样的转换（后面的-ref用于bean引用），通过名字设置构造方法的参数。同样地，c命名空间也没有定义在XSD文件中（但是存在于Spring核心包中）。

The c: namespace uses the same conventions as the p: one (trailing -ref for bean references) for setting the constructor arguments by their names. And just as well, it needs to be declared even though it is not defined in an XSD schema (but it exists inside the Spring core).

对于极少数的情况，不能获得构造方法参数名时（通常不使用调试信息编译字节码），可以退而求其次使用参数的索引。  
For the rare cases where the constructor argument names are not available (usually if the bytecode was compiled without debugging information), one can use fallback to the argument indexes:
```xml
<!-- c-namespace index declaration -->
<bean id="foo" class="x.y.Foo" c:_0-ref="bar" c:_1-ref="baz"/>
```
[Note]
* 由于XML语法，索引符号需要以 _ 开头作为XML属性名，而不能以数字开头（即使在一些IDE中允许）。  
Due to the XML grammar, the index notation requires the presence of the leading _ as XML attribute names cannot start with a number (even though some IDE allow it).

经过实践，构造方法的解决机制在匹配参数方面很有效率，所以除非真的需要，我们推荐在所有配置的地方都使用名字符号。
In practice, the constructor resolution mechanism is quite efficient in matching arguments so unless one really needs to, we recommend using the name notation through-out your configuration.

__合成属性名__
Compound property names

可以使用合成或嵌套的属性名设置bean的属性，只要路径中除了最后的属性值的所有的组件都不为null，考虑使用如下bean定义：

You can use compound or nested property names when you set bean properties, as long as all components of the path except the final property name are not null. Consider the following bean definition.
```xml
<bean id="foo" class="foo.Bar">
    <property name="fred.bob.sammy" value="123" />
</bean>
```
foo有一个属性叫fred，fred有一个属性叫bob，bob有一个属性叫sammy，并且最后的sammy属性的值被设置为123。其中，foo的fred属性和fred的bob属性在bean构造后必须不为null，否则将会抛出空指针异常。  
The foo bean has a fred property, which has a bob property, which has a sammy property, and that final sammy property is being set to the value 123. In order for this to work, the fred property of foo, and the bob property of fred must not be null after the bean is constructed, or a NullPointerException is thrown.

### 7.4.3 使用depends-on
7.4.3 Using depends-on

如果一个bean是另一个bean的依赖，那通常意味着这个bean被设置为另一个bean的属性。典型地，在XML配置中使用<ref/>元素来完成这件事。但是，有时bean之间的依赖并不是直接的，例如，一个静态的初始化器需要被触发，比如在数据库驱动注册时。depends-on属性可以明确地强制一个或多个bean在使用它（们）的bean初始化之前被初始化。
下面的例子使用depends-on属性表示对一个单例bean的依赖：

If a bean is a dependency of another that usually means that one bean is set as a property of another. Typically you accomplish this with the <ref/> element in XML-based configuration metadata. However, sometimes dependencies between beans are less direct; for example, a static initializer in a class needs to be triggered, such as database driver registration. The depends-on attribute can explicitly force one or more beans to be initialized before the bean using this element is initialized. The following example uses the depends-on attribute to express a dependency on a single bean:
```xml
<bean id="beanOne" class="ExampleBean" depends-on="manager"/>
<bean id="manager" class="ManagerBean" />
```
要表示对多个bean的依赖，可以为depends-on属性的值提供多个名字，使用逗号，空格或分号分割：  
To express a dependency on multiple beans, supply a list of bean names as the value of the depends-on attribute, with commas, whitespace and semicolons, used as valid delimiters:
```xml
<bean id="beanOne" class="ExampleBean" depends-on="manager,accountDao">
    <property name="manager" ref="manager" />
</bean>

<bean id="manager" class="ManagerBean" />
<bean id="accountDao" class="x.y.jdbc.JdbcAccountDao" />
```
> depends-on属性能够同时指定初始化时依赖和通信销毁时依赖，这只能运用在单例bean的情况中。依赖的bean在给定的bean销毁之前被销毁。因此，depends-on也能够控制关闭的顺序。（译者注，初始化的时候依赖的对象先初始化才能注入，销毁时需要依赖的对象先销毁才能解绑）  
> The depends-on attribute in the bean definition can specify both an initialization time dependency and, in the case of singleton beans only, a corresponding destroy time dependency. Dependent beans that define a depends-on relationship with a given bean are destroyed first, prior to the given bean itself being destroyed. Thus depends-on can also control shutdown order.

### 7.4.4 延迟初始化的bean
7.4.4 Lazy-initialized beans  
默认地，ApplicationContext的实现创建和配置所有单例bean作为初始化过程的一部分。通常这种预初始化是令人满意的，因为配置或环境中的错误可以立即被发现，相反则可能需要数小时甚至数天才能发现错误。如果不需要预初始化，可以把bean定义为延迟初始化来阻止预初始化。延迟初始化的bean会告诉IoC容器在第一次请求到这个bean时才初始化，而不是启动的时候。  
By default, ApplicationContext implementations eagerly create and configure all singleton beans as part of the initialization process. Generally, this pre-instantiation is desirable, because errors in the configuration or surrounding environment are discovered immediately, as opposed to hours or even days later. When this behavior is not desirable, you can prevent pre-instantiation of a singleton bean by marking the bean definition as lazy-initialized. A lazy-initialized bean tells the IoC container to create a bean instance when it is first requested, rather than at startup.

在XML中，这种行为是通过<bean/>元素的lazy-init属性控制的，例如：  
In XML, this behavior is controlled by the lazy-init attribute on the <bean/> element; for example:
```xml
<bean id="lazy" class="com.foo.ExpensiveToCreateBean" lazy-init="true"/>
<bean name="not.lazy" class="com.foo.AnotherBean"/>
```
当ApplicationContext使用上面的配置启动时时，名为lazy的bean并不急着预初始化，而not.lazy则会预初始化。

When the preceding configuration is consumed by an ApplicationContext, the bean named lazy is not eagerly pre-instantiated when the ApplicationContext is starting up, whereas the not.lazy bean is eagerly pre-instantiated.

但是，当一个延迟初始化的bean是另一个非延迟初始化的单例bean的依赖时，ApplicationContext会在启动时创建这个延迟初始化的bean，因为它必须用来满足那个单例的依赖。这个延迟初始化的bean被注入到非延迟初始化的单例bean中。

However, when a lazy-initialized bean is a dependency of a singleton bean that is not lazy-initialized, the ApplicationContext creates the lazy-initialized bean at startup, because it must satisfy the singleton’s dependencies. The lazy-initialized bean is injected into a singleton bean elsewhere that is not lazy-initialized.

也可以在<beans/>元素上设置其default-lazy-init属性以便在容器级别控制延迟初始化。  
You can also control lazy-initialization at the container level by using the default-lazy-init attribute on the <beans/> element; for example:
```xml
<beans default-lazy-init="true">
    <!-- no beans will be pre-instantiated... -->
</beans>
```
### 7.4.5 自动装配合作者
7.4.5 Autowiring collaborators  
Spring容器可以相互合作的bean间自动装配其关系。你可让让Spring通过检查ApplicationContext的内容自动为你解决bean之间的依赖。自动装配有以下优点：  
The Spring container can autowire relationships between collaborating beans. You can allow Spring to resolve collaborators (other beans) automatically for your bean by inspecting the contents of the ApplicationContext. Autowiring has the following advantages:

* 自动装配将极大地减少指定属性或构造方法参数的需要（在这点上，其它机制比如本章其它小节讲解的bean模板也是有价值的）。  
Autowiring can significantly reduce the need to specify properties or constructor arguments. (Other mechanisms such as a bean template discussed elsewhere in this chapter are also valuable in this regard.)
* 自动装配可以更新配置当你的对象进化时。例如，如果你需要为一个类添加一个依赖，那么不需要修改配置就可以自动满足。因此，自动装配在开发期间非常有用，但不否定在代码库变得更稳定的时候切换到显式的装配。
Autowiring can update a configuration as your objects evolve. For example, if you need to add a dependency to a class, that dependency can be satisfied automatically without you needing to modify the configuration. Thus autowiring can be especially useful during development, without negating the option of switching to explicit wiring when the code base becomes more stable.

使用XML配置时，可以为带有autowire属性的bean定义指定自动装配的模式。自动装配的功能有四种模式。可以为每个自动装配的bean指定一种模式。   
When using XML-based configuration metadata [2], you specify autowire mode for a bean definition with the autowire attribute of the <bean/> element. The autowiring functionality has four modes. You specify autowiring per bean and thus can choose which ones to autowire.

__表7.2 自动装配的模式__
Table 7.2. Autowiring modes

Mode |	Explanation
-- | --
no | 默认地没有自动自动装配。bean的引用必须通过ref元素定义。对于大型部署，不推荐更改默认设置，因为显式地指定合作者能够更好地控制且更清晰。在一定程度上，这也记录了系统的结构。
no | (Default) No autowiring. Bean references must be defined via a ref element. Changing the default setting is not recommended for larger deployments, because specifying collaborators explicitly gives greater control and clarity. To some extent, it documents the structure of a system.
byName | 按属性名称自动装配。Spring为待装配的属性寻找同名的bean。例如，如果一个bean被设置为按属性名称自动装配，且它包含一个属性叫master（亦即，它有setMaster(…)方法），Spring会找到一个名叫master的bean并把它设置到这个属性中。
byName | Autowiring by property name. Spring looks for a bean with the same name as the property that needs to be autowired. For example, if a bean definition is set to autowire by name, and it contains a master property (that is, it has a setMaster(..) method), Spring looks for a bean definition named master, and uses it to set the property.
byType | 按属性的类型自动装配，如果这个属性的类型在容器中只存在一个bean。如果多于一个，则会抛出异常，这意味着不能为那个bean使用按类型装配。如果一个都没有，则什么事都不会发生，这个属性不会被装配。
byType | Allows a property to be autowired if exactly one bean of the property type exists in the container. If more than one exists, a fatal exception is thrown, which indicates that you may not use byType autowiring for that bean. If there are no matching beans, nothing happens; the property is not set.
constructor | 与按类型装配类似，只不过用于构造方法的参数。如果这个构造方法的参数类型在容器中不存在明确的一个bean，将会抛出异常。
constructor | Analogous to byType, but applies to constructor arguments. If there is not exactly one bean of the constructor argument type in the container, a fatal error is raised.

使用按类型装配或构造方法自动装配模式，可以装配数组和集合类型。在这个情况下，容器中所有匹配期望类型的候选者都将被提供用来满足此依赖。你可以自动装配强类型的Map如果其键的类型是String。自动装配Map的值将包含所有匹配期望类型的bean实例，并且Map的键将包含对应的bean的名字。  
With byType or constructor autowiring mode, you can wire arrays and typed-collections. In such cases all autowire candidates within the container that match the expected type are provided to satisfy the dependency. You can autowire strongly-typed Maps if the expected key type is String. An autowired Maps values will consist of all bean instances that match the expected type, and the Maps keys will contain the corresponding bean names.

可以联合使用自动装配行为和依赖检查，其中依赖检查会在自动装配完成后执行。  
You can combine autowire behavior with dependency checking, which is performed after autowiring completes.

__自动装配的局限性和缺点__
Limitations and disadvantages of autowiring  
如果一个项目一直使用自动装配，它会运行得很好。如果只是用在一两个bean上，则会出现混乱。

Autowiring works best when it is used consistently across a project. If autowiring is not used in general, it might be confusing to developers to use it to wire only one or two bean definitions.

自动装配有如下局限性和缺点：  
Consider the limitations and disadvantages of autowiring:

* 在property和constructor-arg上显式设置的依赖总是覆盖自动装配。而且，不能自动装配所谓的简单属性，如基本类型、String和Classes（也包括简单属性的数组）。这是设计层面的局限性。  
Explicit dependencies in property and constructor-arg settings always override autowiring. You cannot autowire so-called simple properties such as primitives, Strings, and Classes (and arrays of such simple properties). This limitation is by-design.

* 自动装配没有显式装配精确。如上表所述，尽管Spring很小心地避免了模棱两可的猜测，但其管理的对象之间的关系并不会被明确地记录。  
Autowiring is less exact than explicit wiring. Although, as noted in the above table, Spring is careful to avoid guessing in case of ambiguity that might have unexpected results, the relationships between your Spring-managed objects are no longer documented explicitly.

* 从Spring容器生成文档的工具可能找不到装配信息。  
Wiring information may not be available to tools that may generate documentation from a Spring container.

* 容器中的多个bean定义可能会匹配setter方法或构造方法参数指定的类型。对于数组、集合或Map，这未必是个问题。但是，对于那些需要单个值的依赖，这种歧义并不能随意解决。如果没有各自不同的bean定义，将会抛出异常。  
Multiple bean definitions within the container may match the type specified by the setter method or constructor argument to be autowired. For arrays, collections, or Maps, this is not necessarily a problem. However for dependencies that expect a single value, this ambiguity is not arbitrarily resolved. If no unique bean definition is available, an exception is thrown.

在上述场景中，有如下几种选择：  
In the latter scenario, you have several options:

* 放弃自动装配，改为显式地装配。  
Abandon autowiring in favor of explicit wiring.
* 设置bean的autowire-candidate属性为false以避免自动装配，这会在下一章节讲解。  
Avoid autowiring for a bean definition by setting its autowire-candidate attributes to false as described in the next section.
* 设置<bean/>标签的primary属性为true，从而为其指定一个单独的bean作为主要的候选者。  
Designate a single bean definition as the primary candidate by setting the primary attribute of its <bean/> element to true.
* 使用基于注解的配置实现更细粒度的控制，参考7.9 基于注解的容器配置。  
Implement the more fine-grained control available with annotation-based configuration, as described in Section 7.9, “Annotation-based container configuration”.

__避免bean自动装配__
Excluding a bean from autowiring  

在单个bean上，可以避免自动装配。在xml形式中，设置<bean>元素的autowire-candidate属性为false，容器就不会让这个bean进入自动装配的架构中（包括注解形式的配置，比如@Autowired）。

On a per-bean basis, you can exclude a bean from autowiring. In Spring’s XML format, set the autowire-candidate attribute of the <bean/> element to false; the container makes that specific bean definition unavailable to the autowiring infrastructure (including annotation style configurations such as @Autowired).

[Note]
The autowire-candidate attribute is designed to only affect type-based autowiring. It does not affect explicit references by name, which will get resolved even if the specified bean is not marked as an autowire candidate. As a consequence, autowiring by name will nevertheless inject a bean if the name matches.

也可以通过定义bean名称的匹配模式避免自动装配。顶级元素<beans>的属性default-autowire-candidates可以接受一个或多个模式。例如，为了限制以Repository结尾的bean自动装配，提供一个值*Repository即可。如果需要提供多个模式，请以逗号分隔。bean定义上的autowire-candidate属性优先级要更高一点，对于这类bean，匹配模式将不起作用。

You can also limit autowire candidates based on pattern-matching against bean names. The top-level <beans/> element accepts one or more patterns within its default-autowire-candidates attribute. For example, to limit autowire candidate status to any bean whose name ends with Repository, provide a value of *Repository. To provide multiple patterns, define them in a comma-separated list. An explicit value of true or false for a bean definitions autowire-candidate attribute always takes precedence, and for such beans, the pattern matching rules do not apply.

这项技术对于那些你不想它们被自动注入到其它bean的bean非常有用。这并不意味着它自己不能使用自动装配，而是，它不是其它bean自动装配的候选者。  

These techniques are useful for beans that you never want to be injected into other beans by autowiring. It does not mean that an excluded bean cannot itself be configured using autowiring. Rather, the bean itself is not a candidate for autowiring other beans.

### 7.4.6 方法注入
7.4.6 Method injection  

在大部分应用场景中，容器中的大多数bean都是单例的。当一个单例bean需要与另一个单例bean合作，或者一个非单例bean需要与另外一个非单例bean合作的时候，仅仅定义一个bean作为另一个的属性就能解决它们的依赖关系。如果bean的生命周期不一致就会出现问题。假如，一个单例bean A 需要使用一个非单例bean B，也许在每次调用A的方法时都需要。容器仅仅创建这个单例bean A 一次，所以只有一次机会设置其属性。容器不能在 A 每次调用的时候都提供一个新的 B 的实例。

In most application scenarios, most beans in the container are singletons. When a singleton bean needs to collaborate with another singleton bean, or a non-singleton bean needs to collaborate with another non-singleton bean, you typically handle the dependency by defining one bean as a property of the other. A problem arises when the bean lifecycles are different. Suppose singleton bean A needs to use non-singleton (prototype) bean B, perhaps on each method invocation on A. The container only creates the singleton bean A once, and thus only gets one opportunity to set the properties. The container cannot provide bean A with a new instance of bean B every time one is needed.

一种解决方法是放弃控制反转。你可以通过实现ApplicationContextAware接口使 A 能连接到容器，并在每次 A 需要 B 的实例的时候调用容器的getBean(“B”)方法取得新的 B 的实例。下面是这种方式的一种案例：

A solution is to forego some inversion of control. You can make bean A aware of the container by implementing the ApplicationContextAware interface, and by making a getBean("B") call to the container ask for (a typically new) bean B instance every time bean A needs it. The following is an example of this approach:
```java
// a class that uses a stateful Command-style class to perform some processing
package fiona.apple;

// Spring-API imports
import org.springframework.beans.BeansException;
import org.springframework.context.ApplicationContext;
import org.springframework.context.ApplicationContextAware;

public class CommandManager implements ApplicationContextAware {

    private ApplicationContext applicationContext;

    public Object process(Map commandState) {
        // grab a new instance of the appropriate Command
        Command command = createCommand();
        // set the state on the (hopefully brand new) Command instance
        command.setState(commandState);
        return command.execute();
    }

    protected Command createCommand() {
        // notice the Spring API dependency!
        return this.applicationContext.getBean("command", Command.class);
    }

    public void setApplicationContext(
            ApplicationContext applicationContext) throws BeansException {
        this.applicationContext = applicationContext;
    }
}
```
上面这种方式并不推荐使用，因为业务代码跟spring耦合了。方法注入，从某种角度来看是spring IoC容器的高级特性，允许以一种干净的方式处理这种用例。

The preceding is not desirable, because the business code is aware of and coupled to the Spring Framework. Method Injection, a somewhat advanced feature of the Spring IoC container, allows this use case to be handled in a clean fashion.

可以在这篇博客找到更多关于方法注入的信息。  

You can read more about the motivation for Method Injection in this blog entry.

__查找方法注入__
Lookup method injection

查找方法注入是容器的一项能力，它可以重写容器管理的bean的方法，并返回另一个bean的查找结果。查找往往涉及到前面描述的那种原型（prototype）bean。spring利用CGLIB库动态地生成字节码子类，从而重写方法以实现查找方法注入。

Lookup method injection is the ability of the container to override methods on container managed beans, to return the lookup result for another named bean in the container. The lookup typically involves a prototype bean as in the scenario described in the preceding section. The Spring Framework implements this method injection by using bytecode generation from the CGLIB library to generate dynamically a subclass that overrides the method.

[Note]
* 为了能使动态的子类有效，被继承的类不能是final，且被重写的方法也不能是final。  
For this dynamic subclassing to work, the class that the Spring bean container will subclass cannot be final, and the method to be overridden cannot be final either.

* 单元测试一个具有抽象方法的类时，需要手动继承此类并重写其抽象方法。  
Unit-testing a class that has an abstract method requires you to subclass the class yourself and to supply a stub implementation of the abstract method.

* 组件扫描的具体方法也需要具体类。  
Concrete methods are also necessary for component scanning which requires concrete classes to pick up.

* 一项关键限制是查找方法不能使用工厂方法和配置类中的@Bean方法，因为容器不会在运行时创建一个子类及其实例。
最后，方法注入的目标对象不能被序列化。  
A further key limitation is that lookup methods won’t work with factory methods and in particular not with @Bean methods in configuration classes, since the container is not in charge of creating the instance in that case and therefore cannot create a runtime-generated subclass on the fly.

查看前面关于CommandManager类的代码片段，可以发现spring容器会动态生成createCommand()方法的实现。CommandManager不会有任何的spring依赖，如下所示：

Looking at the CommandManager class in the previous code snippet, you see that the Spring container will dynamically override the implementation of the createCommand() method. Your CommandManager class will not have any Spring dependencies, as can be seen in the reworked example:
```java
package fiona.apple;

// no more Spring imports!

public abstract class CommandManager {

    public Object process(Object commandState) {
        // grab a new instance of the appropriate Command interface
        Command command = createCommand();
        // set the state on the (hopefully brand new) Command instance
        command.setState(commandState);
        return command.execute();
    }

    // okay... but where is the implementation of this method?
    protected abstract Command createCommand();
}
```
客户端类包含了将被注入的方法（本例中的CommandManager），被注入的方法需要如下签名：  
In the client class containing the method to be injected (the CommandManager in this case), the method to be injected requires a signature of the following form:

`<public|protected> [abstract] <return-type> theMethodName(no-arguments);`

如果方法是抽象的，动态生成的子类会实现这个方法。另外，动态生成的子类也会重写原类中的具体方法。例如：

If the method is abstract, the dynamically-generated subclass implements the method. Otherwise, the dynamically-generated subclass overrides the concrete method defined in the original class. For example:
```xml
<!-- a stateful bean deployed as a prototype (non-singleton) -->
<bean id="myCommand" class="fiona.apple.AsyncCommand" scope="prototype">
    <!-- inject dependencies here as required -->
</bean>

<!-- commandProcessor uses statefulCommandHelper -->
<bean id="commandManager" class="fiona.apple.CommandManager">
    <lookup-method name="createCommand" bean="myCommand"/>
</bean>
```
commandManager这个bean会在任何它需要command实例的时候调用其createCommand()方法。如果实际需要，则必须把command声明为原型（prototype）。如果被声明为单例（singleton），则每次返回的都是同一个command实例。

有兴趣的读者可能会发现ServiceLocatorFactoryBean（位于包org.springframework.beans.factory.config中）就是这么用的。ServiceLocatorFactoryBean中用法与其它工具类一样，ObjectFactoryCreatingFactoryBean，但是它允许你使用自己的查找接口而不是spring指定的查找接口。请参考附加信息中这些类的javadoc文档。

The bean identified as commandManager calls its own method createCommand() whenever it needs a new instance of the myCommand bean. You must be careful to deploy the myCommand bean as a prototype, if that is actually what is needed. If it is as a singleton, the same instance of the myCommand bean is returned each time.


Alternatively, within the annotation-based component model, you may declare a lookup method through the @Lookup annotation:
```java
public abstract class CommandManager {

    public Object process(Object commandState) {
        Command command = createCommand();
        command.setState(commandState);
        return command.execute();
    }

    @Lookup("myCommand")
    protected abstract Command createCommand();
}
```
Or, more idiomatically, you may rely on the target bean getting resolved against the declared return type of the lookup method:
```java
public abstract class CommandManager {

    public Object process(Object commandState) {
        MyCommand command = createCommand();
        command.setState(commandState);
        return command.execute();
    }

    @Lookup
    protected abstract MyCommand createCommand();
}
```
Note that you will typically declare such annotated lookup methods with a concrete stub implementation, in order for them to be compatible with Spring’s component scanning rules where abstract classes get ignored by default. This limitation does not apply in case of explicitly registered or explicitly imported bean classes.

[Tip]
Another way of accessing differently scoped target beans is an ObjectFactory/ Provider injection point. Check out the section called “Scoped beans as dependencies”.

The interested reader may also find the ServiceLocatorFactoryBean (in the org.springframework.beans.factory.config package) to be of use.

__任意的方法替换__
Arbitrary method replacement  

方法注入的一种很少使用的形式，它是使用另外的方法实现替换目标bean中的任意方法。读者可以跳过本章的剩余部分直到这项功能真正需要的时候再回来看。

A less useful form of method injection than lookup method injection is the ability to replace arbitrary methods in a managed bean with another method implementation. Users may safely skip the rest of this section until the functionality is actually needed.

以xml形式为例，可以使用replaced-method元素指定用另一种实现替换bean中已存在的方法。如下例，我们希望重写其computeValue方法：

With XML-based configuration metadata, you can use the replaced-method element to replace an existing method implementation with another, for a deployed bean. Consider the following class, with a method computeValue, which we want to override:
```java
public class MyValueCalculator {

    public String computeValue(String input) {
        // some real code...
    }

    // some other methods...
}
```
一个实现了org.springframework.beans.factory.support.MethodReplacer接口的类提供了一个新的方法定义。  

A class implementing the org.springframework.beans.factory.support.MethodReplacer interface provides the new method definition.
```java
/**
 * meant to be used to override the existing computeValue(String)
 * implementation in MyValueCalculator
 */
public class ReplacementComputeValue implements MethodReplacer {

    public Object reimplement(Object o, Method m, Object[] args) throws Throwable {
        // get the input value, work with it, and return a computed result
        String input = (String) args[0];
        ...
        return ...;
    }
}
```
原类的bean定义可能看起来像这样：

The bean definition to deploy the original class and specify the method override would look like this:
```xml
<bean id="myValueCalculator" class="x.y.z.MyValueCalculator">
    <!-- arbitrary method replacement -->
    <replaced-method name="computeValue" replacer="replacementComputeValue">
        <arg-type>String</arg-type>
    </replaced-method>
</bean>

<bean id="replacementComputeValue" class="a.b.c.ReplacementComputeValue"/>
```
可以使用一个或多个<arg-type/>元素指明原方法的参数签名。参数的签名仅仅当原方法有多个重载方法时才是必要的。为了方便，string类型的参数也可以是其全路径的子串，例如，以下方式均可匹配java.lang.String：

You can use one or more contained <arg-type/> elements within the <replaced-method/> element to indicate the method signature of the method being overridden. The signature for the arguments is necessary only if the method is overloaded and multiple variants exist within the class. For convenience, the type string for an argument may be a substring of the fully qualified type name. For example, the following all match java.lang.String:
```java
java.lang.String
String
Str
```
因为参数的个数往往就能够区分每一种可能了，这种简写方法可以少打几个字，所以你可以只输入最短的字符就能够匹配参数类型。

Because the number of arguments is often enough to distinguish between each possible choice, this shortcut can save a lot of typing, by allowing you to type only the shortest string that will match an argument type.

## 7.5 bean的作用域
7.5 Bean scopes  

当创建一个bean定义时，就给了一份创建那个类的实例的食谱。bean定义是一份食谱，这个想法很重要，因为那意味着，可以根据一份食谱创建很多实例。

When you create a bean definition, you create a recipe for creating actual instances of the class defined by that bean definition. The idea that a bean definition is a recipe is important, because it means that, as with a class, you can create many object instances from a single recipe.

不仅可以控制bean实例的各种各样的依赖关系和配置值，还可以控制这些实例的作用域（scope）。这种方式很强大且是弹性的，因为可以通过配置选择实例的作用域，而不需要在类级别控制其作用域。bean可以被部署成多个作用域之一，spring支持很多作用域，对于web应用可以使用5种。

You can control not only the various dependencies and configuration values that are to be plugged into an object that is created from a particular bean definition, but also the scope of the objects created from a particular bean definition. This approach is powerful and flexible in that you can choose the scope of the objects you create through configuration instead of having to bake in the scope of an object at the Java class level. Beans can be defined to be deployed in one of a number of scopes: out of the box, the Spring Framework supports seven scopes, five of which are available only if you use a web-aware ApplicationContext.

下面的作用域都是可以直接使用的，也可以创建自定义作用域。

The following scopes are supported out of the box. You can also create a custom scope.

__表 7.3. bean的作用域__
Table 7.3. Bean scopes

Scope |Description
--|--
singleton |(Default) Scopes a single bean definition to a single object instance per Spring IoC container.
singleton（单例） |	默认值，每个spring的IoC容器中只保留一份bean定义对应的实例。
prototype |Scopes a single bean definition to any number of object instances.
prototype（原型）|	一份bean定义对应多个实例。
request |Scopes a single bean definition to the lifecycle of a single HTTP request; that is, each HTTP request has its own instance of a bean created off the back of a single bean definition. Only valid in the context of a web-aware Spring ApplicationContext.
request（请求）|	依赖于Http请求的生命周期，也就是说，每个Http请求都有它自己实例。这只在web应用上下文中有效。
session |Scopes a single bean definition to the lifecycle of an HTTP Session. Only valid in the context of a web-aware Spring ApplicationContext.
session（会话）	| 依赖于Http会话的生命周期。这只在web应用上下文中有效。
globalSession |Scopes a single bean definition to the lifecycle of a global HTTP Session. Typically only valid when used in a Portlet context. Only valid in the context of a web-aware Spring ApplicationContext.
globalSession（全局会话）|	依赖于全局Http会话的生命周期。典型地仅当使用在Portlet上下文中有效。这只在web应用上下文中有效。
application |Scopes a single bean definition to the lifecycle of a ServletContext. Only valid in the context of a web-aware Spring ApplicationContext.
application（应用）|	依赖于ServletContext的生命周期。这只在web应用上下文中有效。
websocket |Scopes a single bean definition to the lifecycle of a WebSocket. Only valid in the context of a web-aware Spring ApplicationContext.
websocket	依赖于WebSocket的生命周期。这只在web应用上下文中有效。

[Note]
> 从Spring 3.0开始，还有一个thread作用域，但默认并没有被注册。更多信息请参考SimpleThreadScope的文档。关于怎么注册这个作用域及任何自定义作用域，请参考使用自定义作用域。  
As of Spring 3.0, a thread scope is available, but is not registered by default. For more information, see the documentation for SimpleThreadScope. For instructions on how to register this or any other custom scope, see the section called “Using a custom scope”.

### 7.5.1 单例作用域
7.5.1 The singleton scope

一个单例bean仅共享同一个实例，并且所有的请求都只返回同一个实例。

Only one shared instance of a singleton bean is managed, and all requests for beans with an id or ids matching that bean definition result in that one specific bean instance being returned by the Spring container.

换句话说，当把一个bean定义为单例时，spring容器严格地只创建那个bean定义的一个实例。这个单独的实例被存储在单例bean的缓存中，并且所有后来的请求及引用都会返回这个缓存的对象。

To put it another way, when you define a bean definition and it is scoped as a singleton, the Spring IoC container creates exactly one instance of the object defined by that bean definition. This single instance is stored in a cache of such singleton beans, and all subsequent requests and references for that named bean return the cached object.

![](https://docs.spring.io/spring/docs/4.3.20.BUILD-SNAPSHOT/spring-framework-reference/htmlsingle/images/singleton.png)

singleton

spring中单例bean的概念不同于GoF设计模式中关于单例模式的定义。GoF的单例模式硬编码了对象的作用域，所以一个特定的类有且只有一个实例在一个类加载器中被创建。spring单例的作用域被更好地描述 为每个容器及每个bean。这意味着如果在一个spring容器中定义了一个特定类的bean，那么spring容器会且仅会创建一个那个bean定义对应的类的实例。单例作用域是spring默认的作用域。在xml形式中，按如下方式定义一个单例bean：

Spring’s concept of a singleton bean differs from the Singleton pattern as defined in the Gang of Four (GoF) patterns book. The GoF Singleton hard-codes the scope of an object such that one and only one instance of a particular class is created per ClassLoader. The scope of the Spring singleton is best described as per container and per bean. This means that if you define one bean for a particular class in a single Spring container, then the Spring container creates one and only one instance of the class defined by that bean definition. The singleton scope is the default scope in Spring. To define a bean as a singleton in XML, you would write, for example:
```xml
<bean id="accountService" class="com.foo.DefaultAccountService"/>

<!-- the following is equivalent, though redundant (singleton scope is the default) -->
<bean id="accountService" class="com.foo.DefaultAccountService" scope="singleton"/>
```

### 7.5.2 原型作用域
7.5.2 The prototype scope 

非单例的原型作用域的bean将会导致每次请求时都创建一个新的实例。也就是说，在这个bean被注入到另一个bean时，或通过容器的getBean()方法请求它时都会创建一个新的实例。通常来说，对于所有有状态的bean使用原型作用域，对于无状态的bean使用单例作用域。

The non-singleton, prototype scope of bean deployment results in the creation of a new bean instance every time a request for that specific bean is made. That is, the bean is injected into another bean or you request it through a getBean() method call on the container. As a rule, use the prototype scope for all stateful beans and the singleton scope for stateless beans.

下面的图表描绘了spring的原型作用域。一个DAO对象无需配置成原型的，因为它不具有状态，这里仅仅是作者为了重用单例作用域那节的图表。

The following diagram illustrates the Spring prototype scope. A data access object (DAO) is not typically configured as a prototype, because a typical DAO does not hold any conversational state; it was just easier for this author to reuse the core of the singleton diagram.
![](https://docs.spring.io/spring/docs/4.3.20.BUILD-SNAPSHOT/spring-framework-reference/htmlsingle/images/prototype.png)
prototype

在xml形式中，按如下方式定义一个原型bean：

The following example defines a bean as a prototype in XML:

```xml
<bean id="accountService" class="com.foo.DefaultAccountService" scope="prototype"/>
```

与其它作用域相比，spring不会管理一个原型bean的完整生命周期：容器实例化、配置、装配原型对象，然后把它交给客户端，没有更多原型实例的记录了。因此，尽管初始化的回调方法在所有对象上被调用，但是对于原型对象，销毁的回调方法并未被调用。客户端代码必须自己清除原型作用域的对象并释放其占用的资源。为了让spring容器释放原型对象占用的资源，试着使用自定义的bean后置处理器，它拥有将要被清除的bean的引用。

In contrast to the other scopes, Spring does not manage the complete lifecycle of a prototype bean: the container instantiates, configures, and otherwise assembles a prototype object, and hands it to the client, with no further record of that prototype instance. Thus, although initialization lifecycle callback methods are called on all objects regardless of scope, in the case of prototypes, configured destruction lifecycle callbacks are not called. The client code must clean up prototype-scoped objects and release expensive resources that the prototype bean(s) are holding. To get the Spring container to release resources held by prototype-scoped beans, try using a custom bean post-processor, which holds a reference to beans that need to be cleaned up.

在某些方面，spring容器关于原型对象的角色就相当于Java中的new操作。从传递点之后所有的生命周期都有客户端自己处理（关于spring容器中bean的生命周期的详细信息，请参考7.6.1 生命周期回调）。

In some respects, the Spring container’s role in regard to a prototype-scoped bean is a replacement for the Java new operator. All lifecycle management past that point must be handled by the client. (For details on the lifecycle of a bean in the Spring container, see Section 7.6.1, “Lifecycle callbacks”.)

### 7.5.3 依赖于原型bean的单例bean
7.5.3 Singleton beans with prototype-bean dependencies

当使用依赖于原型bean的单例bean的时候，注意依赖关系是在实例化的时候被解决的。因此，如果依赖注入一个原型bean到单例bean，一个新的原型bean会被实例化，然后注入到单例bean中。这个原型实例是唯一一个提供给这个单例bean的实例。

When you use singleton-scoped beans with dependencies on prototype beans, be aware that dependencies are resolved at instantiation time. Thus if you dependency-inject a prototype-scoped bean into a singleton-scoped bean, a new prototype bean is instantiated and then dependency-injected into the singleton bean. The prototype instance is the sole instance that is ever supplied to the singleton-scoped bean.

但是，假设需要在运行时单例bean每次都获得一个新的原型bean，没有办法做到注入一个新的原型bean到单例bean，因为注入仅仅发生一次，当容器实例化单例bean的时候已经解决了它的依赖的注入。如果需要在运行时获取新的原型实例，请参考7.4.6 方法注入。

However, suppose you want the singleton-scoped bean to acquire a new instance of the prototype-scoped bean repeatedly at runtime. You cannot dependency-inject a prototype-scoped bean into your singleton bean, because that injection occurs only once, when the Spring container is instantiating the singleton bean and resolving and injecting its dependencies. If you need a new instance of a prototype bean at runtime more than once, see Section 7.4.6, “Method injection”

### 7.5.4 请求、会话、全局会话、应用及WebSocket作用域
7.5.4 Request, session, global session, application, and WebSocket scopes


请求、会话、全局会话、应用及WebSocket作用域仅仅当使用在web上下文中才有效（比如XmlWebApplicationContext）。如果在其它spring容器中使用这些作用域，比如ClassPathXmlApplicationContext，将会抛出IllegalStateException，因为未知的作用域。

The request, session, globalSession, application, and websocket scopes are only available if you use a web-aware Spring ApplicationContext implementation (such as XmlWebApplicationContext). If you use these scopes with regular Spring IoC containers such as the ClassPathXmlApplicationContext, an IllegalStateException will be thrown complaining about an unknown bean scope.

__初始化web配置__
Initial web configuration

为了支持请求、会话、全局会话、应用及WebSocket作用域，一些小小的初始化配置是必须的（对于单例和原型作用域，初始化配置并不是必须的）。

To support the scoping of beans at the request, session, globalSession, application, and websocket levels (web-scoped beans), some minor initial configuration is required before you define your beans. (This initial setup is not required for the standard scopes, singleton and prototype.)

怎么完成这个初始化配置依赖于特定的Servlet环境。

How you accomplish this initial setup depends on your particular Servlet environment.

如果使用Spring Web MVC，使用Spring的DispatcherServlet或DispatcherPortlet处理请求，那么就需要什么配置了，因为DispatcherServlet和DispatcherPortlet已经帮你配置好了。

If you access scoped beans within Spring Web MVC, in effect, within a request that is processed by the Spring DispatcherServlet or DispatcherPortlet, then no special setup is necessary: DispatcherServlet and DispatcherPortlet already expose all relevant state.

如果使用Servlet 2.5 Web容器，不使用Spring的DispatcherServlet处理请求（例如，使用JSF或Struts时），则需要注册org.springframework.web.context.request.RequestContextListener ServletRequestListener。对于Servlet 3.0+，这项配置可以通过WebApplicationInitializer接口编程式处理。对于更老的容器，只能在web.xml文件中添加如下声明了：

If you use a Servlet 2.5 web container, with requests processed outside of Spring’s DispatcherServlet (for example, when using JSF or Struts), you need to register the org.springframework.web.context.request.RequestContextListener ServletRequestListener. For Servlet 3.0+, this can be done programmatically via the WebApplicationInitializer interface. Alternatively, or for older containers, add the following declaration to your web application’s web.xml file:
```xml
<web-app>
    ...
    <listener>
        <listener-class>
            org.springframework.web.context.request.RequestContextListener
        </listener-class>
    </listener>
    ...
</web-app>
```
如果使用上述配置，考虑使用Spring的RequestContextFilter。过滤器映射依赖于其web应用配置，所以应该合适地调整之。

Alternatively, if there are issues with your listener setup, consider using Spring’s RequestContextFilter. The filter mapping depends on the surrounding web application configuration, so you have to change it as appropriate.
```xml
<web-app>
    ...
    <filter>
        <filter-name>requestContextFilter</filter-name>
        <filter-class>org.springframework.web.filter.RequestContextFilter</filter-class>
    </filter>
    <filter-mapping>
        <filter-name>requestContextFilter</filter-name>
        <url-pattern>/*</url-pattern>
    </filter-mapping>
    ...
</web-app>
```

DispatcherServlet, RequestContextListener和RequestContextFilter其实是做了同一件事，也就是绑定Http请求对象到服务于那个请求的线程。这样才能使得请求或会话作用域的bean能够更深入到调用链中。

DispatcherServlet, RequestContextListener, and RequestContextFilter all do exactly the same thing, namely bind the HTTP request object to the Thread that is servicing that request. This makes beans that are request- and session-scoped available further down the call chain.

__请求作用域__
Request scope

考虑如下xml形式的bean定义：

Consider the following XML configuration for a bean definition:
```xml
<bean id="loginAction" class="com.foo.LoginAction" scope="request"/>
```
spring容器为每次Http请求创建了一个新的LoginAction的实例。也就是说，loginAction的作用域是Http请求级别的。可以尽情地改变这个实例的内部状态，因为从同一份bean定义创建的其它实例不会发现这些状态的改变，它们只与特定的请求相关。当请求处理完成了，它绑定的bean就会被丢弃。

The Spring container creates a new instance of the LoginAction bean by using the loginAction bean definition for each and every HTTP request. That is, the loginAction bean is scoped at the HTTP request level. You can change the internal state of the instance that is created as much as you want, because other instances created from the same loginAction bean definition will not see these changes in state; they are particular to an individual request. When the request completes processing, the bean that is scoped to the request is discarded.


当使用注解驱动的组件或Java配置时，@RequestScope注解可以被用来赋值给一个组件的请求作用域。

When using annotation-driven components or Java Config, the @RequestScope annotation can be used to assign a component to the request scope.
```java
@RequestScope
@Component
public class LoginAction {
    // ...
}
```
__会话作用域__
Session scope

考虑如下xml形式的bean定义：

Consider the following XML configuration for a bean definition:
```xml
<bean id="userPreferences" class="com.foo.UserPreferences" scope="session"/>
```
spring容器为一个单独的Htpp会话创建一个新的UserPreferences实例。也就是说userPreferences的作用域是Http会话级别的。与请求作用域的bean一样，可以尽情地改变这个实例的内部状态，因为从同一份bean定义创建的其它实例不会发现这些状态的改变，它们只与特定的Http会话相关。当这个Http会话被丢弃的时候，它绑定的bean也就被丢弃了。

The Spring container creates a new instance of the UserPreferences bean by using the userPreferences bean definition for the lifetime of a single HTTP Session. In other words, the userPreferences bean is effectively scoped at the HTTP Session level. As with request-scoped beans, you can change the internal state of the instance that is created as much as you want, knowing that other HTTP Session instances that are also using instances created from the same userPreferences bean definition do not see these changes in state, because they are particular to an individual HTTP Session. When the HTTP Session is eventually discarded, the bean that is scoped to that particular HTTP Session is also discarded.

当使用注解驱动的组件或Java配置时，@SessionScope 注解可以被用来赋值给一个组件的会话作用域。

When using annotation-driven components or Java Config, the @SessionScope annotation can be used to assign a component to the session scope.
```java
@SessionScope
@Component
public class UserPreferences {
    // ...
}
```

__全局会话作用域__
Global session scope

考虑下面的定义：

Consider the following bean definition:
```xml
<bean id="userPreferences" class="com.foo.UserPreferences" scope="globalSession"/>
```

全局会话作用域与标准的Http会话作用域类似，不过它只用在基于portlet的web应用上下文中。portlet规范中定义了全局会话的概念，那就是共享了同一个portlet应用中所有的组件的全局的会话。定义为全局会话作用域的bean的生命周期与全局的portlet会话相关。

The globalSession scope is similar to the standard HTTP Session scope (described above), and applies only in the context of portlet-based web applications. The portlet specification defines the notion of a global Session that is shared among all portlets that make up a single portlet web application. Beans defined at the globalSession scope are scoped (or bound) to the lifetime of the global portlet Session.

如果在标准的Servlet应用中定义一个或多个拥有全局会话作用域的bean，那么标准的Http会话作用域将被使用，并不会报错。

If you write a standard Servlet-based web application and you define one or more beans as having globalSession scope, the standard HTTP Session scope is used, and no error is raised.

__应用作用域__
Application scope

考虑如下xml形式的bean定义：

Consider the following XML configuration for a bean definition:
```xml
<bean id="appPreferences" class="com.foo.AppPreferences" scope="application"/>
```
对于一个完整的web应用spring容器会创建一个新的AppPreferences实例。也就是说，appPreferences的作用域是ServletContext级别的，作为一个ServletContext普通属性被存储。这与spring的单例bean很类似，但有两种非常重要的区别：它在每个ServletContext中是单例，而不是spring的“ApplicationContext”（ApplicationContext在任何给定的web应用中可能存在多个）。另外，它实际上是作为ServletContext的属性被暴露并可见。

The Spring container creates a new instance of the AppPreferences bean by using the appPreferences bean definition once for the entire web application. That is, the appPreferences bean is scoped at the ServletContext level, stored as a regular ServletContext attribute. This is somewhat similar to a Spring singleton bean but differs in two important ways: It is a singleton per ServletContext, not per Spring 'ApplicationContext' (for which there may be several in any given web application), and it is actually exposed and therefore visible as a ServletContext attribute.

当使用注解驱动的组件或Java配置时，@ApplicationScope 注解可以被用来赋值给一个组件的应用作用域。

When using annotation-driven components or Java Config, the @ApplicationScope annotation can be used to assign a component to the application scope.
```java
@ApplicationScope
@Component
public class AppPreferences {
    // ...
}
```
__有作用域的bean作为依赖项__
Scoped beans as dependencies

spring容器不仅管理对象（bean）的实例化，也连接合作者（依赖）。例如，如果注入一个Http请求作用域的bean到另一个更长周期的bean，可能会在原bean的位置选择使用AOP代理。也就是说，需要注入一个代理对象，这个代理对象暴露了与原对象相同的公共接口，但是它能从相关的作用域获取真实的目标对象，并以委托的方式调用真实对象的方法。

The Spring IoC container manages not only the instantiation of your objects (beans), but also the wiring up of collaborators (or dependencies). If you want to inject (for example) an HTTP request scoped bean into another bean of a longer-lived scope, you may choose to inject an AOP proxy in place of the scoped bean. That is, you need to inject a proxy object that exposes the same public interface as the scoped object but that can also retrieve the real target object from the relevant scope (such as an HTTP request) and delegate method calls onto the real object.

[Note]
> 你可能也会在单例bean之间使用<aop:scoped-proxy/>，通过序列化的中间代理使用这个引用，然后可以重新获得反序列化的目标单例bean。   
You may also use <aop:scoped-proxy/> between beans that are scoped as singleton, with the reference then going through an intermediate proxy that is serializable and therefore able to re-obtain the target singleton bean on deserialization.

> 当在原型对象上使用<aop:scoped-proxy/>时，在共享的代理对象上每次的方法调用都会创建一个新的目标实例。   
When declaring <aop:scoped-proxy/> against a bean of scope prototype, every method call on the shared proxy will lead to the creation of a new target instance which the call is then being forwarded to.

> 另外，有作用域的代理并不是唯一一种生命周期安全的访问更短作用域的bean的方式。也可以简单地声明注入点（构造方法、setter方法的参数或自动装配的字段）作为ObjectFactory<MyTargetBean>，在每次需要的时候调用getObject()获取当前实例——这种方式不用占有实例或分开存储它。   
Also, scoped proxies are not the only way to access beans from shorter scopes in a lifecycle-safe fashion. You may also simply declare your injection point (i.e. the constructor/setter argument or autowired field) as ObjectFactory<MyTargetBean>, allowing for a getObject() call to retrieve the current instance on demand every time it is needed - without holding on to the instance or storing it separately.

> As an extended variant, you may declare ObjectProvider<MyTargetBean> which delivers several additional access variants, including getIfAvailable and getIfUnique.

> 这种方式在JSR-330中的变种叫作Provider，使用Provider<MyTargetBean>声明并在每次需要时调用get()方法。更多关于JSR-330的详细信息请点击这里。  
The JSR-330 variant of this is called Provider, used with a Provider<MyTargetBean> declaration and a corresponding get() call for every retrieval attempt. See here for more details on JSR-330 overall.

下面的配置中仅仅只有一行，便更重要地是要理解“为什么”和“怎么样”。

The configuration in the following example is only one line, but it is important to understand the "why" as well as the "how" behind it.
```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns:aop="http://www.springframework.org/schema/aop"
    xsi:schemaLocation="http://www.springframework.org/schema/beans
        http://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/aop
        http://www.springframework.org/schema/aop/spring-aop.xsd">

    <!-- an HTTP Session-scoped bean exposed as a proxy -->
    <bean id="userPreferences" class="com.foo.UserPreferences" scope="session">
        <!-- instructs the container to proxy the surrounding bean -->
        <aop:scoped-proxy/>
    </bean>

    <!-- a singleton-scoped bean injected with a proxy to the above bean -->
    <bean id="userService" class="com.foo.SimpleUserService">
        <!-- a reference to the proxied userPreferences bean -->
        <property name="userPreferences" ref="userPreferences"/>
    </bean>
</beans>
```
为了创建那么个代理，在bean定义中插入子元素<aop:scoped-proxy/>即可（参考选择代理的类型和41 基于xml的配置）。为什么请求、会话、全局会话及自定义级别的作用域的bean需要<aop:scoped-proxy/>元素呢？让我们检验下面的单例bean的定义，并与前面的定义做对比（下面的userPreferences的定义目前来说是不完整的）。

To create such a proxy, you insert a child <aop:scoped-proxy/> element into a scoped bean definition (see the section called “Choosing the type of proxy to create” and Chapter 41, XML Schema-based configuration). Why do definitions of beans scoped at the request, session, globalSession and custom-scope levels require the <aop:scoped-proxy/> element? Let’s examine the following singleton bean definition and contrast it with what you need to define for the aforementioned scopes (note that the following userPreferences bean definition as it stands is incomplete).
```xml
<bean id="userPreferences" class="com.foo.UserPreferences" scope="session"/>

<bean id="userManager" class="com.foo.UserManager">
    <property name="userPreferences" ref="userPreferences"/>
</bean>
```
在这个例子中，单例userManager被注入了一个Http会话作用域的对象userPreferences。这里的显著点是userManager是一个单例：它将在每个容器中严格地实例化一次，并且它的依赖（此例中仅仅userPreferences一个）也只会注入一次。这就意味着userManager将只会操作同一个userPreferences对象，也就是一开始注入的那个。

In the preceding example, the singleton bean userManager is injected with a reference to the HTTP Session-scoped bean userPreferences. The salient point here is that the userManager bean is a singleton: it will be instantiated exactly once per container, and its dependencies (in this case only one, the userPreferences bean) are also injected only once. This means that the userManager bean will only operate on the exact same userPreferences object, that is, the one that it was originally injected with.

这并不是我们把短周期的bean注入长周期的bean想要的结果，例如，注入一个Http会话bean到一个单例bean中。不如说，我们只需要一个userManager对象，但是需要与特定会话关联的userPreferences对象。这样，容器创建一个与UserPreferences类相同的公共接口的对象（理想状态是UserPreferences的一个实例），它可以从作用域机制（Http请求、会话等）中获取真实的UserPreferences对象。然后把这个代理对象注入到userManager中，且userManager不会意识到这是一个代理。在这个案例中，当UserManager实例调用依赖注入的UserPreferences对象的方法时，实际上是调用的这个代理的方法。这个代理然后从Http会话中获取真实的UserPreferences对象，并在这个真实的UserPreferences对象上委托方法调用。

This is not the behavior you want when injecting a shorter-lived scoped bean into a longer-lived scoped bean, for example injecting an HTTP Session-scoped collaborating bean as a dependency into singleton bean. Rather, you need a single userManager object, and for the lifetime of an HTTP Session, you need a userPreferences object that is specific to said HTTP Session. Thus the container creates an object that exposes the exact same public interface as the UserPreferences class (ideally an object that is a UserPreferences instance) which can fetch the real UserPreferences object from the scoping mechanism (HTTP request, Session, etc.). The container injects this proxy object into the userManager bean, which is unaware that this UserPreferences reference is a proxy. In this example, when a UserManager instance invokes a method on the dependency-injected UserPreferences object, it actually is invoking a method on the proxy. The proxy then fetches the real UserPreferences object from (in this case) the HTTP Session, and delegates the method invocation onto the retrieved real UserPreferences object.

因此，当注入请求、会话和全局会话作用域的bean到合作对象时，需要下面这样正确和完整的配置：

Thus you need the following, correct and complete, configuration when injecting request-, session-, and globalSession-scoped beans into collaborating objects:
```xml
<bean id="userPreferences" class="com.foo.UserPreferences" scope="session">
    <aop:scoped-proxy/>
</bean>

<bean id="userManager" class="com.foo.UserManager">
    <property name="userPreferences" ref="userPreferences"/>
</bean>
```
__选择代理的类型__
Choosing the type of proxy to create

默认地，当spring容器创建一个标记了<aop:scoped-proxy/>元素的bean的代理时，会创建一个基于CGLIB的代理。

By default, when the Spring container creates a proxy for a bean that is marked up with the <aop:scoped-proxy/> element, a CGLIB-based class proxy is created.

[Note]
> CGLIB代理只能拦截公共方法调用！不要在那个代理上调用非公共方法，那样不会委托给真实的目标对象。  
CGLIB proxies only intercept public method calls! Do not call non-public methods on such a proxy; they will not be delegated to the actual scoped target object.

替代方案，可以配置spring容器创建标准的JDK基于接口的代理，通过指定<aop:scoped-proxy/>元素的proxy-target-class属性的值为false。使用JDK基于接口的代理意味着在classpath上不需要额外的库了。但是，这也意味着原类必须实现至少一个接口，并且注入了这个bean的所有合作者都必须通过这个bean的一个接口引用它。

Alternatively, you can configure the Spring container to create standard JDK interface-based proxies for such scoped beans, by specifying false for the value of the proxy-target-class attribute of the <aop:scoped-proxy/> element. Using JDK interface-based proxies means that you do not need additional libraries in your application classpath to effect such proxying. However, it also means that the class of the scoped bean must implement at least one interface, and that all collaborators into which the scoped bean is injected must reference the bean through one of its interfaces.
```xml
<!-- DefaultUserPreferences implements the UserPreferences interface -->
<bean id="userPreferences" class="com.foo.DefaultUserPreferences" scope="session">
    <aop:scoped-proxy proxy-target-class="false"/>
</bean>

<bean id="userManager" class="com.foo.UserManager">
    <property name="userPreferences" ref="userPreferences"/>
</bean>
```
更多关于选择基于类的代理还是选择基于接口的代理的详细信息，请参考[11.6 代理机制](#proxying mechanisms)。

For more detailed information about choosing class-based or interface-based proxying, see Section 11.6, “Proxying mechanisms”.

### 7.5.5 自定义作用域
7.5.5 Custom scopes

作用域机制是可以扩展的，我们可以定义自己的作用域，甚至可以重新定义已存在的作用域，不过后者被认为是坏的实践，并且不能重写内置的单例和原型作用域。

The bean scoping mechanism is extensible; You can define your own scopes, or even redefine existing scopes, although the latter is considered bad practice and you cannot override the built-in singleton and prototype scopes.

__创建自定义作用域__
Creating a custom scope

为了让自定义的作用域集成到spring容器中，需要实现org.springframework.beans.factory.config.Scope接口，这个接口会在接下来讲到。关于怎么实现自己的作用域，请参考spring自己提供的关于Scope的实现和Scope的javadocs，这里更详细地解释了需要实现的方法。

To integrate your custom scope(s) into the Spring container, you need to implement the org.springframework.beans.factory.config.Scope interface, which is described in this section. For an idea of how to implement your own scopes, see the Scope implementations that are supplied with the Spring Framework itself and the Scope javadocs, which explains the methods you need to implement in more detail.

Scope接口有四个方法，从作用域中获取对象、移除对象和允许它们被销毁。

The Scope interface has four methods to get objects from the scope, remove them from the scope, and allow them to be destroyed.

下面的方法用来从作用域中返回对象。例如，会话作用域的实现返回会话作用域的bean（如果不存在，则会返回一个新的实例，并绑定到会话中，为了将来使用）。

The following method returns the object from the underlying scope. The session scope implementation, for example, returns the session-scoped bean (and if it does not exist, the method returns a new instance of the bean, after having bound it to the session for future reference).
```java
Object get(String name, ObjectFactory objectFactory)
```
下面的方法用来从作用域中移除对象。例如，会话作用域的实现从会话中移除会话作用域的bean。对象应该被返回，但是如果指定名字的对象未找到可以返回null。

The following method removes the object from the underlying scope. The session scope implementation for example, removes the session-scoped bean from the underlying session. The object should be returned, but you can return null if the object with the specified name is not found.
```java
Object remove(String name)
```
下面的方法用来注册作用域在销毁时或其中对象被销毁时应该执行的回调。参考javadocspring的作用域的一个实现获取关于销毁回调的更多信息。

The following method registers the callbacks the scope should execute when it is destroyed or when the specified object in the scope is destroyed. Refer to the javadocs or a Spring scope implementation for more information on destruction callbacks.
```java
void registerDestructionCallback(String name, Runnable destructionCallback)
```
下面的方法用来从作用域中获取会话（conversation，非session）标识。每个作用域的标识都不一样。对于会话作用域的实现，这个标识可以是会话id（session id）。

The following method obtains the conversation identifier for the underlying scope. This identifier is different for each scope. For a session scoped implementation, this identifier can be the session identifier.
```java
String getConversationId()
```
__使用自定义作用域__
Using a custom scope

在写完或测试完一个或多个自定义作用域的实现后，需要让spring容器识别到新的作用域。下面的方法是注册一个新的作用域到spring容器的核心方法。

After you write and test one or more custom Scope implementations, you need to make the Spring container aware of your new scope(s). The following method is the central method to register a new Scope with the Spring container:
```java
void registerScope(String scopeName, Scope scope);
```
这个方法定义在ConfigurableBeanFactory接口中，它允许大部分ApplicationContext的具体实现，这些实现通过BeanFactory属性与Spring联系在一起。

This method is declared on the ConfigurableBeanFactory interface, which is available on most of the concrete ApplicationContext implementations that ship with Spring via the BeanFactory property.

registerScope(..)方法的第一个参数是唯一与一个作用域关联的名字，例如，spring容器自己的singleto和prototype。它的第二个参数是具体的自定义的Scope的实现的一个实例，也就是希望注册并使用的。

The first argument to the registerScope(..) method is the unique name associated with a scope; examples of such names in the Spring container itself are singleton and prototype. The second argument to the registerScope(..) method is an actual instance of the custom Scope implementation that you wish to register and use.

假设你写了自定义的Scope实现，并像下面这样注册了它。

Suppose that you write your custom Scope implementation, and then register it as below.

[Note]
> 下面的例子使用了已经包含在spring中但默认未注册的SimpleThreadScope。这种方式同样适用于自定义的Scope实现。  
The example below uses SimpleThreadScope which is included with Spring, but not registered by default. The instructions would be the same for your own custom Scope implementations.
```java
Scope threadScope = new SimpleThreadScope();
beanFactory.registerScope("thread", threadScope);
```
然后就可以按照作用域的规则使用自定义的作用域了：  

You then create bean definitions that adhere to the scoping rules of your custom Scope:
```java
<bean id="..." class="..." scope="thread">
```
使用自定义的Scope实现，并不一定要编程式地注册，也可以像下面这样声明，使用CustomScopeConfigurer类：  

With a custom Scope implementation, you are not limited to programmatic registration of the scope. You can also do the Scope registration declaratively, using the CustomScopeConfigurer class:
```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns:aop="http://www.springframework.org/schema/aop"
    xsi:schemaLocation="http://www.springframework.org/schema/beans
        http://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/aop
        http://www.springframework.org/schema/aop/spring-aop.xsd">

    <bean class="org.springframework.beans.factory.config.CustomScopeConfigurer">
        <property name="scopes">
            <map>
                <entry key="thread">
                    <bean class="org.springframework.context.support.SimpleThreadScope"/>
                </entry>
            </map>
        </property>
    </bean>

    <bean id="bar" class="x.y.Bar" scope="thread">
        <property name="name" value="Rick"/>
        <aop:scoped-proxy/>
    </bean>

    <bean id="foo" class="x.y.Foo">
        <property name="bar" ref="bar"/>
    </bean>

</beans>
```
[Note]
> 当放置<aop:scoped-proxy/>在一个FactoryBean实现中时，这个bean本身就有作用域了，它并不是从getObject()返回的对象。  
When you place <aop:scoped-proxy/> in a FactoryBean implementation, it is the factory bean itself that is scoped, not the object returned from getObject().

## 7.6 bean的特性
7.6 Customizing the nature of a bean

### 7.6.1 生命周期回调
7.6.1 Lifecycle callbacks

为了与容器中bean的生命周期管理交互，可以实现Spring的InitializingBean和DisposableBean接口。容器会在初始化和销毁bean时调用前者的afterPropertiesSet()和后者destroy()方法执行相应的动作。

To interact with the container’s management of the bean lifecycle, you can implement the Spring InitializingBean and DisposableBean interfaces. The container calls afterPropertiesSet() for the former and destroy() for the latter to allow the bean to perform certain actions upon initialization and destruction of your beans.

[Tip]
> 在现代spring应用中，JSR-250的@PostConstruct和@PreDestroy注解通常被认为是接收生命周期回调更好的实践。使用这些注解意味着bean不会与spring的接口耦合。更详细的信息请参考7.9.8 @PostConstruct和@PreDestroy。 
如果不想使用JSR-250的注解，但又不想不与spring耦合，可以在bean定义时使用init-method和destroy-method指定其初始化和销毁方法。

> The JSR-250 @PostConstruct and @PreDestroy annotations are generally considered best practice for receiving lifecycle callbacks in a modern Spring application. Using these annotations means that your beans are not coupled to Spring specific interfaces. For details see Section 7.9.8, “@PostConstruct and @PreDestroy”.

> If you don’t want to use the JSR-250 annotations but you are still looking to remove coupling consider the use of init-method and destroy-method object definition metadata.

spring内部使用BeanPostProcessor的实例处理它能找到的任何回调接口并调用合适的方法。如果需要定制一些spring未提供的又可以立即使用的功能或生命周期行为，可以直接实现自己的BeanPostProcessor 。更多信息请参考7.8 容器扩展点。

Internally, the Spring Framework uses BeanPostProcessor implementations to process any callback interfaces it can find and call the appropriate methods. If you need custom features or other lifecycle behavior Spring does not offer out-of-the-box, you can implement a BeanPostProcessor yourself. For more information, see Section 7.8, “Container Extension Points”.

除了初始化和销毁回调，spring管理的对象也可以实现Lifecycle接口，从而参与到容器本身的生命周期的启动和关闭中。

In addition to the initialization and destruction callbacks, Spring-managed objects may also implement the Lifecycle interface so that those objects can participate in the startup and shutdown process as driven by the container’s own lifecycle.

生命周期回调接口将在下面描述。

The lifecycle callback interfaces are described in this section.

__初始化回调__
Initialization callbacks

org.springframework.beans.factory.InitializingBean接口允许bean的所有必要属性都被容器设置好后执行初始化操作。InitializingBean接口只有一个方法：

The org.springframework.beans.factory.InitializingBean interface allows a bean to perform initialization work after all necessary properties on the bean have been set by the container. The InitializingBean interface specifies a single method:
```java
void afterPropertiesSet() throws Exception;
```
其实并不建议使用InitializingBean 接口因为没有必要把代码与spring耦合起来。可以使用@PostConstruct注解或在bean定义中指定初始化方法作为替代方案。在xml配置中，可以使用init-method属性指定一个无参的方法。在Java配置中，可以使用@Bean的initMethod属性，参考接收生命周期回调。例如，下面这种方式不会与spring耦合：

It is recommended that you do not use the InitializingBean interface because it unnecessarily couples the code to Spring. Alternatively, use the @PostConstruct annotation or specify a POJO initialization method. In the case of XML-based configuration metadata, you use the init-method attribute to specify the name of the method that has a void no-argument signature. With Java config, you use the initMethod attribute of @Bean, see the section called “Receiving lifecycle callbacks”. For example, the following:
```xml
<bean id="exampleInitBean" class="examples.ExampleBean" init-method="init"/>
```
```java
public class ExampleBean {

    public void init() {
        // do some initialization work
    }
}
```
上面这种方式严格来说与下面的方式是一样的：

…​is exactly the same as…​
```xml
<bean id="exampleInitBean" class="examples.AnotherExampleBean"/>
```
```java
public class AnotherExampleBean implements InitializingBean {

    public void afterPropertiesSet() {
        // do some initialization work
    }
}
```

but does not couple the code to Spring.

__销毁回调__
Destruction callbacks

实现了org.springframework.beans.factory.DisposableBean接口的bean允许在包含这的容器被销毁时获取一个回调。DisposableBean接口只有一个方法：

Implementing the org.springframework.beans.factory.DisposableBean interface allows a bean to get a callback when the container containing it is destroyed. The DisposableBean interface specifies a single method:
```java
void destroy() throws Exception;
```
其实并不建议使用DisposableBean回调接口，因为没必要把代码与spring耦合。可以使用@PreDestroy注解或在bean定义中指定销毁方法作为替代方案。在xml配置中，可以使用<bean/>元素的destroy-method属性。在Java配置中，可以使用@Bean的destroyMethod属性，参考接收生命周期回调。例如，下面这种方式不会与spring耦合：

It is recommended that you do not use the DisposableBean callback interface because it unnecessarily couples the code to Spring. Alternatively, use the @PreDestroy annotation or specify a generic method that is supported by bean definitions. With XML-based configuration metadata, you use the destroy-method attribute on the <bean/>. With Java config, you use the destroyMethod attribute of @Bean, see the section called “Receiving lifecycle callbacks”. For example, the following definition:
```xml
<bean id="exampleInitBean" class="examples.ExampleBean" destroy-method="cleanup"/>
```
```java
public class ExampleBean {

    public void cleanup() {
        // do some destruction work (like releasing pooled connections)
    }
}
```
上面这种方式严格来说与下面是一样的：

is exactly the same as:
```xml
<bean id="exampleInitBean" class="examples.AnotherExampleBean"/>
```
```java
public class AnotherExampleBean implements DisposableBean {

    public void destroy() {
        // do some destruction work (like releasing pooled connections)
    }
}
```
but does not couple the code to Spring.

[Tip]
> <bean>元素的destroy-method属性可以被赋予一个特殊的值“inferred”，它可以引导spring自动检测类中的公共close或shutdown方法（任何实现了java.lang.AutoCloseable和java.io.Closeable接口的类会匹配）。这个特殊的值“inferred”也可以设置在 <beans>元素的default-destroy-method属性上，这样会使所有的bean都执行这种行为（参考默认的初始化和销毁方法）。注意，这在Java配置中是默认行为。  
The destroy-method attribute of a <bean> element can be assigned a special (inferred) value which instructs Spring to automatically detect a public close or shutdown method on the specific bean class (any class that implements java.lang.AutoCloseable or java.io.Closeable would therefore match). This special (inferred) value can also be set on the default-destroy-method attribute of a <beans> element to apply this behavior to an entire set of beans (see the section called “Default initialization and destroy methods”). Note that this is the default behavior with Java config.

__默认的初始化和销毁方法__
Default initialization and destroy methods

当不使用spring指定的InitializingBean和DisposableBean回调接口编写初始化和销毁回调方法时，我们一般会把这些方法命名为init(), initialize(), dispose()等等。理想状态下，在整个项目中这些方法的名字都是标准化的，以便所有的开发者都使用相同的名字并保证一致性。

When you write initialization and destroy method callbacks that do not use the Spring-specific InitializingBean and DisposableBean callback interfaces, you typically write methods with names such as init(), initialize(), dispose(), and so on. Ideally, the names of such lifecycle callback methods are standardized across a project so that all developers use the same method names and ensure consistency.

可以配置spring容器在每个bean上自动寻找初始化和销毁的回调方法。这意味着，开发者可以直接使用init()作为初始化方法而不用为每一个bean配置init-method=”init”了。容器会在bean创建后调用这个方法（而且，这与标准的生命周期回调是一致的）。这个特性也需要为初始化和销毁方法定义统一的命名约定。

You can configure the Spring container to look for named initialization and destroy callback method names on every bean. This means that you, as an application developer, can write your application classes and use an initialization callback called init(), without having to configure an init-method="init" attribute with each bean definition. The Spring IoC container calls that method when the bean is created (and in accordance with the standard lifecycle callback contract described previously). This feature also enforces a consistent naming convention for initialization and destroy method callbacks.

假设你的初始化方法叫init()，销毁方法叫destroy()，那么你的类将与下面的类很相似：

Suppose that your initialization callback methods are named init() and destroy callback methods are named destroy(). Your class will resemble the class in the following example.
```xml
public class DefaultBlogService implements BlogService {

    private BlogDao blogDao;

    public void setBlogDao(BlogDao blogDao) {
        this.blogDao = blogDao;
    }

    // this is (unsurprisingly) the initialization callback method
    public void init() {
        if (this.blogDao == null) {
            throw new IllegalStateException("The [blogDao] property must be set.");
        }
    }
}
```
```xml
<beans default-init-method="init">

    <bean id="blogService" class="com.foo.DefaultBlogService">
        <property name="blogDao" ref="blogDao" />
    </bean>

</beans>
```

顶级元素<beans/>上的default-init-method属性会使得spring容器自动识别bean中的init方法作为初始化回调方法。当bean创建并装配后，如果它有这个方法就会在合适的时候被调用。

The presence of the default-init-method attribute on the top-level <beans/> element attribute causes the Spring IoC container to recognize a method called init on beans as the initialization method callback. When a bean is created and assembled, if the bean class has such a method, it is invoked at the appropriate time.

同样地，可以配置顶级元素<beans/>上的default-destroy-method属性声明销毁的回调方法。

You configure destroy method callbacks similarly (in XML, that is) by using the default-destroy-method attribute on the top-level <beans/> element.

当bean存在与上述约定不一样的回调方法名称时，可以使用<bean/>元素本身的init-method和destroy-method代替默认的方法。

Where existing bean classes already have callback methods that are named at variance with the convention, you can override the default by specifying (in XML, that is) the method name using the init-method and destroy-method attributes of the <bean/> itself.

spring容器保证在bean的所有依赖都注入完毕时立马调用初始化回调方法。因此，初始化方法是调用在原生（非原生即代理）的bean引用上，这意味着AOP的拦截器还没开始生效。目标bean首先被完全创建，然后AOP代理及其拦截器链才被应用。如果目标bean与代理是分开定义的，我们甚至可以绕过代理直接与原生bean进行交互。因此，在初始化方法上应用拦截器是矛盾的，因为，这样做会把目标bean的生命周期与它的代理或拦截器耦合在一起，在直接使用原生bean时就会有很奇怪的语法。

The Spring container guarantees that a configured initialization callback is called immediately after a bean is supplied with all dependencies. Thus the initialization callback is called on the raw bean reference, which means that AOP interceptors and so forth are not yet applied to the bean. A target bean is fully created first, then an AOP proxy (for example) with its interceptor chain is applied. If the target bean and the proxy are defined separately, your code can even interact with the raw target bean, bypassing the proxy. Hence, it would be inconsistent to apply the interceptors to the init method, because doing so would couple the lifecycle of the target bean with its proxy/interceptors and leave strange semantics when your code interacts directly to the raw target bean.

__组合使用生命周期机制__
Combining lifecycle mechanisms

从spring 2.5 开始，有三种方式可以控制bean的生命周期行为：InitializingBean和DisposableBean回调接口；自定义的init()和destroy()方法；@PostConstruct和@PreDestroy注解。可以混合使用这些方式来控制给定的bean。

As of Spring 2.5, you have three options for controlling bean lifecycle behavior: the InitializingBean and DisposableBean callback interfaces; custom init() and destroy() methods; and the @PostConstruct and @PreDestroy annotations. You can combine these mechanisms to control a given bean.

[Note]
> 如果一个bean上配置了多种方式，并且每一种方式都配置为不同的方法名称，那么每一个配置的方法将按下面的顺序执行。但是，如果配置的相同的方法名称，那么只会执行一次。  
If multiple lifecycle mechanisms are configured for a bean, and each mechanism is configured with a different method name, then each configured method is executed in the order listed below. However, if the same method name is configured - for example, init() for an initialization method - for more than one of these lifecycle mechanisms, that method is executed once, as explained in the preceding section.

同一个bean配置了多种方式，对于初始化方法将按以下顺序调用：

Multiple lifecycle mechanisms configured for the same bean, with different initialization methods, are called as follows:

* @PostConstruct注解的方法  
Methods annotated with @PostConstruct

* InitializingBean接口定义的afterPropertiesSet()方法  
afterPropertiesSet() as defined by the InitializingBean callback interface

* 自定义的init()方法  
A custom configured init() method

销毁方法也是同样的顺序：

Destroy methods are called in the same order:

* @PreDestroy注解的方法  
Methods annotated with @PreDestroy
* DisposableBean接口定义的destroy()方法  
destroy() as defined by the DisposableBean callback interface
* 自定义的destroy()方法  
A custom configured destroy() method

__启动和停止回调__
Startup and shutdown callbacks

Lifecycle接口为任何有自己生命周期需求的对象定义了一些基本的方法（例如，启动和停止一些后台进程）。

The Lifecycle interface defines the essential methods for any object that has its own lifecycle requirements (e.g. starts and stops some background process):
```java
public interface Lifecycle {

    void start();

    void stop();

    boolean isRunning();
}
```
所有spring管理的对象都可以实现这个接口。然后，当ApplicationContext接收到启动和停止的信号时，例如，运行时的stop/restart场景，它会级联调用此上下文内所有的Lifecycle实现。它是通过委托LifecycleProcessor实现的：

Any Spring-managed object may implement that interface. Then, when the ApplicationContext itself receives start and stop signals, e.g. for a stop/restart scenario at runtime, it will cascade those calls to all Lifecycle implementations defined within that context. It does this by delegating to a LifecycleProcessor:
```java
public interface LifecycleProcessor extends Lifecycle {

    void onRefresh();

    void onClose();
}
```
注意，LifecycleProcessor本身扩展了Lifecycle接口，同时添加了两个另外的方法用于在上下文刷新和关闭时做出响应。

Notice that the LifecycleProcessor is itself an extension of the Lifecycle interface. It also adds two other methods for reacting to the context being refreshed and closed.

[Tip]
> 注意，org.springframework.context.Lifecycle接口只能显式地接收启动和停止的通知，而不会在因上下文刷新导致自动启动时接收到通知。考虑使用org.springframework.context.SmartLifecycle接口实现更细粒度的控制，它可以检测到自动启动（当然，也包括启动阶段）。同样地，请注意停止通知并不保证在销毁之前来临：正常停止时，所有的Lifecycle bean将会在销毁回调前首先接收到停止通知，在上下文生命周期内的热刷新或强制刷新，仅仅销毁方法会被调用。

> Note that the regular org.springframework.context.Lifecycle interface is just a plain contract for explicit start/stop notifications and does NOT imply auto-startup at context refresh time. Consider implementing org.springframework.context.SmartLifecycle instead for fine-grained control over auto-startup of a specific bean (including startup phases). Also, please note that stop notifications are not guaranteed to come before destruction: On regular shutdown, all Lifecycle beans will first receive a stop notification before the general destruction callbacks are being propagated; however, on hot refresh during a context’s lifetime or on aborted refresh attempts, only destroy methods will be called.

启动和停止的调用顺序可能是很重要的。如果两个对象存在依赖关系，那么依赖方将在其依赖启动之后启动，并在其依赖停止之前停止。但是，有些时候依赖并不是直接的。你可能只知道一些特定类型的对象将在另外一些类型的对象之前启动。在这种情形中，SmartLifecycle接口有另外一种选择，它的父接口Phased中定义了一个getPhase()方法。

The order of startup and shutdown invocations can be important. If a "depends-on" relationship exists between any two objects, the dependent side will start after its dependency, and it will stop before its dependency. However, at times the direct dependencies are unknown. You may only know that objects of a certain type should start prior to objects of another type. In those cases, the SmartLifecycle interface defines another option, namely the getPhase() method as defined on its super-interface, Phased.
```java
public interface Phased {

    int getPhase();
}
public interface SmartLifecycle extends Lifecycle, Phased {

    boolean isAutoStartup();

    void stop(Runnable callback);
}
```
启动时，低相位（phase）的对象先启动；停止时，顺序反过来。因此，实现了SmartLifecycle接口并且getPahse()返回Integer.MIN_VALUE的对象将先启动后停止。反过来，带有Integer.MAX_VALUE相位的对象将会后启动先停止（可能是因为它依赖于其它进程运行）。对于那些正常的未实现SmartLifecycle接口的Lifecycle对象，它们的相位默认值为0。因此，负相位的对象将在这些正常对象启动之前启动，停止之后停止，反之亦然。

When starting, the objects with the lowest phase start first, and when stopping, the reverse order is followed. Therefore, an object that implements SmartLifecycle and whose getPhase() method returns Integer.MIN_VALUE would be among the first to start and the last to stop. At the other end of the spectrum, a phase value of Integer.MAX_VALUE would indicate that the object should be started last and stopped first (likely because it depends on other processes to be running). When considering the phase value, it’s also important to know that the default phase for any "normal" Lifecycle object that does not implement SmartLifecycle would be 0. Therefore, any negative phase value would indicate that an object should start before those standard components (and stop after them), and vice versa for any positive phase value.

可以发现SmartLifecycle中的stop方法可以接受一个回调。任何实现必须在其停止线程完成以后调用回调（Runnable）的run()方法。在必须的时候可以异步停止，因为LifecycleProcessor接口默认的实现DefaultLifecycleProcessor会等到超时直到每个相位的对象都调用了那个回调。默认每个相位的超时时间是30秒。可以在上下文中定义一个名叫“lifecycleProcessor”的bean重写默认的生命周期处理器。如果仅仅只修改超时时间，那么下面的定义就足够了：

As you can see the stop method defined by SmartLifecycle accepts a callback. Any implementation must invoke that callback’s run() method after that implementation’s shutdown process is complete. That enables asynchronous shutdown where necessary since the default implementation of the LifecycleProcessor interface, DefaultLifecycleProcessor, will wait up to its timeout value for the group of objects within each phase to invoke that callback. The default per-phase timeout is 30 seconds. You can override the default lifecycle processor instance by defining a bean named "lifecycleProcessor" within the context. If you only want to modify the timeout, then defining the following would be sufficient:
```xml
<bean id="lifecycleProcessor" class="org.springframework.context.support.DefaultLifecycleProcessor">
    <!-- timeout value in milliseconds -->
    <property name="timeoutPerShutdownPhase" value="10000"/>
</bean>
```
如前所述，LifecycleProcessor接口也定义了上下文刷新（onRefresh()）和关闭（onClose()）的方法。后者会简单地驱动关闭进程即使stop()方法被显式地调用了，但是它会发生在上下文关闭的时候。另一方面，刷新方法会带来SmartLifecycle bean的另一个功能。在上下文被刷新时（在所有对象都被实例化和初始化之后），这个方法会被调用，并且那时默认的生命周期处理器会检查每个SmartLifecycle对象的isAutoStartup()方法返回的布尔值。如果为true，那么这个对象会在那时被启动而不会等待上下文显式地调用或它自己的start()方法（不像上下文的刷新，标准的上下文实现并不会自动启动）。相位的值及依赖关系将会按与上面描述相同的方式检查启动顺序。

As mentioned, the LifecycleProcessor interface defines callback methods for the refreshing and closing of the context as well. The latter will simply drive the shutdown process as if stop() had been called explicitly, but it will happen when the context is closing. The 'refresh' callback on the other hand enables another feature of SmartLifecycle beans. When the context is refreshed (after all objects have been instantiated and initialized), that callback will be invoked, and at that point the default lifecycle processor will check the boolean value returned by each SmartLifecycle object’s isAutoStartup() method. If "true", then that object will be started at that point rather than waiting for an explicit invocation of the context’s or its own start() method (unlike the context refresh, the context start does not happen automatically for a standard context implementation). The "phase" value as well as any "depends-on" relationships will determine the startup order in the same way as described above.

在非web应用中优雅地关闭spring的IoC容器
Shutting down the Spring IoC container gracefully in non-web applications

[Note]
> 这节只针对非web应用。spring中基于web的ApplicationContext的实现已经可以优雅地关闭容器了当相关的web应用停止时。  
This section applies only to non-web applications. Spring’s web-based ApplicationContext implementations already have code in place to shut down the Spring IoC container gracefully when the relevant web application is shut down.

如果在非web应用环境中使用spring的IoC容器，例如，在富客户端桌面环境中，注册一个JVM的关闭钩子（shutdown hook）。这样做可以保证优雅地关闭并调用相关单例bean的销毁方法从而使得所有的资源都被释放。当然，你必须要正确地配置并实现这些销毁方法。

If you are using Spring’s IoC container in a non-web application environment; for example, in a rich client desktop environment; you register a shutdown hook with the JVM. Doing so ensures a graceful shutdown and calls the relevant destroy methods on your singleton beans so that all resources are released. Of course, you must still configure and implement these destroy callbacks correctly.

为了注册一个关闭钩子，调用ConfigurableApplicationContext接口的registerShutdownHook()方法。

To register a shutdown hook, you call the registerShutdownHook() method that is declared on the ConfigurableApplicationContext interface:
```java
import org.springframework.context.ConfigurableApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;

public final class Boot {

    public static void main(final String[] args) throws Exception {
        ConfigurableApplicationContext ctx = new ClassPathXmlApplicationContext("beans.xml");

        // add a shutdown hook for the above context...
        ctx.registerShutdownHook();

        // app runs here...

        // main method exits, hook is called prior to the app shutting down...
    }
}
```

### 7.6.2 ApplicationContextAware and BeanNameAware

当ApplicationContext创建了一个实现了org.springframework.context.ApplicationContextAware接口的对象实例时，这个实例就拥有了ApplicationContext的引用。

When an ApplicationContext creates an object instance that implements the org.springframework.context.ApplicationContextAware interface, the instance is provided with a reference to that ApplicationContext.
```java
public interface ApplicationContextAware {

    void setApplicationContext(ApplicationContext applicationContext) throws BeansException;
}
```
因此，bean可以编程式地操作创建了它的ApplicationContext，可以通过ApplicationContext接口，也可以把它强制转换成它的子类，比如ConfigurableApplicationContext，这样可以暴露更多的功能。一种使用方式是提取其它bean。有时这个能力很有用，但是，通常来说应该避免这样操作，因为这会使得代码与spring耦合，并且不符合控制反转的原则，而控制反转中合作者是以属性的方式提供的。ApplicationContext还提供了一些诸如访问文件资源、发布应用事件、访问消息源的方法。关于附加功能的描述请参考7.15 ApplicationContext的附加功能。

Thus beans can manipulate programmatically the ApplicationContext that created them, through the ApplicationContext interface, or by casting the reference to a known subclass of this interface, such as ConfigurableApplicationContext, which exposes additional functionality. One use would be the programmatic retrieval of other beans. Sometimes this capability is useful; however, in general you should avoid it, because it couples the code to Spring and does not follow the Inversion of Control style, where collaborators are provided to beans as properties. Other methods of the ApplicationContext provide access to file resources, publishing application events, and accessing a MessageSource. These additional features are described in Section 7.15, “Additional capabilities of the ApplicationContext”

从spring 2.5 开始，自动装配就是另外一种获取ApplicationContext引用的方式。传统的constructor和byType装配模式（参考7.4.5 自动装配合作者）能够分别为构造方法参数或setter方法参数提供一个ApplicationContext类型的依赖。另外，也可以使用注解方式自动装配字段或方法参数。如果这样做了，ApplicationContext就会被注入到一个字段、构造方法参数或者其它需要ApplicationContext类型的方法参数，当然这些字段或参数得带有@Autowired注解。更多信息请参考7.9.2 @Autowired。

As of Spring 2.5, autowiring is another alternative to obtain reference to the ApplicationContext. The "traditional" constructor and byType autowiring modes (as described in Section 7.4.5, “Autowiring collaborators”) can provide a dependency of type ApplicationContext for a constructor argument or setter method parameter, respectively. For more flexibility, including the ability to autowire fields and multiple parameter methods, use the new annotation-based autowiring features. If you do, the ApplicationContext is autowired into a field, constructor argument, or method parameter that is expecting the ApplicationContext type if the field, constructor, or method in question carries the @Autowired annotation. For more information, see Section 7.9.2, “@Autowired”.

当ApplicationContext创建了一个实现了org.springframework.beans.factory.BeanNameAware接口的类时，这个类就拥有了一个定义在它关联的对象定义中的名字的引用。

When an ApplicationContext creates a class that implements the org.springframework.beans.factory.BeanNameAware interface, the class is provided with a reference to the name defined in its associated object definition.
```java
public interface BeanNameAware {

    void setBeanName(String name) throws BeansException;
}
```
这个方法会在操作了正常的属性之后但是在初始化方法之前运行，不管是InitializingBean的afterPropertiesSet()方法还是自定义的init()方法

The callback is invoked after population of normal bean properties but before an initialization callback such as InitializingBean afterPropertiesSet or a custom init-method.

### 7.6.3 其它Aware接口
7.6.3 Other Aware interfaces

除了上面讨论的ApplicationContextAware和BeanNameAware接口，spring还提供了一系列的Aware接口使得bean可以从容器获取需要的资源（依赖）。大部分重要的Aware接口汇总在下面了——通常，它们的名字已经可以很好地解释它们的依赖类型了。

Besides ApplicationContextAware and BeanNameAware discussed above, Spring offers a range of Aware interfaces that allow beans to indicate to the container that they require a certain infrastructure dependency. The most important Aware interfaces are summarized below - as a general rule, the name is a good indication of the dependency type:

__表7.4 Aware接口__
Table 7.4. Aware interfaces

Name |	Injected Dependency |	Explained in…​
--|--|--
ApplicationContextAware | Declaring ApplicationContext  声明ApplicationContext | Section 7.6.2, “ApplicationContextAware and BeanNameAware”
ApplicationEventPublisherAware| Event publisher of the enclosing ApplicationContext 封闭的ApplicationContext的事件发布者 | Section 7.15, “Additional capabilities of the ApplicationContext”
BeanClassLoaderAware | Class loader used to load the bean classes. | Section 7.3.2, “Instantiating beans”
BeanFactoryAware | Declaring BeanFactory | Section 7.6.2, “ApplicationContextAware and BeanNameAware”
BeanNameAware| Name of the declaring bean | Section 7.6.2, “ApplicationContextAware and BeanNameAware”
BootstrapContextAware | Resource adapter BootstrapContext the container runs in. Typically available only in JCA aware ApplicationContexts | Chapter 32, JCA CCI
LoadTimeWeaverAware | Defined weaver for processing class definition at load time |Section 11.8.4, “Load-time weaving with AspectJ in the Spring Framework”
MessageSourceAware |Configured strategy for resolving messages (with support for parametrization and internationalization) |Section 7.15, “Additional capabilities of the ApplicationContext”
NotificationPublisherAware |Spring JMX notification publisher |Section 31.7, “Notifications”
PortletConfigAware | Current PortletConfig the container runs in. Valid only in a web-aware Spring ApplicationContext | Chapter 25, Portlet MVC Framework
PortletContextAware | Current PortletContext the container runs in. Valid only in a web-aware Spring ApplicationContext |Chapter 25, Portlet MVC Framework
ResourceLoaderAware | Configured loader for low-level access to resources| Chapter 8, Resources
ServletConfigAware | Current ServletConfig the container runs in. Valid only in a web-aware Spring ApplicationContext | Chapter 22, Web MVC framework
ServletContextAware | Current ServletContext the container runs in. Valid only in a web-aware Spring ApplicationContext| Chapter 22, Web MVC framework

再次注意，使用这些接口将使你的代码与spring的API耦合，并且不会遵循控制反转的原则。因此，他们被推荐用于需要编程式访问容器的基础设施bean。

Note again that usage of these interfaces ties your code to the Spring API and does not follow the Inversion of Control style. As such, they are recommended for infrastructure beans that require programmatic access to the container.

## 7.7 bean定义的继承
7.7 Bean definition inheritance

bean的定义中可以包含大量的配置信息，包括构造方法参数、属性值以及容器指定的信息，比如初始化方法、静态工厂方法名等等。子bean定义可以从父定义中继承配置信息。子定义可以重写这些值，也可以在需要的时候添加新的。使用父子定义可以少码几个字，这是模板的一种形式。

A bean definition can contain a lot of configuration information, including constructor arguments, property values, and container-specific information such as initialization method, static factory method name, and so on. A child bean definition inherits configuration data from a parent definition. The child definition can override some values, or add others, as needed. Using parent and child bean definitions can save a lot of typing. Effectively, this is a form of templating.

如果编程式地使用ApplicationContext，子定义可以使用ChildBeanDefinition类。不过大部分用户并不这样使用，而是以类似ClassPathXmlApplicationContext的形式声明式地配置。在xml中，可以使用子定义的parent属性指定父bean。

If you work with an ApplicationContext interface programmatically, child bean definitions are represented by the ChildBeanDefinition class. Most users do not work with them on this level, instead configuring bean definitions declaratively in something like the ClassPathXmlApplicationContext. When you use XML-based configuration metadata, you indicate a child bean definition by using the parent attribute, specifying the parent bean as the value of this attribute.
```xml
<bean id="inheritedTestBean" abstract="true"
        class="org.springframework.beans.TestBean">
    <property name="name" value="parent"/>
    <property name="age" value="1"/>
</bean>

<bean id="inheritsWithDifferentClass"
        class="org.springframework.beans.DerivedTestBean"
        parent="inheritedTestBean" init-method="initialize">
    <property name="name" value="override"/>
    <!-- the age property value of 1 will be inherited from parent -->
</bean>
```
如果没有指定子定义的class，则会使用父定义的class，不过也可以重写它。后者必须保证子定义中的class与父定义中的class兼容，即它可以接受父定义中的属性值。

A child bean definition uses the bean class from the parent definition if none is specified, but can also override it. In the latter case, the child bean class must be compatible with the parent, that is, it must accept the parent’s property values.

子定义可以从父定义继承作用域、构造方法参数值、属性值以及方法，也可以为它们添加新值。任何在子定义中定义的作用域、初始化方法、销毁方法或者静态工厂方法都会覆盖父定义中的配置。

A child bean definition inherits scope, constructor argument values, property values, and method overrides from the parent, with the option to add new values. Any scope, initialization method, destroy method, and/or static factory method settings that you specify will override the corresponding parent settings.

其余的配置总是取决于子定义：依赖、自动装配模式、依赖检查、单例、延迟初始化。

The remaining settings are always taken from the child definition: depends on, autowire mode, dependency check, singleton, lazy init.

上面的例子中在父定义中显式地使用了abstract属性。如果父定义不指定类，那么必须显式地标记abstract属性，如下：

The preceding example explicitly marks the parent bean definition as abstract by using the abstract attribute. If the parent definition does not specify a class, explicitly marking the parent bean definition as abstract is required, as follows:
```xml
<bean id="inheritedTestBeanWithoutClass" abstract="true">
    <property name="name" value="parent"/>
    <property name="age" value="1"/>
</bean>

<bean id="inheritsWithClass" class="org.springframework.beans.DerivedTestBean"
        parent="inheritedTestBeanWithoutClass" init-method="initialize">
    <property name="name" value="override"/>
    <!-- age will inherit the value of 1 from the parent bean definition-->
</bean>
```
父bean不能被实例化，因为它本身并不完整，并且被显式地标记为abstract了。当一个bean被定义为abstract，则它只能用做定义子bean的模板。如果试着使用abstract的父bean，比如通过另一个bean引用它或调用getBean()方法获取它，都会报错。同样，容器内部的preInstantiateSingletons()方法也会忽略定义为abstract的bean。

The parent bean cannot be instantiated on its own because it is incomplete, and it is also explicitly marked as abstract. When a definition is abstract like this, it is usable only as a pure template bean definition that serves as a parent definition for child definitions. Trying to use such an abstract parent bean on its own, by referring to it as a ref property of another bean or doing an explicit getBean() call with the parent bean id, returns an error. Similarly, the container’s internal preInstantiateSingletons() method ignores bean definitions that are defined as abstract.

[Note]
> ApplicationContext默认预实例化所有的单例。因此，如果有一个仅用于模板的bean，并且指定了类，请保证把abstract属性设置为true，否则容器会尝试预实例化它。

> ApplicationContext pre-instantiates all singletons by default. Therefore, it is important (at least for singleton beans) that if you have a (parent) bean definition which you intend to use only as a template, and this definition specifies a class, you must make sure to set the abstract attribute to true, otherwise the application context will actually (attempt to) pre-instantiate the abstract bean.

## 7.8 容器扩展点
7.8 Container Extension Points

通常，开发者不需要自己实现ApplicationContext。然而，Spring提供了一些特殊的集成接口，实现这些接口可以扩展Spring。下面几个章节将介绍这些集成接口。

Typically, an application developer does not need to subclass ApplicationContext implementation classes. Instead, the Spring IoC container can be extended by plugging in implementations of special integration interfaces. The next few sections describe these integration interfaces.

### 7.8.1 使用BeanPostProcessor自定义bean
7.8.1 Customizing beans using a BeanPostProcessor

可以实现BeanPostProcessor接口定义的回调方法，从而提供自己的实例化逻辑、依赖注入逻辑等。如果想要在spring容器完成了实例化、配置及初始化bean前后实现自定义的逻辑，那么可以插入一个或多个BeanPostProcessor的实现。

The BeanPostProcessor interface defines callback methods that you can implement to provide your own (or override the container’s default) instantiation logic, dependency-resolution logic, and so forth. If you want to implement some custom logic after the Spring container finishes instantiating, configuring, and initializing a bean, you can plug in one or more BeanPostProcessor implementations.

可以配置多重BeanPostProcessor实例，并且可以设置它们的order属性控制它们的执行顺序，不过只有当这些实例也实现了Ordered接口才可以。更多信息请参考BeanPostProcessor和Ordered接口的javadoc，也可以参考下面关于BeanPostProcessor的编程式注册的说明。

You can configure multiple BeanPostProcessor instances, and you can control the order in which these BeanPostProcessors execute by setting the order property. You can set this property only if the BeanPostProcessor implements the Ordered interface; if you write your own BeanPostProcessor you should consider implementing the Ordered interface too. For further details, consult the javadocs of the BeanPostProcessor and Ordered interfaces. See also the note below on programmatic registration of BeanPostProcessors.

[Note]
> BeanPostProcessor操作对象实例，也就是说IoC容器实例化bean实例后BeanPostProcessor才工作。  

> BeanPostProcessors operate on bean (or object) instances; that is to say, the Spring IoC container instantiates a bean instance and then BeanPostProcessors do their work.

> BeanPostProcessor是每个容器作用域的，即使使用容器继承也不行。声明在一个容器内的BeanPostProcessor只能处理那个容器内的bean。换句话说，一个容器内的bean不会被另一个容器的BeanPostProcessor处理，即使两个容器处于同一继承体系中。 
> BeanPostProcessors are scoped per-container. This is only relevant if you are using container hierarchies. If you define a BeanPostProcessor in one container, it will only post-process the beans in that container. In other words, beans that are defined in one container are not post-processed by a BeanPostProcessor defined in another container, even if both containers are part of the same hierarchy.

> 为了改变实际的bean定义，需要使用BeanFactoryPostProcessor，参考7.8.2 使用BeanFactoryPostProcessor自定义配置元数据。
> To change the actual bean definition (i.e., the blueprint that defines the bean), you instead need to use a BeanFactoryPostProcessor as described in Section 7.8.2, “Customizing configuration metadata with a BeanFactoryPostProcessor”.

org.springframework.beans.factory.config.BeanPostProcessor接口包含两个回调方法。如果容器中一个类被注册为后处理器，那么对于容器创建的每一个bean实例，在它们初始化前后都会获得这个后处理器的一个回调。这个后处理器可以对bean实例采取任何行动，也可以完全忽略回调。一个bean后处理器一般用于检查回调接口或用代理包装bean。一些Spring的AOP基础类为了提供代理包装的逻辑都被实现为后处理器。

（译者注：此处要理解实例化（instantiation）和初始化（initialization）的区别，bean是先实例化再初始化的，后（置）处理器是针对实例化来说的，它是在bean实例化后起作用，但是它内部的两个方法分别在bean初始化前后执行拦截的作用）

The org.springframework.beans.factory.config.BeanPostProcessor interface consists of exactly two callback methods. When such a class is registered as a post-processor with the container, for each bean instance that is created by the container, the post-processor gets a callback from the container both before container initialization methods (such as InitializingBean’s afterPropertiesSet() and any declared init method) are called as well as after any bean initialization callbacks. The post-processor can take any action with the bean instance, including ignoring the callback completely. A bean post-processor typically checks for callback interfaces or may wrap a bean with a proxy. Some Spring AOP infrastructure classes are implemented as bean post-processors in order to provide proxy-wrapping logic.

ApplicationContext会自动检测所有实现了BeanPostProcessor接口的所有bean，接着把它们注册为后处理器，以便在其它bean创建的时候调用它们。后处理器bean可以像容器中其它bean一样部署。

An ApplicationContext automatically detects any beans that are defined in the configuration metadata which implement the BeanPostProcessor interface. The ApplicationContext registers these beans as post-processors so that they can be called later upon bean creation. Bean post-processors can be deployed in the container just like any other beans.

注意，当使用@Bean工厂方法配置一个BeanPostProcessor实现类时，这个工厂方法的返回值类型必须是这个实现类本身或至少得是org.springframework.beans.factory.config.BeanPostProcessor接口，清晰地表明这个bean是后处理器的本性。否则，ApplicationContext在完全创建它之前不能通过类型检测到它。因为BeanPostProcessor需要早点被实例化以便应用到其它bean的初始化中，所以早期的类型检测是必要的。

Note that when declaring a BeanPostProcessor using an @Bean factory method on a configuration class, the return type of the factory method should be the implementation class itself or at least the org.springframework.beans.factory.config.BeanPostProcessor interface, clearly indicating the post-processor nature of that bean. Otherwise, the ApplicationContext won’t be able to autodetect it by type before fully creating it. Since a BeanPostProcessor needs to be instantiated early in order to apply to the initialization of other beans in the context, this early type detection is critical.

[Note]
> 虽然我们推荐使用ApplicationContext自动检测BeanPostProcessor，但是也可以使用ConfigurableBeanFactory的addBeanPostProcessor方法编程式地注册。这在注册之前处理条件逻辑或者在同一继承体系上下文之间复制后处理器很有用。注意，编程式注册的BeanPostProcessor并不遵从Ordered接口的顺序，那是因为它们注册的顺序就是它们执行的顺序。同样地，编程式注册的BeanPostProcessor总是在通过自动检测注册的BeanPostProcessor之前执行。

> While the recommended approach for BeanPostProcessor registration is through ApplicationContext auto-detection (as described above), it is also possible to register them programmatically against a ConfigurableBeanFactory using the addBeanPostProcessor method. This can be useful when needing to evaluate conditional logic before registration, or even for copying bean post processors across contexts in a hierarchy. Note however that BeanPostProcessors added programmatically do not respect the Ordered interface. Here it is the order of registration that dictates the order of execution. Note also that BeanPostProcessors registered programmatically are always processed before those registered through auto-detection, regardless of any explicit ordering.

[Note]
> 实现了BeanPostProcessor接口的类是很特殊的，并且会被容器特殊对待。所有BeanPostProcessor和它们直接引用的bean会在启动时实例化，且作为ApplicationContext启动的特殊部分。然后，所有的BeanPostProcessor按顺序注册并应用到所有后来的bean上。因为AOP的自动代理也被实现为BeanPostProcessor本身，所以无论是BeanPostProcessor还是它们直接引用的bean都有获得自动代理的资格，且不会有切面织入它们。对于这些bean，你应该会看到一条信息日志消息：“Bean foo is not eligible for getting processed by all BeanPostProcessor interfaces (for example: not eligible for auto-proxying)”。注意，如果有bean通过自动装配或@Resource（可能会变成自动装配）被装配到了BeanPostProcessor，Spring在通过类型匹配查找依赖时可能会访问非预期的bean，从而使它们具有自动代理或其它后处理的资格。例如，如果通过不带name属性的@Resource注解在一个字段或setter方法上声明了一个依赖，并且这个字段或setter方法的名字并不直接与一个bean的名字通信，然后Spring将通过类型去匹配它们。

> Classes that implement the BeanPostProcessor interface are special and are treated differently by the container. All BeanPostProcessors and beans that they reference directly are instantiated on startup, as part of the special startup phase of the ApplicationContext. Next, all BeanPostProcessors are registered in a sorted fashion and applied to all further beans in the container. Because AOP auto-proxying is implemented as a BeanPostProcessor itself, neither BeanPostProcessors nor the beans they reference directly are eligible for auto-proxying, and thus do not have aspects woven into them.

> For any such bean, you should see an informational log message: "Bean foo is not eligible for getting processed by all BeanPostProcessor interfaces (for example: not eligible for auto-proxying)".

> Note that if you have beans wired into your BeanPostProcessor using autowiring or @Resource (which may fall back to autowiring), Spring might access unexpected beans when searching for type-matching dependency candidates, and therefore make them ineligible for auto-proxying or other kinds of bean post-processing. For example, if you have a dependency annotated with @Resource where the field/setter name does not directly correspond to the declared name of a bean and no name attribute is used, then Spring will access other beans for matching them by type.

下面的例子展示了如何在ApplicationContext中编写、注册并使用BeanPostProcessor。

The following examples show how to write, register, and use BeanPostProcessors in an ApplicationContext.

__例子，BeanPostProcessor风格的Hello World__
Example: Hello World, BeanPostProcessor-style

第一个例子展示了基本用法，它显示了一个自定义的BeanPostProcessor实现在bean在被创建时调用它们的toString()方法并把结果打印到控制台。


This first example illustrates basic usage. The example shows a custom BeanPostProcessor implementation that invokes the toString() method of each bean as it is created by the container and prints the resulting string to the system console.

请看下面自定义的BeanPostProcessor实现类的定义：

Find below the custom BeanPostProcessor implementation class definition:
```java
package scripting;

import org.springframework.beans.factory.config.BeanPostProcessor;

public class InstantiationTracingBeanPostProcessor implements BeanPostProcessor {

    // simply return the instantiated bean as-is
    public Object postProcessBeforeInitialization(Object bean, String beanName) {
        return bean; // we could potentially return any object reference here...
    }

    public Object postProcessAfterInitialization(Object bean, String beanName) {
        System.out.println("Bean '" + beanName + "' created : " + bean.toString());
        return bean;
    }
}
```
```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns:lang="http://www.springframework.org/schema/lang"
    xsi:schemaLocation="http://www.springframework.org/schema/beans
        http://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/lang
        http://www.springframework.org/schema/lang/spring-lang.xsd">

    <lang:groovy id="messenger"
            script-source="classpath:org/springframework/scripting/groovy/Messenger.groovy">
        <lang:property name="message" value="Fiona Apple Is Just So Dreamy."/>
    </lang:groovy>

    <!--
    when the above bean (messenger) is instantiated, this custom
    BeanPostProcessor implementation will output the fact to the system console
    -->
    <bean class="scripting.InstantiationTracingBeanPostProcessor"/>

</beans>
```
从上面可以看到，InstantiationTracingBeanPostProcessor是如此简单的定义。它甚至不需要一个名字，而且因为它是一个bean，它也可以像其它bean一样被依赖注入。（上面的例子也展示了如何通过Groovy脚本语言定义一个bean。关于Spring的动态语言支持的详细描述请参考35 动态语言支持。）

Notice how the InstantiationTracingBeanPostProcessor is simply defined. It does not even have a name, and because it is a bean it can be dependency-injected just like any other bean. (The preceding configuration also defines a bean that is backed by a Groovy script. The Spring dynamic language support is detailed in the chapter entitled Chapter 35, Dynamic language support.)

下面简单展示了Java应用如何执行前面的代码和配置：

The following simple Java application executes the preceding code and configuration:
```java
import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;
import org.springframework.scripting.Messenger;

public final class Boot {

    public static void main(final String[] args) throws Exception {
        ApplicationContext ctx = new ClassPathXmlApplicationContext("scripting/beans.xml");
        Messenger messenger = (Messenger) ctx.getBean("messenger");
        System.out.println(messenger);
    }

}
```
上面应用的输出看起来像下面这样

The output of the preceding application resembles the following:
```java
Bean 'messenger' created : org.springframework.scripting.groovy.GroovyMessenger@272961
org.springframework.scripting.groovy.GroovyMessenger@272961
```

__Example: The RequiredAnnotationBeanPostProcessor__

使用回调接口、注解或自定义的BeanPostProcessor实现类是一种扩展Spring容器的常用方式。Spring的RequiredAnnotationBeanPostProcessor是其中一个例子——它可以保证bean中被注解@Required标记的属性能被依赖注入一个值。

Using callback interfaces or annotations in conjunction with a custom BeanPostProcessor implementation is a common means of extending the Spring IoC container. An example is Spring’s RequiredAnnotationBeanPostProcessor - a BeanPostProcessor implementation that ships with the Spring distribution which ensures that JavaBean properties on beans that are marked with an (arbitrary) annotation are actually (configured to be) dependency-injected with a value.

### 7.8.2 使用BeanFactoryPostProcessor自定义配置元数据
7.8.2 Customizing configuration metadata with a BeanFactoryPostProcessor

接下来我们看的一个扩展点是org.springframework.beans.factory.config.BeanFactoryPostProcessor。这个接口的语法与BeanPostProcessor类型，主要区别是：BeanFactoryPostProcessor操作的是配置元数据，也就是说，Spring容器允许BeanFactoryPostProcessor读取配置元数据并可能在容器实例化这些bean之前改变它们，当然，BeanFactoryPostProcessor除外。

The next extension point that we will look at is the org.springframework.beans.factory.config.BeanFactoryPostProcessor. The semantics of this interface are similar to those of the BeanPostProcessor, with one major difference: BeanFactoryPostProcessor operates on the bean configuration metadata; that is, the Spring IoC container allows a BeanFactoryPostProcessor to read the configuration metadata and potentially change it before the container instantiates any beans other than BeanFactoryPostProcessors.

可以配置多重BeanFactoryPostProcessor，也可以通过设置它们的order属性控制它们的执行顺序，当然，只有实现了Ordered接口才能设置顺序。更多BeanFactoryPostProcessor和Ordered接口的详细信息请参考javadoc。

You can configure multiple BeanFactoryPostProcessors, and you can control the order in which these BeanFactoryPostProcessors execute by setting the order property. However, you can only set this property if the BeanFactoryPostProcessor implements the Ordered interface. If you write your own BeanFactoryPostProcessor, you should consider implementing the Ordered interface too. Consult the javadocs of the BeanFactoryPostProcessor and Ordered interfaces for more details.

[Note]
> 如果想改变实际的bean实例，使用BeanPostProcessor即可。尽管在技术上使用BeanFactoryPostProcessor也是可以的，但是这样做会导致不成熟的bean实例化，违背了标准的容器生命周期，并可能导致负面影响，比如绕过bean的后置处理。 
> If you want to change the actual bean instances (i.e., the objects that are created from the configuration metadata), then you instead need to use a BeanPostProcessor (described above in Section 7.8.1, “Customizing beans using a BeanPostProcessor”). While it is technically possible to work with bean instances within a BeanFactoryPostProcessor (e.g., using BeanFactory.getBean()), doing so causes premature bean instantiation, violating the standard container lifecycle. This may cause negative side effects such as bypassing bean post processing.

> 同样地，BeanFactoryPostProcessor也是每个容器作用域的，即使在容器继承体系中也是如此。在一个容器中定义的BeanFactoryPostProcessor，只能作用于这个容器的bean定义，不能处理另一个容器的bean定义，即使两个容器羽毛球同一个继承的体系也不可以。
> Also, BeanFactoryPostProcessors are scoped per-container. This is only relevant if you are using container hierarchies. If you define a BeanFactoryPostProcessor in one container, it will only be applied to the bean definitions in that container. Bean definitions in one container will not be post-processed by BeanFactoryPostProcessors in another container, even if both containers are part of the same hierarchy.

ApplicationContext中定义的BeanFactoryPostProcessor是自动执行的，以便作用于这个容器中定义的元数据。Spring包含很多预先定义好的BeanFactoryPostProcessor，比如PropertyOverrideConfigure和PropertyPlaceholderConfigure。也可以使用自定义的BeanFactoryPostProcessor，比如，用于注册自定义的属性编辑器。

A bean factory post-processor is executed automatically when it is declared inside an ApplicationContext, in order to apply changes to the configuration metadata that define the container. Spring includes a number of predefined bean factory post-processors, such as PropertyOverrideConfigurer and PropertyPlaceholderConfigurer. A custom BeanFactoryPostProcessor can also be used, for example, to register custom property editors.

ApplicationContext会自动检测所有实现了BeanFactoryPostProcessor接口的bean，并在适当的时候使用这些bean作为bean工厂后置处理器。可以像部署其它bean一样部署这些后置处理器。

An ApplicationContext automatically detects any beans that are deployed into it that implement the BeanFactoryPostProcessor interface. It uses these beans as bean factory post-processors, at the appropriate time. You can deploy these post-processor beans as you would any other bean.

[Note]
> 与BeanPostProcessor一样，一般不会配置BeanFactoryPostProcessor延迟初始化。如果没有别的bean引用一个Bean(Factory)PostProcessor，那这个后置处理器不会被实例化。因此，设置延迟初始化会被忽略，并且Bean(Factory)PostProcessor会自动初始化，即使在<beans/>元素设置default-lazy-init为true也没用。
> As with BeanPostProcessors , you typically do not want to configure BeanFactoryPostProcessors for lazy initialization. If no other bean references a Bean(Factory)PostProcessor, that post-processor will not get instantiated at all. Thus, marking it for lazy initialization will be ignored, and the Bean(Factory)PostProcessor will be instantiated eagerly even if you set the default-lazy-init attribute to true on the declaration of your <beans /> element.

__Example: the Class name substitution PropertyPlaceholderConfigurer__
例子：类名替代PropertyPlaceholderConfigurer

如果使用标准的Java Properties形式从外部文件中加载属性值，那么需要使用PropertyPlaceholderConfigurer。这样做可以定制环境相关的属性值，比如数据库URL和密码，而不需要冒很大的风险去修改容器中XML的定义。

You use the PropertyPlaceholderConfigurer to externalize property values from a bean definition in a separate file using the standard Java Properties format. Doing so enables the person deploying an application to customize environment-specific properties such as database URLs and passwords, without the complexity or risk of modifying the main XML definition file or files for the container.

查看下面的XML配置片段，配置DataSource时使用了占位符。这个例子展示了如何从外部文件配置属性。在运行时，PropertyPlaceholderConfigurer会替代其中的一些属性。这些被替代的占位符以${property-name}的形式定义，这与Ant/log4j/JSP EL的风格是一致的。

Consider the following XML-based configuration metadata fragment, where a DataSource with placeholder values is defined. The example shows properties configured from an external Properties file. At runtime, a PropertyPlaceholderConfigurer is applied to the metadata that will replace some properties of the DataSource. The values to replace are specified as placeholders of the form ${property-name} which follows the Ant / log4j / JSP EL style.
```xml
<bean class="org.springframework.beans.factory.config.PropertyPlaceholderConfigurer">
    <property name="locations" value="classpath:com/foo/jdbc.properties"/>
</bean>

<bean id="dataSource" destroy-method="close"
        class="org.apache.commons.dbcp.BasicDataSource">
    <property name="driverClassName" value="${jdbc.driverClassName}"/>
    <property name="url" value="${jdbc.url}"/>
    <property name="username" value="${jdbc.username}"/>
    <property name="password" value="${jdbc.password}"/>
</bean>
```
实际的值是以标准Java Properties的形式存在于另一个文件中的：

The actual values come from another file in the standard Java Properties format:
```java
jdbc.driverClassName=org.hsqldb.jdbcDriver
jdbc.url=jdbc:hsqldb:hsql://production:9002
jdbc.username=sa
jdbc.password=root
```
因此，在运行时字符串`${jdbc.username}`会被替换为值’sa’，其它的占位符也是按一样的规则替换。PropertyPlaceholderConfigurer会检查一个bean定义的大部分属性。另外，占位符的前缀和后缀是可以自定义的。

Therefore, the string ${jdbc.username} is replaced at runtime with the value 'sa', and the same applies for other placeholder values that match keys in the properties file. The PropertyPlaceholderConfigurer checks for placeholders in most properties and attributes of a bean definition. Furthermore, the placeholder prefix and suffix can be customized.

Spring 2.5中引入的context命名空间中有专门配置占位符的元素。一个或多个位置可以使用逗号分割开。

With the context namespace introduced in Spring 2.5, it is possible to configure property placeholders with a dedicated configuration element. One or more locations can be provided as a comma-separated list in the location attribute.

`<context:property-placeholder location="classpath:com/foo/jdbc.properties"/>`
PropertyPlaceholderConfigurer不仅查找我们指定的Properties文件中的属性值。默认地，如果在指定的文件中没有找到它还会检查Java的系统属性。设置它的systemPropertiesMode属性可以改变它的行为，包含以下三个值：

The PropertyPlaceholderConfigurer not only looks for properties in the Properties file you specify. By default it also checks against the Java System properties if it cannot find a property in the specified properties files. You can customize this behavior by setting the systemPropertiesMode property of the configurer with one of the following three supported integer values:

* never(0)：从不检查系统属性。
* fallback(1)：如果指定文件中没有找到才检查系统属性。这是默认值。
* override(2)：在尝试查找指定的文件前先检查系统属性。这允许系统属性覆盖其它的属性来源。
* never (0): Never check system properties
* fallback (1): Check system properties if not resolvable in the specified properties files. This is the default.
* override (2): Check system properties first, before trying the specified properties files. This allows system properties to override any other property source.

更多信息请参考PropertyPlaceholderConfigurer的javadoc。

Consult the PropertyPlaceholderConfigurer javadocs for more information.

[Tip]
* 可以使用PropertyPlaceholderConfigurer去替换类名，这在运行时指定实现类有时候会很有用。例如：

You can use the PropertyPlaceholderConfigurer to substitute class names, which is sometimes useful when you have to pick a particular implementation class at runtime. For example:
```xml
<bean class="org.springframework.beans.factory.config.PropertyPlaceholderConfigurer">
    <property name="locations">
        <value>classpath:com/foo/strategy.properties</value>
    </property>
    <property name="properties">
        <value>custom.strategy.class=com.foo.DefaultStrategy</value>
    </property>
</bean>

<bean id="serviceStrategy" class="${custom.strategy.class}"/>
```
如果在运行时这个类不能被解析为正确的类，那么这个bean会创建失败，对于非延迟初始化的bean，这种失败发生在ApplicationContext的preInstantiateSingletons()期间。

If the class cannot be resolved at runtime to a valid class, resolution of the bean fails when it is about to be created, which is during the preInstantiateSingletons() phase of an ApplicationContext for a non-lazy-init bean.

__Example: the PropertyOverrideConfigurer__

PropertyOverrideConfigurer，另外一个bean工厂后置处理器，与PropertyPlaceholderConfigurer类似，但又不像后者，原始的bean定义的属性可以有默认值或没有值。如果重写的Properties文件没有合适的值，那就使用默认值。

The PropertyOverrideConfigurer, another bean factory post-processor, resembles the PropertyPlaceholderConfigurer, but unlike the latter, the original definitions can have default values or no values at all for bean properties. If an overriding Properties file does not have an entry for a certain bean property, the default context definition is used.

注意，bean定义是不会意识到被重写的，所以从XML定义是看不出重写配置器被使用的。如果有多个PropertyOverrideConfigurer实例定义了同一个属性的不同的值，那么最后一个会起作用，因为重写机制的存在。

Note that the bean definition is not aware of being overridden, so it is not immediately obvious from the XML definition file that the override configurer is being used. In case of multiple PropertyOverrideConfigurer instances that define different values for the same bean property, the last one wins, due to the overriding mechanism.

属性文件按下面的形式编写：

Properties file configuration lines take this format:
```java
beanName.property=value
```
For example:
```java
dataSource.driverClassName=com.mysql.jdbc.Driver
dataSource.url=jdbc:mysql:mydb
```
这个例子适用于包含了一个dataSource的bean的定义，且它包含driver和url属性。

This example file can be used with a container definition that contains a bean called dataSource, which has driver and url properties.

也支持复合的属性名，只要除了最后一个属性之前的组件不为空即可。例如：

Compound property names are also supported, as long as every component of the path except the final property being overridden is already non-null (presumably initialized by the constructors). In this example…​

`foo.fred.bob.sammy=123`
它表示foo有一个fred属性，fred有一个bob属性，bob有一个属性sammy，这个sammy属性会被设置成123。

the sammy property of the bob property of the fred property of the foo bean is set to the scalar value 123.
[Note]
> 注意，重写的值总是字面值，它们不会被翻译成bean的引用，这种转换也适用于XML定义中指定的bean引用的原始值。
> Specified override values are always literal values; they are not translated into bean references. This convention also applies when the original value in the XML bean definition specifies a bean reference.

Spring 2.5中引入的context命名空间中有专门配置属性重写的元素。

With the context namespace introduced in Spring 2.5, it is possible to configure property overriding with a dedicated configuration element:
```xml
<context:property-override location="classpath:override.properties"/>
```
（关于BeanPostProcessor和BeanFactoryPostProcessor的使用请参考博文：http://blog.csdn.net/tangtong1/article/details/51916679）

### 7.8.3 使用FactoryBean自定义实例化逻辑
7.8.3 Customizing instantiation logic with a FactoryBean

为对象实现org.springframework.beans.factory.FactoryBean接口，这些对象是他们自己的工厂。

Implement the org.springframework.beans.factory.FactoryBean interface for objects that are themselves factories.

FactoryBean接口是Spring窗口实例化逻辑的插入点。如果有一个非常复杂的初始化代码，它用Java能比XML更好地表述，那么就可以创建一个自己的FactoryBean，编写复杂的初始化逻辑在那个类中，并插入到窗口中。

The FactoryBean interface is a point of pluggability into the Spring IoC container’s instantiation logic. If you have complex initialization code that is better expressed in Java as opposed to a (potentially) verbose amount of XML, you can create your own FactoryBean, write the complex initialization inside that class, and then plug your custom FactoryBean into the container.
FactoryBean接口提供了三个方法：

The FactoryBean interface provides three methods:

* Object getObject()：返回这个工厂创建的一个实例。这个实例可以共享，由这个工厂单例还是原型决定。
* boolean isSingleton()：如果返回的是单例则返回true，否则返回false。
* Class getObjectType()：返回getObject()方法返回的对象的类型，如果事先不知道类型那就返回null。

* Object getObject(): returns an instance of the object this factory creates. The instance can possibly be shared, depending on whether this factory returns singletons or prototypes.
* boolean isSingleton(): returns true if this FactoryBean returns singletons, false otherwise.
* Class getObjectType(): returns the object type returned by the getObject() method or null if the type is not known in advance.

FactoryBean的概念和接口在Spring框架中被大量使用，Spring自己至少使用了50个FactoryBean的实现。

The FactoryBean concept and interface is used in a number of places within the Spring Framework; more than 50 implementations of the FactoryBean interface ship with Spring itself.

当需要从容器获得实际的FactoryBean的实例时，调用ApplicationContext的getBean()方法时在bean的id前面加上&符号即可。所以对于一个id为myBean的FactoryBean，调用getBean(“myBean”)会返回FactoryBean创建的实例，然而，调用getBean(“&myBean”)则会返回FactoryBean的实例本身。

When you need to ask a container for an actual FactoryBean instance itself instead of the bean it produces, preface the bean’s id with the ampersand symbol ( &) when calling the getBean() method of the ApplicationContext. So for a given FactoryBean with an id of myBean, invoking getBean("myBean") on the container returns the product of the FactoryBean; whereas, invoking getBean("&myBean") returns the FactoryBean instance itself.

（关于FactoryBean的使用请参考博文：http://blog.csdn.net/tangtong1/article/details/51920069）

## 7.9 基于注解的容器配置
7.9 Annotation-based container configuration

> 注解形式比XML形式更好吗？

> Are annotations better than XML for configuring Spring?

> 注解形式的引入引起了一个话题，它是否比XML形式更好。简单的回答是视情况而定。详细的回答是每一种方式都有它的优缺点，通常由开发者决定哪种方式更适合他们。由于他们定义的方式，注解提供了更多的上下文声明，导致了更短更简明的配置。然而，XML形式装配组件不会涉及到它们的源码或者重新编译它们。一些开发者更喜欢亲近源码，但另一些则认为注解类不是POJO，且配置很分散，难以控制。

> The introduction of annotation-based configurations raised the question of whether this approach is 'better' than XML. The short answer is it depends. The long answer is that each approach has its pros and cons, and usually it is up to the developer to decide which strategy suits them better. Due to the way they are defined, annotations provide a lot of context in their declaration, leading to shorter and more concise configuration. However, XML excels at wiring up components without touching their source code or recompiling them. Some developers prefer having the wiring close to the source while others argue that annotated classes are no longer POJOs and, furthermore, that the configuration becomes decentralized and harder to control.

> 不管做出什么选择，Spring都支持两种风格且可以混用它们。另外，通过Java配置的方式，Spring可以让注解变得非侵入式，不会触碰到目标组件的源码。而且，所有的配置方式Spring Tool Suite都支持。

> No matter the choice, Spring can accommodate both styles and even mix them together. It’s worth pointing out that through its JavaConfig option, Spring allows annotations to be used in a non-invasive way, without touching the target components source code and that in terms of tooling, all configuration styles are supported by the Spring Tool Suite.

一种XML形式的替代方案是使用基于注解的配置，它依赖于字节码元数据，用于装配组件并可取代尖括号式的声明。不同于使用XML描述一个bean，开发者需要把配置移动到组件类本身，并给相关的类、方法及字段声明加上注解。像例子：RequiredAnnotationBeanPostProcessor中提及的一样，联合使用BeanPostProcessor和注解是扩展Spring容器的一种常用方法。例如，Spring 2.0引入了@Required注解，它强制属性必须获取一个值。Spring 2.5遵循同样的方式驱动依赖注入。本质上，@Autowired注解提供了相同的能力，如7.4.5 自动装配合作者中描述的一样，不过它提供了更细粒度的控制和更广泛的适用性。Spring 2.5也支持JSR-250的注解，比如@PostConstruct和@PreDestroy。Spring 3.0支持JSR-330的注解，它们位于javax.inject包下，比如@Inject和@Named。更详细的信息可以在相关章节中找到。

An alternative to XML setups is provided by annotation-based configuration which rely on the bytecode metadata for wiring up components instead of angle-bracket declarations. Instead of using XML to describe a bean wiring, the developer moves the configuration into the component class itself by using annotations on the relevant class, method, or field declaration. As mentioned in the section called “Example: The RequiredAnnotationBeanPostProcessor”, using a BeanPostProcessor in conjunction with annotations is a common means of extending the Spring IoC container. For example, Spring 2.0 introduced the possibility of enforcing required properties with the @Required annotation. Spring 2.5 made it possible to follow that same general approach to drive Spring’s dependency injection. Essentially, the @Autowired annotation provides the same capabilities as described in Section 7.4.5, “Autowiring collaborators” but with more fine-grained control and wider applicability. Spring 2.5 also added support for JSR-250 annotations such as @PostConstruct, and @PreDestroy. Spring 3.0 added support for JSR-330 (Dependency Injection for Java) annotations contained in the javax.inject package such as @Inject and @Named. Details about those annotations can be found in the relevant section.

[Note]
> 注解形式的注入在XML形式之前执行，因此，如果同时使用了两者，则XML形式的注入会覆盖注解形式的注入。
> Annotation injection is performed before XML injection, thus the latter configuration will override the former for properties wired through both approaches.

可以一个一个地注册这些bean，也可以隐式地注册它们，使用下面的配置即可（注意，请包含context命名空间）。

As always, you can register them as individual bean definitions, but they can also be implicitly registered by including the following tag in an XML-based Spring configuration (notice the inclusion of the context namespace):
```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns:context="http://www.springframework.org/schema/context"
    xsi:schemaLocation="http://www.springframework.org/schema/beans
        http://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/context
        http://www.springframework.org/schema/context/spring-context.xsd">

    <context:annotation-config/>

</beans>
```
（隐式注册的后置处理器包括AutowiredAnnotationBeanPostProcessor, CommonAnnotationBeanPostProcessor, PersistenceAnnotationBeanPostProcessor以及前面提到的RequiredAnnotationBeanPostProcessor。）

(The implicitly registered post-processors include AutowiredAnnotationBeanPostProcessor, CommonAnnotationBeanPostProcessor, PersistenceAnnotationBeanPostProcessor, as well as the aforementioned RequiredAnnotationBeanPostProcessor.)

[Note]
> <context:annotation-config/>只能寻找它所定义的上下文中的注解。这意味着，如果在一个WebApplicationContext中为一个DispatcherServlete配置了<context:annotation-config/>，那么它只会检测controller中的@Autowired，而不会检测service。更多信息请参考22.2 DispatcherServlet。
> <context:annotation-config/> only looks for annotations on beans in the same application context in which it is defined. This means that, if you put <context:annotation-config/> in a WebApplicationContext for a DispatcherServlet, it only checks for @Autowired beans in your controllers, and not your services. See Section 22.2, “The DispatcherServlet” for more information.

### 7.9.1 @Required

也可以在setter方法上使用@Required

The @Required annotation applies to bean property setter methods, as in the following example:
```java
public class SimpleMovieLister {

    private MovieFinder movieFinder;

    @Required
    public void setMovieFinder(MovieFinder movieFinder) {
        this.movieFinder = movieFinder;
    }

    // ...
}
```

This annotation simply indicates that the affected bean property must be populated at configuration time, through an explicit property value in a bean definition or through autowiring. The container throws an exception if the affected bean property has not been populated; this allows for eager and explicit failure, avoiding NullPointerExceptions or the like later on. It is still recommended that you put assertions into the bean class itself, for example, into an init method. Doing so enforces those required references and values even when you use the class outside of a container.

### 7.9.2 @Autowired
[Note]
> JSR-330的@Inject注解可以替换下面例子中Spring的@Autowired注解。更多详情请点这里。
> JSR 330’s @Inject annotation can be used in place of Spring’s @Autowired annotation in the examples below. See here for more details.

You can apply the @Autowired annotation to constructors:
```java
public class MovieRecommender {

    private final CustomerPreferenceDao customerPreferenceDao;

    @Autowired
    public MovieRecommender(CustomerPreferenceDao customerPreferenceDao) {
        this.customerPreferenceDao = customerPreferenceDao;
    }

    // ...
}
```
[Note]
> 从Spring 4.3开始，如果目标bean只有一个构造方法，则@Autowired的构造方法不再是必要的。如果有多个构造方法，那么至少一个必须被注解以便告诉容器使用哪个。
> As of Spring Framework 4.3, an @Autowired annotation on such a constructor is no longer necessary if the target bean only defines one constructor to begin with. However, if several constructors are available, at least one must be annotated to teach the container which one to use.

也可以在setter方法上使用@Autowired：

As expected, you can also apply the @Autowired annotation to "traditional" setter methods:
```java
public class SimpleMovieLister {

    private MovieFinder movieFinder;

    @Autowired
    public void setMovieFinder(MovieFinder movieFinder) {
        this.movieFinder = movieFinder;
    }

    // ...
}
```
也可以应用在具有任意名字和多个参数的方法上：

You can also apply the annotation to methods with arbitrary names and/or multiple arguments:
```java
public class MovieRecommender {

    private MovieCatalog movieCatalog;

    private CustomerPreferenceDao customerPreferenceDao;

    @Autowired
    public void prepare(MovieCatalog movieCatalog,
            CustomerPreferenceDao customerPreferenceDao) {
        this.movieCatalog = movieCatalog;
        this.customerPreferenceDao = customerPreferenceDao;
    }

    // ...
}
```
也可以应用在字段上，甚至可以与构造方法上混用：

You can apply @Autowired to fields as well and even mix it with constructors:
```java
public class MovieRecommender {

    private final CustomerPreferenceDao customerPreferenceDao;

    @Autowired
    private MovieCatalog movieCatalog;

    @Autowired
    public MovieRecommender(CustomerPreferenceDao customerPreferenceDao) {
        this.customerPreferenceDao = customerPreferenceDao;
    }

    // ...
}
```
[Tip]
Make sure that your target components (e.g. MovieCatalog, CustomerPreferenceDao) are consistently declared by the type that you are using for your @Autowired-annotated injection points. Otherwise injection may fail due to no type match found at runtime.

For XML-defined beans or component classes found through a classpath scan, the container usually knows the concrete type upfront. However, for @Bean factory methods, you need to make sure that the declared return type is sufficiently expressive. For components implementing several interfaces or for components potentially referred to by their implementation type, consider declaring the most specific return type on your factory method (at least as specific as required by the injection points referring to your bean).

也可以从ApplicationContext中提供特定类型的所有bean，只要添加这个注解在一个那种类型的数组字段或方法上即可：

It is also possible to provide all beans of a particular type from the ApplicationContext by adding the annotation to a field or method that expects an array of that type:
```java
public class MovieRecommender {

    @Autowired
    private MovieCatalog[] movieCatalogs;

    // ...
}
```
同样适用于集合类型：

The same applies for typed collections:
```java
public class MovieRecommender {

    private Set<MovieCatalog> movieCatalogs;

    @Autowired
    public void setMovieCatalogs(Set<MovieCatalog> movieCatalogs) {
        this.movieCatalogs = movieCatalogs;
    }

    // ...
}
```
[Tip]
> 如果需要数组或list中的元素按顺序排列的话，可以让这些bean实现org.springframework.core.Ordered接口或使用@Order注解或标准的@Priority注解。

> Your target beans can implement the org.springframework.core.Ordered interface or use the @Order or standard @Priority annotation if you want items in the array or list to be sorted in a specific order. Otherwise their order will follow the registration order of the corresponding target bean definitions in the container.

The @Order annotation may be declared at target class level but also on @Bean methods, potentially being very individual per bean definition (in case of multiple definitions with the same bean class). @Order values may influence priorities at injection points, but please be aware that they do not influence singleton startup order which is an orthogonal concern determined by dependency relationships and @DependsOn declarations.

Note that the standard javax.annotation.Priority annotation is not available at the @Bean level since it cannot be declared on methods. Its semantics can be modeled through @Order values in combination with @Primary on a single bean per type.

甚至Map也可以被自动装配，只要key的类型是String就可以。Map的value将包含所有的特定类型的bean，并且key会包含这些bean的名字。

Even typed Maps can be autowired as long as the expected key type is String. The Map values will contain all beans of the expected type, and the keys will contain the corresponding bean names:
```java
public class MovieRecommender {

    private Map<String, MovieCatalog> movieCatalogs;

    @Autowired
    public void setMovieCatalogs(Map<String, MovieCatalog> movieCatalogs) {
        this.movieCatalogs = movieCatalogs;
    }

    // ...
}```

默认地，如果没有候选的bean则自动装配会失败。这种默认的行为表示被注解的方法、构造方法及字段必须（required）有相应的依赖。也可按下面的方法改变这种行为。

By default, the autowiring fails whenever zero candidate beans are available; the default behavior is to treat annotated methods, constructors, and fields as indicating required dependencies. This behavior can be changed as demonstrated below.
```java
public class SimpleMovieLister {

    private MovieFinder movieFinder;

    @Autowired(required = false)
    public void setMovieFinder(MovieFinder movieFinder) {
        this.movieFinder = movieFinder;
    }

    // ...
}
```
[Note]
> 每个类只能有一个构造方法被标记为required，但可以有多个非必须的构造方法被注解。这种情况下，每个构造方法都会考虑候选者，且Spring使用最贪婪的那个构造方法，它的依赖都能被满足，并且有最多个的参数。 
@Autowired的required属性比@Required注解更推荐使用。这个required属性表示如果不能被自动装配那么这个属性是非必须的、会被忽略的。另一方面，@Required则更强势，它要求这个属性必须被设置不管容器以什么样的方式支持它。如果没有值被注入，就会抛出异常。

> Only one annotated constructor per-class can be marked as required, but multiple non-required constructors can be annotated. In that case, each is considered among the candidates and Spring uses the greediest constructor whose dependencies can be satisfied, that is the constructor that has the largest number of arguments.

> The required attribute of @Autowired is recommended over the @Required annotation. The required attribute indicates that the property is not required for autowiring purposes, the property is ignored if it cannot be autowired. @Required, on the other hand, is stronger in that it enforces the property that was set by any means supported by the container. If no value is injected, a corresponding exception is raised.

Alternatively, you may express the non-required nature of a particular dependency through Java 8’s java.util.Optional:
```java
public class SimpleMovieLister {

    @Autowired
    public void setMovieFinder(Optional<MovieFinder> movieFinder) {
        ...
    }
}
```
也可以把@Autowired用在那些著名的可解析的依赖的接口上：BeanFactory, ApplicationContext, Environment, ResourceLoader, ApplicationEventPublisher, 以及MessageSource。这些接口和它们扩展的接口，比如ConfigurableApplicationContext或ResourcePatternResolver，会自动解析，不需要特殊设置。

You can also use @Autowired for interfaces that are well-known resolvable dependencies: BeanFactory, ApplicationContext, Environment, ResourceLoader, ApplicationEventPublisher, and MessageSource. These interfaces and their extended interfaces, such as ConfigurableApplicationContext or ResourcePatternResolver, are automatically resolved, with no special setup necessary.
```java
public class MovieRecommender {

    @Autowired
    private ApplicationContext context;

    public MovieRecommender() {
    }

    // ...
}
```
[Note]
> @Autowired， @Inject， @Resource和@Value注解都是被Spring的BeanPostProcessor处理的，这反过来意味着我们不能使用自己的BeanPostProcessor或BeanFactoryPostProcessor类型来处理这些注解。这些类型必须通过XML或使用Spring的@Bean方法显式地装配。

> @Autowired, @Inject, @Resource, and @Value annotations are handled by Spring BeanPostProcessor implementations which in turn means that you cannot apply these annotations within your own BeanPostProcessor or BeanFactoryPostProcessor types (if any). These types must be 'wired up' explicitly via XML or using a Spring @Bean method.

### 7.9.3 使用@Primary微调基于注解的自动装配
7.9.3 Fine-tuning annotation-based autowiring with @Primary

因为基于类型的自动装配可能会导致多个候选者，所以对这种过程通常需要更多的控制。一种方式是使用Spring的@Primary注解。它表示如果存在多个候选者且另一个bean只需要一个特定类型的bean依赖时，就使用标记了@Primary注解的那个依赖。如果只有一个候选者那就直接使用那个候选者即可。

Because autowiring by type may lead to multiple candidates, it is often necessary to have more control over the selection process. One way to accomplish this is with Spring’s @Primary annotation. @Primary indicates that a particular bean should be given preference when multiple beans are candidates to be autowired to a single-valued dependency. If exactly one 'primary' bean exists among the candidates, it will be the autowired value.

我们假设下面的配置，定义firstMovieCatalog作为主要的（primary）MovieCatalog。

Let’s assume we have the following configuration that defines firstMovieCatalog as the primary MovieCatalog.
```java
@Configuration
public class MovieConfiguration {

    @Bean
    @Primary
    public MovieCatalog firstMovieCatalog() { ... }

    @Bean
    public MovieCatalog secondMovieCatalog() { ... }

    // ...
}
```
使用这种配置，下面的MovieRecommender将装配firstMovieCatalog。

With such configuration, the following MovieRecommender will be autowired with the firstMovieCatalog.
```java
public class MovieRecommender {

    @Autowired
    private MovieCatalog movieCatalog;

    // ...
}
```
通信的bean定义看起来像下面这样：

The corresponding bean definitions appear as follows.
```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns:context="http://www.springframework.org/schema/context"
    xsi:schemaLocation="http://www.springframework.org/schema/beans
        http://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/context
        http://www.springframework.org/schema/context/spring-context.xsd">

    <context:annotation-config/>

    <bean class="example.SimpleMovieCatalog" primary="true">
        <!-- inject any dependencies required by this bean -->
    </bean>

    <bean class="example.SimpleMovieCatalog">
        <!-- inject any dependencies required by this bean -->
    </bean>

    <bean id="movieRecommender" class="example.MovieRecommender"/>

</beans>
```
### 7.9.4 使用限定符微调基于注解的自动装配
7.9.4 Fine-tuning annotation-based autowiring with qualifiers

当一个类型有几个实例时使用@Primary是一种有效的方式。当需要对选择过程做更多的控制时，那就需要用到Spring的@Qualifier注解了。为指定的参数绑定一个限定的值，可以缩小类型匹配的范围，使用这种方式，这个值可以是一个很普通的描述性的值：

@Primary is an effective way to use autowiring by type with several instances when one primary candidate can be determined. When more control over the selection process is required, Spring’s @Qualifier annotation can be used. You can associate qualifier values with specific arguments, narrowing the set of type matches so that a specific bean is chosen for each argument. In the simplest case, this can be a plain descriptive value:
```java
public class MovieRecommender {

    @Autowired
    @Qualifier("main")
    private MovieCatalog movieCatalog;

    // ...
}
```
@Qualifier注解还能用在构造方法参数或方法参数上：

The @Qualifier annotation can also be specified on individual constructor arguments or method parameters:
```java
public class MovieRecommender {

    private MovieCatalog movieCatalog;

    private CustomerPreferenceDao customerPreferenceDao;

    @Autowired
    public void prepare(@Qualifier("main")MovieCatalog movieCatalog,
            CustomerPreferenceDao customerPreferenceDao) {
        this.movieCatalog = movieCatalog;
        this.customerPreferenceDao = customerPreferenceDao;
    }

    // ...
}
```
通信的bean定义如下所示。带有限定符“main”的bean会被装配到拥有同样值的构造方法参数上。

The corresponding bean definitions appear as follows. The bean with qualifier value "main" is wired with the constructor argument that is qualified with the same value.
```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns:context="http://www.springframework.org/schema/context"
    xsi:schemaLocation="http://www.springframework.org/schema/beans
        http://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/context
        http://www.springframework.org/schema/context/spring-context.xsd">

    <context:annotation-config/>

    <bean class="example.SimpleMovieCatalog">
        <qualifier value="main"/>

        <!-- inject any dependencies required by this bean -->
    </bean>

    <bean class="example.SimpleMovieCatalog">
        <qualifier value="action"/>

        <!-- inject any dependencies required by this bean -->
    </bean>

    <bean id="movieRecommender" class="example.MovieRecommender"/>

</beans>
```
bean的名字被认为是默认的限定符，它是一种后备的匹配。因此，可以定义一个bean的id为“main”，而不是内嵌qualifier元素，这会有一样的匹配结果。然而，尽管可以使用这种转换通过名字引用指定的bean，但是@Autowired默认是使用带有限定符的类型驱动的注入的。这意味着，带有bean名字的限定符总是会缩小类型匹配的语义，它们从语义上无法表达引用的是一个独立的bean的id。好的限定符是“main”、“EMEA”或者“persistent”等，它们表达了指定的组件是不同于bean的id的，它们可能会在匿名的bean定义中自动形成，就像前面的例子一样。

For a fallback match, the bean name is considered a default qualifier value. Thus you can define the bean with an id "main" instead of the nested qualifier element, leading to the same matching result. However, although you can use this convention to refer to specific beans by name, @Autowired is fundamentally about type-driven injection with optional semantic qualifiers. This means that qualifier values, even with the bean name fallback, always have narrowing semantics within the set of type matches; they do not semantically express a reference to a unique bean id. Good qualifier values are "main" or "EMEA" or "persistent", expressing characteristics of a specific component that are independent from the bean id, which may be auto-generated in case of an anonymous bean definition like the one in the preceding example.

限定符也可以用于集合类型上，如前所述，比如Set<MovieCatalog>。在这种情况下，所有声明了指定限定符的bean都将被注入到集合中。这表示限定符并不是独立的，它们甚至可以有限定规则。例如，可以使用相同的限定符“action”定义多个MovieCatalog，所有的这些都会被注入到带有@Qualifier(“action”)注解的Set<MovieCatalog>中。

Qualifiers also apply to typed collections, as discussed above, for example, to Set<MovieCatalog>. In this case, all matching beans according to the declared qualifiers are injected as a collection. This implies that qualifiers do not have to be unique; they rather simply constitute filtering criteria. For example, you can define multiple MovieCatalog beans with the same qualifier value "action", all of which would be injected into a Set<MovieCatalog> annotated with @Qualifier("action").

[Tip]
> 如果想要表达是通过名字注入的，不要使用@Autowired，甚至不要通过@Qualifier的值引用一个bean的名字。可以使用JSR-250中的@Resource注解，它从语义上表达了通过bean的独立的名字引用它们，这样匹配过程跟声明的类型就没有关系了。不如说@Autowired有不同的语义：在通过类型选择到了候选的bean后，指定了的限定符就会被认为在这些已选择的bean中，例如，“account”限定符只会匹配那些拥有相同限定符标签的bean。 
> Letting qualifier values select against target bean names, within the type-matching candidates, doesn’t even require a @Qualifier annotation at the injection point. If there is no other resolution indicator (e.g. a qualifier or a primary marker), for a non-unique dependency situation, Spring will match the injection point name (i.e. field name or parameter name) against the target bean names and choose the same-named candidate, if any.

> That said, if you intend to express annotation-driven injection by name, do not primarily use @Autowired, even if is capable of selecting by bean name among type-matching candidates. Instead, use the JSR-250 @Resource annotation, which is semantically defined to identify a specific target component by its unique name, with the declared type being irrelevant for the matching process. @Autowired has rather different semantics: After selecting candidate beans by type, the specified String qualifier value will be considered within those type-selected candidates only, e.g. matching an "account" qualifier against beans marked with the same qualifier label.

> 对于那些定义为集合、Map或者数组类型的bean，@Resource也是一种很好的解决方案，直接通过名字引用指定的集合或数组。不过从4.3开始，集合、Map和数组类型也可以通过@Autowired类型匹配引用了，只要元素的类型被保存在@Bean的返回类型签名中或者集合继承体系中。在这种情况下，限定符可以用于选择相同类型的集合，就像前面描述的一样。从4.3开始，@Autowired也可以自引用注入，比如，引用当前正在注入的bean。注意，自引用只是一种后备选项，还是优先使用正常的依赖注入其它的bean。在那种情况下，自引用不参与到正常的候选者选择中，并且从来都不是主要的，相反，它们总是有最低的优先级。特别地，使用自引用作为最后的手段，例如，通过bean的事务代理调用同一个实例的其它方法：在这种情况下考虑把受影响的方法提取到一个分离的委托中。作为替代方案，@Resource可以通过独立的名字获取到当前bean的一个代理。@Autowired也可以通过限定符应用到参数级别上，比如字段、构造方法或多个参数的方法。相反地，@Resource只能应用在字段或只有一个参数的setter方法上。结论是，如果注入的目标是构造方法或多参数的方法就使用带限定符的@Autowired吧。

> For beans that are themselves defined as a collection/map or array type, @Resource is a fine solution, referring to the specific collection or array bean by unique name. That said, as of 4.3, collection/map and array types can be matched through Spring’s @Autowired type matching algorithm as well, as long as the element type information is preserved in @Bean return type signatures or collection inheritance hierarchies. In this case, qualifier values can be used to select among same-typed collections, as outlined in the previous paragraph.

> As of 4.3, @Autowired also considers self references for injection, i.e. references back to the bean that is currently injected. Note that self injection is a fallback; regular dependencies on other components always have precedence. In that sense, self references do not participate in regular candidate selection and are therefore in particular never primary; on the contrary, they always end up as lowest precedence. In practice, use self references as a last resort only, e.g. for calling other methods on the same instance through the bean’s transactional proxy: Consider factoring out the affected methods to a separate delegate bean in such a scenario. Alternatively, use @Resource which may obtain a proxy back to the current bean by its unique name.

> @Autowired applies to fields, constructors, and multi-argument methods, allowing for narrowing through qualifier annotations at the parameter level. By contrast, @Resource is supported only for fields and bean property setter methods with a single argument. As a consequence, stick with qualifiers if your injection target is a constructor or a multi-argument method.

也可创建自定义的限定符注解，只要定义一个注解并在其定义上添加@Qualifier即可：

You can create your own custom qualifier annotations. Simply define an annotation and provide the @Qualifier annotation within your definition:
```java
@Target({ElementType.FIELD, ElementType.PARAMETER})
@Retention(RetentionPolicy.RUNTIME)
@Qualifier
public @interface Genre {

    String value();
}
```
然后就可以在字段或参数上使用自定义的限定符了：

Then you can provide the custom qualifier on autowired fields and parameters:
```java
public class MovieRecommender {

    @Autowired
    @Genre("Action")
    private MovieCatalog actionCatalog;

    private MovieCatalog comedyCatalog;

    @Autowired
    public void setComedyCatalog(@Genre("Comedy") MovieCatalog comedyCatalog) {
        this.comedyCatalog = comedyCatalog;
    }

    // ...
}
```
接着，提供候选的bean定义的信息。可以添加<qualifier/>标签作为<bean/>标签的子标签，并指定其type和value去匹配自定义的限定符注解。类型通过这个注解的全路径匹配，当然，如果没有风险的话也可以使用其类名。两种方式如下所示：

Next, provide the information for the candidate bean definitions. You can add <qualifier/> tags as sub-elements of the <bean/> tag and then specify the type and value to match your custom qualifier annotations. The type is matched against the fully-qualified class name of the annotation. Or, as a convenience if no risk of conflicting names exists, you can use the short class name. Both approaches are demonstrated in the following example.
```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns:context="http://www.springframework.org/schema/context"
    xsi:schemaLocation="http://www.springframework.org/schema/beans
        http://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/context
        http://www.springframework.org/schema/context/spring-context.xsd">

    <context:annotation-config/>

    <bean class="example.SimpleMovieCatalog">
        <qualifier type="Genre" value="Action"/>
        <!-- inject any dependencies required by this bean -->
    </bean>

    <bean class="example.SimpleMovieCatalog">
        <qualifier type="example.Genre" value="Comedy"/>
        <!-- inject any dependencies required by this bean -->
    </bean>

    <bean id="movieRecommender" class="example.MovieRecommender"/>

</beans>
```
在7.10 类路径扫描及管理的组件中，我们将会看到一种在XML中配置限定符元数据的替代方案。特别地，请参考7.10.8 通过注解提供注解元数据。

In Section 7.10, “Classpath scanning and managed components”, you will see an annotation-based alternative to providing the qualifier metadata in XML. Specifically, see Section 7.10.8, “Providing qualifier metadata with annotations”.

在一些情况下，使用没有值的注解可能就足够了。这非常有用当注解提供了一种更通用的目的，并且可以运用到不同的依赖上。例如，当没有网络时可能会需要一种offline的类别。第一步定义这个简单的注解：

In some cases, it may be sufficient to use an annotation without a value. This may be useful when the annotation serves a more generic purpose and can be applied across several different types of dependencies. For example, you may provide an offline catalog that would be searched when no Internet connection is available. First define the simple annotation:
```java
@Target({ElementType.FIELD, ElementType.PARAMETER})
@Retention(RetentionPolicy.RUNTIME)
@Qualifier
public @interface Offline {

}
```
然后，添加这个注解到将被自动装配的字段或属性上：

Then add the annotation to the field or property to be autowired:
```java
public class MovieRecommender {

    @Autowired
    @Offline
    private MovieCatalog offlineCatalog;

    // ...
}
```
然后，这个bean定义就只需要限定符类型：

Now the bean definition only needs a qualifier type:
```xml
<bean class="example.SimpleMovieCatalog">
    <qualifier type="Offline"/>
    <!-- inject any dependencies required by this bean -->
</bean>
```
也可以自定义限定符注解，使它们带有命名的属性或者替代简单的value属性。如果多个属性值被指定在一个将被装配的字段或参数上，那么bean的定义必须匹配所有的属性值。例如，请看下面的注解定义：

You can also define custom qualifier annotations that accept named attributes in addition to or instead of the simple value attribute. If multiple attribute values are then specified on a field or parameter to be autowired, a bean definition must match all such attribute values to be considered an autowire candidate. As an example, consider the following annotation definition:
```java
@Target({ElementType.FIELD, ElementType.PARAMETER})
@Retention(RetentionPolicy.RUNTIME)
@Qualifier
public @interface MovieQualifier {

    String genre();

    Format format();
}
```
这种情况下Format是一个枚举：

In this case Format is an enum:
```java
public enum Format {
    VHS, DVD, BLURAY
}```

被装配的字段使用这个自定义的限定符注解，它包含两个属性：genre和format。

The fields to be autowired are annotated with the custom qualifier and include values for both attributes: genre and format.
```java
public class MovieRecommender {

    @Autowired
    @MovieQualifier(format=Format.VHS, genre="Action")
    private MovieCatalog actionVhsCatalog;

    @Autowired
    @MovieQualifier(format=Format.VHS, genre="Comedy")
    private MovieCatalog comedyVhsCatalog;

    @Autowired
    @MovieQualifier(format=Format.DVD, genre="Action")
    private MovieCatalog actionDvdCatalog;

    @Autowired
    @MovieQualifier(format=Format.BLURAY, genre="Comedy")
    private MovieCatalog comedyBluRayCatalog;

    // ...
}
```
最后，这些bean的定义需要包含这些限定符。这个例子也展示了bean的meta属性可以使用<qualifier/>子元素代替。如果可以，优先使用<qualifier/>及其属性，但是，如果滑限定符自动装配机制会使用<meta/>标签提供的值，就像下面例子中最后两个bean的定义那样：

Finally, the bean definitions should contain matching qualifier values. This example also demonstrates that bean meta attributes may be used instead of the <qualifier/> sub-elements. If available, the <qualifier/> and its attributes take precedence, but the autowiring mechanism falls back on the values provided within the <meta/> tags if no such qualifier is present, as in the last two bean definitions in the following example.
```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns:context="http://www.springframework.org/schema/context"
    xsi:schemaLocation="http://www.springframework.org/schema/beans
        http://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/context
        http://www.springframework.org/schema/context/spring-context.xsd">

    <context:annotation-config/>

    <bean class="example.SimpleMovieCatalog">
        <qualifier type="MovieQualifier">
            <attribute key="format" value="VHS"/>
            <attribute key="genre" value="Action"/>
        </qualifier>
        <!-- inject any dependencies required by this bean -->
    </bean>

    <bean class="example.SimpleMovieCatalog">
        <qualifier type="MovieQualifier">
            <attribute key="format" value="VHS"/>
            <attribute key="genre" value="Comedy"/>
        </qualifier>
        <!-- inject any dependencies required by this bean -->
    </bean>

    <bean class="example.SimpleMovieCatalog">
        <meta key="format" value="DVD"/>
        <meta key="genre" value="Action"/>
        <!-- inject any dependencies required by this bean -->
    </bean>

    <bean class="example.SimpleMovieCatalog">
        <meta key="format" value="BLURAY"/>
        <meta key="genre" value="Comedy"/>
        <!-- inject any dependencies required by this bean -->
    </bean>

</beans>
```
### 7.9.5 使用泛型作为自动装配限定符
7.9.5 Using generics as autowiring qualifiers
除了@Qualifier注解，也可以使用Java的泛型类型作为一种显式的限定。例如，假设有如下配置：

In addition to the @Qualifier annotation, it is also possible to use Java generic types as an implicit form of qualification. For example, suppose you have the following configuration:
```java
@Configuration
public class MyConfiguration {

    @Bean
    public StringStore stringStore() {
        return new StringStore();
    }

    @Bean
    public IntegerStore integerStore() {
        return new IntegerStore();
    }
}
```
假设，上述bean实现了泛型接口，例如Store<String>和Store<Integer>，可以使用@Autowired装配Store接口，并且泛型会作为一种限定：

Assuming that beans above implement a generic interface, i.e. Store<String> and Store<Integer>, you can @Autowire the Store interface and the generic will be used as a qualifier:
```java
@Autowired
private Store<String> s1; // <String> qualifier, injects the stringStore bean

@Autowired
private Store<Integer> s2; // <Integer> qualifier, injects the integerStore bean
```
泛型限定符也可以用于自动装配的List、Map或数组：

Generic qualifiers also apply when autowiring Lists, Maps and Arrays:
```java
// Inject all Store beans as long as they have an <Integer> generic
// Store<String> beans will not appear in this list
@Autowired
private List<Store<Integer>> s;
```
### 7.9.6 CustomAutowireConfigurer
CustomAutowireConfigurer是一个BeanFactoryPostProcessor，它可以注册自定义的限定符注解类型，甚至它们没有被Spring的@Qualifier注解所注解。

The CustomAutowireConfigurer is a BeanFactoryPostProcessor that enables you to register your own custom qualifier annotation types even if they are not annotated with Spring’s @Qualifier annotation.
```xml
<bean id="customAutowireConfigurer"
        class="org.springframework.beans.factory.annotation.CustomAutowireConfigurer">
    <property name="customQualifierTypes">
        <set>
            <value>example.CustomQualifier</value>
        </set>
    </property>
</bean>
```
AutowireCandidateResolver通过以下方式决定了自动装配的候选者：

The AutowireCandidateResolver determines autowire candidates by:
* 每个bean定义的auto-candidate值
* <beans/>元素上定义的default-autowire-candidates模式
* 使用了@Qualifier注解或任何在CustomAutowireConfigurer中注册的自定义注解

* the autowire-candidate value of each bean definition
* any default-autowire-candidates pattern(s) available on the <beans/> element
* the presence of @Qualifier annotations and any custom annotations registered with the CustomAutowireConfigurer

当多个bean被限定为候选者时，主要决定因素如下：如果这些候选者中有一个bean定义上明确地设置了primay属性为true，那么它将被选择。

When multiple beans qualify as autowire candidates, the determination of a "primary" is the following: if exactly one bean definition among the candidates has a primary attribute set to true, it will be selected.

### 7.9.7 @Resource
Spring也支持使用JSR-250的@Resource注解在字段和setter方法上进行注入。这在Java EE 5和6中是一种通用的模式，例如，在JSF 1.2管理的bean或JAX-WS 2.0终端。Spring也同样支持这种模式来管理对象。

Spring also supports injection using the JSR-250 @Resource annotation on fields or bean property setter methods. This is a common pattern in Java EE 5 and 6, for example in JSF 1.2 managed beans or JAX-WS 2.0 endpoints. Spring supports this pattern for Spring-managed objects as well.

@Resource拥有一个name属性，默认地，Spring会把这个name属性的值解释为将要注入的bean的名字。换句话说，它按照名字的语法进行注入，如下例所示：

@Resource takes a name attribute, and by default Spring interprets that value as the bean name to be injected. In other words, it follows by-name semantics, as demonstrated in this example:
```java
public class SimpleMovieLister {

    private MovieFinder movieFinder;

    @Resource(name="myMovieFinder")
    public void setMovieFinder(MovieFinder movieFinder) {
        this.movieFinder = movieFinder;
    }
}
```
如果没有提供名字，默认的名字从字段名或setter方法名派生而来。对于一个字段，它会取字段的名字，对于stter方法，它会取bean的属性名。所以，下面的方法会使用名字为“movieFinder”的bean注入到setter方法中。

If no name is specified explicitly, the default name is derived from the field name or setter method. In case of a field, it takes the field name; in case of a setter method, it takes the bean property name. So the following example is going to have the bean with name "movieFinder" injected into its setter method:
```java
public class SimpleMovieLister {

    private MovieFinder movieFinder;

    @Resource
    public void setMovieFinder(MovieFinder movieFinder) {
        this.movieFinder = movieFinder;
    }
}
```
[Note]
> 这个注解提供的名字会被ApplicationContext的CommonAnnotationBeanPostProcessor解析为一个bean的名字。如果显式地配置了Spring的SimpleJndiBeanFactory，这个名字也可以通过JNDI进行解析。但是，我们建议你使用默认的行为，简单地使用Spring的JNDI查找能力来间接寻址。
> The name provided with the annotation is resolved as a bean name by the ApplicationContext of which the CommonAnnotationBeanPostProcessor is aware. The names can be resolved through JNDI if you configure Spring’s SimpleJndiBeanFactory explicitly. However, it is recommended that you rely on the default behavior and simply use Spring’s JNDI lookup capabilities to preserve the level of indirection.

如果@Resource没有显式地指定名字，与@Autowired类似，它会寻找主要的（primary）类型匹配如果没有找到默认的名字，并解决众所周知的依赖：BeanFactory, ApplicationContext, ResourceLoader, ApplicationEventPublisher, 和MessageSource接口。

In the exclusive case of @Resource usage with no explicit name specified, and similar to @Autowired, @Resource finds a primary type match instead of a specific named bean and resolves well-known resolvable dependencies: the BeanFactory, ApplicationContext, ResourceLoader, ApplicationEventPublisher, and MessageSource interfaces.

因此，下面的例子中customerPreferenceDao字段首先会寻找名字为customerPreferenceDao的bean，然后才会寻找接口CustomerPreferenceDao的主要的类型匹配。同样地，“context”字段会寻找ApplicationContext类型的已知的可解析的依赖。

Thus in the following example, the customerPreferenceDao field first looks for a bean named customerPreferenceDao, then falls back to a primary type match for the type CustomerPreferenceDao. The "context" field is injected based on the known resolvable dependency type ApplicationContext.

public class MovieRecommender {

    @Resource
    private CustomerPreferenceDao customerPreferenceDao;

    @Resource
    private ApplicationContext context;

    public MovieRecommender() {
    }

    // ...
}

### 7.9.8 @PostConstruct and @PreDestroy
CommonAnnotationBeanPostProcessor不仅能够识别到@Resource注解，还能识别到JSR-250的生命周期注解。这是在Spring 2.5引入的，这项支持为初始化回调及销毁回调又提供了一种替代方案。CommonAnnotationBeanPostProcessor是在ApplicationContext中注册的，因此带有这些注解的方法会与Spring自身的生命周期接口方法或显式声明的回调方法在同样调用。下面的例子中，缓存会在初始化的时候设置并在销毁时清除。

The CommonAnnotationBeanPostProcessor not only recognizes the @Resource annotation but also the JSR-250 lifecycle annotations. Introduced in Spring 2.5, the support for these annotations offers yet another alternative to those described in initialization callbacks and destruction callbacks. Provided that the CommonAnnotationBeanPostProcessor is registered within the Spring ApplicationContext, a method carrying one of these annotations is invoked at the same point in the lifecycle as the corresponding Spring lifecycle interface method or explicitly declared callback method. In the example below, the cache will be pre-populated upon initialization and cleared upon destruction.
```java
public class CachingMovieLister {

    @PostConstruct
    public void populateMovieCache() {
        // populates the movie cache upon initialization...
    }

    @PreDestroy
    public void clearMovieCache() {
        // clears the movie cache upon destruction...
    }
}
```
[Note]
关于组合使用不同的生命周期机制的详细信息，请参考组合使用生命周期机制。

For details about the effects of combining various lifecycle mechanisms, see the section called “Combining lifecycle mechanisms”.

## 7.10 类路径扫描及管理的组件
7.10 Classpath scanning and managed components

本章的大部分例子都将采用XML的形式配置元数据。上一节（7.9 基于注解的容器配置）描述了怎么在源码级别提供配置。即便如此，基本的配置还是得通过XML来配置，注解仅仅用于驱动依赖注入。本节提供了一种隐式地通过扫描类路径检测候选组件的方式。候选组件是那些符合相应过滤规则并与容器通信的类。这种方式可以让我们不再通过XML的形式执行bean的注册，而是采用注解（比如@Component）、AspectJ表达式或自定义的过滤规则来选择哪些类将被注册到容器中。

Most examples in this chapter use XML to specify the configuration metadata that produces each BeanDefinition within the Spring container. The previous section (Section 7.9, “Annotation-based container configuration”) demonstrates how to provide a lot of the configuration metadata through source-level annotations. Even in those examples, however, the "base" bean definitions are explicitly defined in the XML file, while the annotations only drive the dependency injection. This section describes an option for implicitly detecting the candidate components by scanning the classpath. Candidate components are classes that match against a filter criteria and have a corresponding bean definition registered with the container. This removes the need to use XML to perform bean registration; instead you can use annotations (for example @Component), AspectJ type expressions, or your own custom filter criteria to select which classes will have bean definitions registered with the container.

[Note]
> 从Spring 3.0开始，许多由Spring的Java配置项目提供的功能都成为了Spring的核心部分。这使得我们可以通过Java而不是XML形式定义bean。参考@Configuration， @Bean， @Import和@DependsOn注解，来看一看怎么使用这些新功能。
> Starting with Spring 3.0, many features provided by the Spring JavaConfig project are part of the core Spring Framework. This allows you to define beans using Java rather than using the traditional XML files. Take a look at the @Configuration, @Bean, @Import, and @DependsOn annotations for examples of how to use these new features.

### 7.10.1 @Component及其扩展注解
7.10.1 @Component and further stereotype annotations

@Repository注解是一种用于标识存储类（也被称为数据访问对象或者DAO）的标记。异常的自动翻译是这个标记的用法之一，参考20.2.2 异常翻译。

The @Repository annotation is a marker for any class that fulfills the role or stereotype of a repository (also known as Data Access Object or DAO). Among the uses of this marker is the automatic translation of exceptions as described in Section 20.2.2, “Exception translation”.

Spring提供了一些扩展注解：@Component， @Service和@Controller。@Component可用于管理任何Spring的组件。@Repository， @Service和@Controller是@Component用于指定用例的特殊形式，比如，在持久层、服务层和表现层。使用@Service或@Controller能够让你的类更易于被合适的工具处理或与切面（aspect）关联。比如，这些注解可以使目标组件成为切入点。当然，@Repository， @Service和@Controller也能携带更多的语义。因此，如果你还在考虑使用@Component还是@Service用于注解service层，那么就选@Service吧，它更清晰。同样地，如前面所述，@Repository还能够用于在持久层标记自动异常翻译。

Spring provides further stereotype annotations: @Component, @Service, and @Controller. @Component is a generic stereotype for any Spring-managed component. @Repository, @Service, and @Controller are specializations of @Component for more specific use cases, for example, in the persistence, service, and presentation layers, respectively. Therefore, you can annotate your component classes with @Component, but by annotating them with @Repository, @Service, or @Controller instead, your classes are more properly suited for processing by tools or associating with aspects. For example, these stereotype annotations make ideal targets for pointcuts. It is also possible that @Repository, @Service, and @Controller may carry additional semantics in future releases of the Spring Framework. Thus, if you are choosing between using @Component or @Service for your service layer, @Service is clearly the better choice. Similarly, as stated above, @Repository is already supported as a marker for automatic exception translation in your persistence layer.

### 7.10.2 元注解
7.10.2 Meta-annotations

Spring提供了很多注解可用于元注解。元注解即一种可用于别的注解之上的注解。例如，@Service就是一种被元注解@Component注解的注解：

Many of the annotations provided by Spring can be used as meta-annotations in your own code. A meta-annotation is simply an annotation that can be applied to another annotation. For example, the @Service annotation mentioned above is meta-annotated with @Component:
```java
@Target(ElementType.TYPE)
@Retention(RetentionPolicy.RUNTIME)
@Documented
@Component // Spring will see this and treat @Service in the same way as @Component
public @interface Service {

    // ....
}
```
元注解也可以组合起来形成组合注解。例如，@RestController注解是一种@Controller与@ResponseBody组合的注解。

Meta-annotations can also be combined to create composed annotations. For example, the @RestController annotation from Spring MVC is composed of @Controller and @ResponseBody.

另外，组合注解也可以重新定义来自元注解的属性。这在只想暴露元注解的部分属性值的时候非常有用。例如，Spring的@SessionScope注解把它的作用域硬编码为session，但是仍然允许自定义proxyMode。

In addition, composed annotations may optionally redeclare attributes from meta-annotations to allow user customization. This can be particularly useful when you want to only expose a subset of the meta-annotation’s attributes. For example, Spring’s @SessionScope annotation hardcodes the scope name to session but still allows customization of the proxyMode.
```java
@Target({ElementType.TYPE, ElementType.METHOD})
@Retention(RetentionPolicy.RUNTIME)
@Documented
@Scope(WebApplicationContext.SCOPE_SESSION)
public @interface SessionScope {

    /**
     * Alias for {@link Scope#proxyMode}.
     * <p>Defaults to {@link ScopedProxyMode#TARGET_CLASS}.
     */
    @AliasFor(annotation = Scope.class)
    ScopedProxyMode proxyMode() default ScopedProxyMode.TARGET_CLASS;

}
```
@SessionScope然后就可以使用了，而且不需要提供proxyMode，如下：

@SessionScope can then be used without declaring the proxyMode as follows:
```java
@Service
@SessionScope
public class SessionScopedService {
    // ...
}
```
或者重写proxyMode的值，如下：

Or with an overridden value for the proxyMode as follows:
```java
@Service
@SessionScope(proxyMode = ScopedProxyMode.INTERFACES)
public class SessionScopedUserService implements UserService {
    // ...
}
```
更多信息请参考Spring注解编程模型。

For further details, consult the Spring Annotation Programming Model.

### 7.10.3 自动检测类并注册bean定义
7.10.3 Automatically detecting classes and registering bean definitions
Spring能够自动检测被注解的类，并把它们注册到ApplicationContext中。例如，下面的两个会被自动检测到：

Spring can automatically detect stereotyped classes and register corresponding BeanDefinitions with the ApplicationContext. For example, the following two classes are eligible for such autodetection:
```java
@Service
public class SimpleMovieLister {

    private MovieFinder movieFinder;

    @Autowired
    public SimpleMovieLister(MovieFinder movieFinder) {
        this.movieFinder = movieFinder;
    }
}
@Repository
public class JpaMovieFinder implements MovieFinder {
    // implementation elided for clarity
}
```
为了能够自动检测到这些类并注册它们，需要为@Configuration类添加@ComponentScan注解，并设置它的basePackage属性为这两个类所在的父包（替代方案，也可以使用逗号、分号、空格分割这两个类所在的包）。

To autodetect these classes and register the corresponding beans, you need to add @ComponentScan to your @Configuration class, where the basePackages attribute is a common parent package for the two classes. (Alternatively, you can specify a comma/semicolon/space-separated list that includes the parent package of each class.)
```java
@Configuration
@ComponentScan(basePackages = "org.example")
public class AppConfig  {
    ...
}
```
[Note]
> 上面的配置也可以简单地使用这个注解的value属性，例如：ComponentScan(“org.example”)
> For concision, the above may have used the value attribute of the annotation, i.e. @ComponentScan("org.example")

也可以使用XML形式的配置：

The following is an alternative using XML
```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns:context="http://www.springframework.org/schema/context"
    xsi:schemaLocation="http://www.springframework.org/schema/beans
        http://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/context
        http://www.springframework.org/schema/context/spring-context.xsd">

    <context:component-scan base-package="org.example"/>

</beans>
```
[Tip]
> 使用<context:component-scan>隐式地允许<context:annotation-config>的功能。因此，使用<context:component-scan>时一般就不需要再包含<context:annotation-config>元素了。

> The use of <context:component-scan> implicitly enables the functionality of <context:annotation-config>. There is usually no need to include the <context:annotation-config> element when using <context:component-scan>.

[Note]
> 类路径扫描的包必须保证这些包出现在classpath中。当使用Ant构建JAR包时，请确定不要激活仅仅使用文件的开关。同样地，类路径目录可能在某些环境下基于安全考虑不允许暴露，基于JDK 1.7.0_45及更高版本的app（需要在清单中设置信任库，参考http://stackoverflow.com/questions/19394570/java-jre-7u45-breaks-classloader-getresources）。

> The scanning of classpath packages requires the presence of corresponding directory entries in the classpath. When you build JARs with Ant, make sure that you do not activate the files-only switch of the JAR task. Also, classpath directories may not get exposed based on security policies in some environments, e.g. standalone apps on JDK 1.7.0_45 and higher (which requires 'Trusted-Library' setup in your manifests; see http://stackoverflow.com/questions/19394570/java-jre-7u45-breaks-classloader-getresources).

另外，使用component-scan元素时默认也启用了AutowiredAnnotationBeanPostProcessor和CommonAnnotationBeanPostProcessor。这意味着这两个组件被自动检测到了且不需要在XML中配置任何元数据。

Furthermore, the AutowiredAnnotationBeanPostProcessor and CommonAnnotationBeanPostProcessor are both included implicitly when you use the component-scan element. That means that the two components are autodetected and wired together - all without any bean configuration metadata provided in XML.

[Note]
> 可以使用annotation-config元素并设置其属性为false来禁用AutowiredAnnotationBeanPostProcessor和CommonAnnotationBeanPostProcessor。

> You can disable the registration of AutowiredAnnotationBeanPostProcessor and CommonAnnotationBeanPostProcessor by including the annotation-config attribute with a value of false.

### 7.10.4 使用过滤器自定义扫描
7.10.4 Using filters to customize scanning

默认地，只有使用注解@Component, @Repository, @Service, @Controller或自定义注解注解的类才能被检测为候选组件。然而，我们可以使用自定义的过滤器修改并扩展这种行为。添加这些过滤器到@ComponentScan注解的includeFilters或excludeFilters参数即可（或component-scan元素的子元素include-filter或exclude-filter）。每个过滤器元素都需要type和expression属性。下表描述了相关的选项： 

By default, classes annotated with @Component, @Repository, @Service, @Controller, or a custom annotation that itself is annotated with @Component are the only detected candidate components. However, you can modify and extend this behavior simply by applying custom filters. Add them as includeFilters or excludeFilters parameters of the @ComponentScan annotation (or as include-filter or exclude-filter sub-elements of the component-scan element). Each filter element requires the type and expression attributes. The following table describes the filtering options.

Table 7.5. Filter Types

Filter Type | Example Expression |	Description
-- | -- | --
annotation (default) |org.example.SomeAnnotation|An annotation to be present at the type level in target components. 	目标组件类级别的注解
assignable|org.example.SomeClass|A class (or interface) that the target components are assignable to (extend/implement). 目标组件继承或实现的类或接口
aspectj|org.example..*Service+|An AspectJ type expression to be matched by the target components. 用于匹配目标组件的AspecJ类型表达式
regex|org\.example\.Default.*|A regex expression to be matched by the target components class names. (用于匹配目标组件类名的正则表达式)
custom|org.example.MyTypeFilter | A custom implementation of the org.springframework.core.type .TypeFilter interface.

下面的例子展示了如何忽略掉所有的@Repository注解，并使用带有“stub”的Repository代替：

The following example shows the configuration ignoring all @Repository annotations and using "stub" repositories instead.
```java
@Configuration
@ComponentScan(basePackages = "org.example",
        includeFilters = @Filter(type = FilterType.REGEX, pattern = ".*Stub.*Repository"),
        excludeFilters = @Filter(Repository.class))
public class AppConfig {
    ...
}
```
或者使用XML形式配置：

and the equivalent using XML
```xml
<beans>
    <context:component-scan base-package="org.example">
        <context:include-filter type="regex"
                expression=".*Stub.*Repository"/>
        <context:exclude-filter type="annotation"
                expression="org.springframework.stereotype.Repository"/>
    </context:component-scan>
</beans>
```
[Note]
> 也可以设置这个注解的useDefaultFilters=false或为<component-scan/>元素提供属性use-default-filters=”false”忽略掉默认的过滤器。这将不会自动检测带有@Component, @Repository, @Service, @Controller或@Configuration注解的类。

> You can also disable the default filters by setting useDefaultFilters=false on the annotation or providing use-default-filters="false" as an attribute of the <component-scan/> element. This will in effect disable automatic detection of classes annotated with @Component, @Repository, @Service, @Controller, or @Configuration.

### 7.10.5 在组件内部定义bean元数据
7.10.5 Defining bean metadata within components

Spring的组件也可以为容器贡献bean的定义元数据，只要在@Component注解的类内部使用@Bean注解即可。下面是一个简单的例子：

Spring components can also contribute bean definition metadata to the container. You do this with the same @Bean annotation used to define bean metadata within @Configuration annotated classes. Here is a simple example:
```java
@Component
public class FactoryMethodComponent {

    @Bean
    @Qualifier("public")
    public TestBean publicInstance() {
        return new TestBean("publicInstance");
    }

    public void doWork() {
        // Component method implementation omitted
    }
}
```
这个类是Spring的一个组件，它包含一个应用相关的方法doWork()。但是，它也通过方法publicInstance()贡献了一个bean定义。@Bean注解标识了这个工厂方法和其它的bean属性，比如@Qualifier注解的限定符。其它可用于此处的方法级别注解还有@Scope， @Lazy及自定义注解等。

This class is a Spring component that has application-specific code contained in its doWork() method. However, it also contributes a bean definition that has a factory method referring to the method publicInstance(). The @Bean annotation identifies the factory method and other bean definition properties, such as a qualifier value through the @Qualifier annotation. Other method level annotations that can be specified are @Scope, @Lazy, and custom qualifier annotations.

[Tip]
> 除了扮演组件初始化的角色，@Lazy注解还可以放置在被@Autowired或@Inject标记的注入点。在这种情况下，它会使得注入使用延迟代理。

> In addition to its role for component initialization, the @Lazy annotation may also be placed on injection points marked with @Autowired or @Inject. In this context, it leads to the injection of a lazy-resolution proxy.

自动装配的字段和方法也可以像前面讨论的一样被支持，也可以支持@Bean方法的自动装配：

Autowired fields and methods are supported as previously discussed, with additional support for autowiring of @Bean methods:
```java
@Component
public class FactoryMethodComponent {

    private static int i;

    @Bean
    @Qualifier("public")
    public TestBean publicInstance() {
        return new TestBean("publicInstance");
    }

    // use of a custom qualifier and autowiring of method parameters
    @Bean
    protected TestBean protectedInstance(
            @Qualifier("public") TestBean spouse,
            @Value("#{privateInstance.age}") String country) {
        TestBean tb = new TestBean("protectedInstance", 1);
        tb.setSpouse(spouse);
        tb.setCountry(country);
        return tb;
    }

    @Bean
    private TestBean privateInstance() {
        return new TestBean("privateInstance", i++);
    }

    @Bean
    @RequestScope
    public TestBean requestScopedInstance() {
        return new TestBean("requestScopedInstance", 3);
    }
}
```
上面的例子使用了另一个叫作privateInstance的bean的Age属性自动装配了String类型的参数country。Spring的表达式语言使用#{}的记法定义了这个属性的值。对于@Value注解，提前配置的表达式解析器会在需要解析表达式文本的时候寻找bean的名字。

The example autowires the String method parameter country to the value of the age property on another bean named privateInstance. A Spring Expression Language element defines the value of the property through the notation #{ <expression> }. For @Value annotations, an expression resolver is preconfigured to look for bean names when resolving expression text.

As of Spring Framework 4.3, you may also declare a factory method parameter of type InjectionPoint (or its more specific subclass DependencyDescriptor) in order to access the requesting injection point that triggers the creation of the current bean. Note that this will only apply to the actual creation of bean instances, not to the injection of existing instances. As a consequence, this feature makes most sense for beans of prototype scope. For other scopes, the factory method will only ever see the injection point which triggered the creation of a new bean instance in the given scope: for example, the dependency that triggered the creation of a lazy singleton bean. Use the provided injection point metadata with semantic care in such scenarios.
```java
@Component
public class FactoryMethodComponent {

    @Bean @Scope("prototype")
    public TestBean prototypeInstance(InjectionPoint injectionPoint) {
        return new TestBean("prototypeInstance for " + injectionPoint.getMember());
    }
}
```
在Spring组件内部的@Bean方法的处理不同于使用@Configuration注解的类内部的@Bean方法。不同之处是@Component类不会使用CGLIB拦截调用的方法和字段从而进行增强。CGLIB代理在调用@Configuration类中的@Bean方法时会创建对合作对象的引用，这种方法的调用不会通过正常的Java语法调用，而是通过容器以便提供生命周期管理，甚至在通过编程地方式调用@Bean方法时也会形成对其它bean的引用。相反，调用普通的@Component类中的@Bean方法只会形成标准的Java语法调用，不会有特殊的CGLIB处理过程及其它的限制条件。

The @Bean methods in a regular Spring component are processed differently than their counterparts inside a Spring @Configuration class. The difference is that @Component classes are not enhanced with CGLIB to intercept the invocation of methods and fields. CGLIB proxying is the means by which invoking methods or fields within @Bean methods in @Configuration classes creates bean metadata references to collaborating objects; such methods are not invoked with normal Java semantics but rather go through the container in order to provide the usual lifecycle management and proxying of Spring beans even when referring to other beans via programmatic calls to @Bean methods. In contrast, invoking a method or field in an @Bean method within a plain @Component class has standard Java semantics, with no special CGLIB processing or other constraints applying.

[Note]
> 你可能会定义@Bean方法为静态的，这样就不用创建包含它的类的实例了。这在定义后置处理器bean时会形成特殊的情况，比如BeanFactoryPostProcessor或BeanPostProcessor，因为这类bean会在容器的生命周期前期被初始化，而不会触发其它部分的配置。 
> 注意，对静态@Bean方法的调用永远不会被容器拦截，即使在@Configuration类内部。这是由于技术上的瓶颈：CGLIB的子类代理只会重写非静态方法。因此，对另一个@Bean方法的直接调用只有标准的Java语法，只会从工厂方法本身直接返回一个独立的实例。 
> 由于Java语言的可见性，@Bean方法并不一定会对容器中的bean有效。你可能很随意的在非@Configuration类中定义或定义为静态方法。然而，在@Configuration类中的正常的@Bean方法都需要被重写的，因此，它们不应该定义为private或final。 
> @Bean方法也可以在父类中被发现，同样适用于Java 8中接口的默认方法。这使得组建复杂的配置时能具有更好的灵活性，甚至可能通过Java 8的默认方法实现多重继承，这在Spring 4.2开始支持。 
最后，注意一个类中可能会存在相同bean的多个@Bean方法，这会在运行时选择合适的工厂方法。使用的算法时选择“最贪婪”的构造方法，一些场景可能会按如下方法选择相应的工厂方法：满足最多依赖的会被选择，这与使用@Autowired时选择多个构造方法时类似。
> You may declare @Bean methods as static, allowing for them to be called without creating their containing configuration class as an instance. This makes particular sense when defining post-processor beans, e.g. of type BeanFactoryPostProcessor or BeanPostProcessor, since such beans will get initialized early in the container lifecycle and should avoid triggering other parts of the configuration at that point.

> Note that calls to static @Bean methods will never get intercepted by the container, not even within @Configuration classes (see above). This is due to technical limitations: CGLIB subclassing can only override non-static methods. As a consequence, a direct call to another @Bean method will have standard Java semantics, resulting in an independent instance being returned straight from the factory method itself.

> The Java language visibility of @Bean methods does not have an immediate impact on the resulting bean definition in Spring’s container. You may freely declare your factory methods as you see fit in non-@Configuration classes and also for static methods anywhere. However, regular @Bean methods in @Configuration classes need to be overridable, i.e. they must not be declared as private or final.

> @Bean methods will also be discovered on base classes of a given component or configuration class, as well as on Java 8 default methods declared in interfaces implemented by the component or configuration class. This allows for a lot of flexibility in composing complex configuration arrangements, with even multiple inheritance being possible through Java 8 default methods as of Spring 4.2.

> Finally, note that a single class may hold multiple @Bean methods for the same bean, as an arrangement of multiple factory methods to use depending on available dependencies at runtime. This is the same algorithm as for choosing the "greediest" constructor or factory method in other configuration scenarios: The variant with the largest number of satisfiable dependencies will be picked at construction time, analogous to how the container selects between multiple @Autowired constructors.

### 7.10.6 命名自动检测的组件
7.10.6 Naming autodetected components

当一个组件被扫描过程自动检测到时，它的名字由BeanNameGenerator定义的策略生成。默认地，Spring的扩展注解（@Component, @Repository, @Service和@Controller）都包含一个value属性，这个value值会提供一个名字以便通信。

When a component is autodetected as part of the scanning process, its bean name is generated by the BeanNameGenerator strategy known to that scanner. By default, any Spring stereotype annotation (@Component, @Repository, @Service, and @Controller) that contains a name value will thereby provide that name to the corresponding bean definition.

如果这样的注解没有明确地提供value值，或者另外一些检测到的组件（比如自定义过滤器扫描到的组件），那么默认生成器会返回一个首字母小写的短路径的类名。比如，下面两个组件，它们的名字分别为myMovieLister和movieFinderImpl：

If such an annotation contains no name value or for any other detected component (such as those discovered by custom filters), the default bean name generator returns the uncapitalized non-qualified class name. For example, if the following component classes were detected, the names would be myMovieLister and movieFinderImpl:
```java
@Service("myMovieLister")
public class SimpleMovieLister {
    // ...
}
@Repository
public class MovieFinderImpl implements MovieFinder {
    // ...
}
```
[Note]
> 如果不想遵循默认的名字生成策略，也可以提供自定义的策略。首先，需要实现BeanNameGenerator接口，并且要包含一个无参构造方法。然后，配置扫描器时为其指定这个自定义生成器的全路径：
> If you do not want to rely on the default bean-naming strategy, you can provide a custom bean-naming strategy. First, implement the BeanNameGenerator interface, and be sure to include a default no-arg constructor. Then, provide the fully-qualified class name when configuring the scanner:
```java
@Configuration
@ComponentScan(basePackages = "org.example", nameGenerator = MyNameGenerator.class)
public class AppConfig {
    ...
}
```
```xml
<beans>
    <context:component-scan base-package="org.example"
        name-generator="org.example.MyNameGenerator" />
</beans>
```
一般地，当其它的组件可能会明确地引用这个组件时为其注解提供一个名字是个很好地方式。另外，当容器装配时自动生成的名字足够用了。

As a general rule, consider specifying the name with the annotation whenever other components may be making explicit references to it. On the other hand, the auto-generated names are adequate whenever the container is responsible for wiring.

### 7.10.7 为自动检测的组件提供作用域
7.10.7 Providing a scope for autodetected components

一般Spring管理的组件的作用域默认为singleton。但是，有时可能会需要不同的作用域，这时可以通过@Scope注解来声明：

As with Spring-managed components in general, the default and most common scope for autodetected components is singleton. However, sometimes you need a different scope which can be specified via the @Scope annotation. Simply provide the name of the scope within the annotation:
```java
@Scope("prototype")
@Repository
public class MovieFinderImpl implements MovieFinder {
    // ...
}
```
[Note]
@Scope annotations are only introspected on the concrete bean class (for annotated components) or the factory method (for @Bean methods). In contrast to XML bean definitions, there is no notion of bean definition inheritance, and inheritance hierarchies at the class level are irrelevant for metadata purposes.

更详细的信息请参考7.5.4 Request, session, global session, application和WebSocket作用域。

For details on web-specific scopes such as "request"/"session" in a Spring context, see Section 7.5.4, “Request, session, global session, application, and WebSocket scopes”. Like the pre-built annotations for those scopes, you may also compose your own scoping annotations using Spring’s meta-annotation approach: e.g. a custom annotation meta-annotated with @Scope("prototype"), possibly also declaring a custom scoped-proxy mode.

[Note]
> 也可以自定义策略处理作用域而不是依靠这种注解的方法，实现ScopeMetadataResolver接口，并包含一个默认的无参构造方法，然后在配置扫描器的时候提供其全路径即可。 
> To provide a custom strategy for scope resolution rather than relying on the annotation-based approach, implement the ScopeMetadataResolver interface, and be sure to include a default no-arg constructor. Then, provide the fully-qualified class name when configuring the scanner:
```java
@Configuration
@ComponentScan(basePackages = "org.example", scopeResolver = MyScopeResolver.class)
public class AppConfig {
    ...
}
```
```xml
<beans>
    <context:component-scan base-package="org.example" scope-resolver="org.example.MyScopeResolver"/>
</beans>
```
当使用非单例作用域时，有必要为作用域内的对象生成代理。原因如有作用域的bean作为依赖项中描述。因此，component-scan元素需要指明scoped-proxy属性。有三种可选值：无，接口和目标类。例如，下面的配置将使用标准的JDK动态代理：

When using certain non-singleton scopes, it may be necessary to generate proxies for the scoped objects. The reasoning is described in the section called “Scoped beans as dependencies”. For this purpose, a scoped-proxy attribute is available on the component-scan element. The three possible values are: no, interfaces, and targetClass. For example, the following configuration will result in standard JDK dynamic proxies:
```java
@Configuration
@ComponentScan(basePackages = "org.example", scopedProxy = ScopedProxyMode.INTERFACES)
public class AppConfig {
    ...
}
```
```xml
<beans>
    <context:component-scan base-package="org.example" scoped-proxy="interfaces"/>
</beans>
```

### 7.10.8 使用注解提供限定符
7.10.8 Providing qualifier metadata with annotations

@Qualifier注解在7.9.4 使用限定符微调基于注解的自动装配中被讨论过。那节的例子中展示了如何使用@Qualifier注解，并展示了如何使用自定义的限定符注解提供更细粒度的控制。那些例子都是基于XML形式的，使用qualifier或meta子元素为bean提供限定符。同样地，也可以在类级别提供注解达到同样的效果。下面的三个例子展示了用法：

The @Qualifier annotation is discussed in Section 7.9.4, “Fine-tuning annotation-based autowiring with qualifiers”. The examples in that section demonstrate the use of the @Qualifier annotation and custom qualifier annotations to provide fine-grained control when you resolve autowire candidates. Because those examples were based on XML bean definitions, the qualifier metadata was provided on the candidate bean definitions using the qualifier or meta sub-elements of the bean element in the XML. When relying upon classpath scanning for autodetection of components, you provide the qualifier metadata with type-level annotations on the candidate class. The following three examples demonstrate this technique:
```java
@Component
@Qualifier("Action")
public class ActionMovieCatalog implements MovieCatalog {
    // ...
}
@Component
@Genre("Action")
public class ActionMovieCatalog implements MovieCatalog {
    // ...
}
@Component
@Offline
public class CachingMovieCatalog implements MovieCatalog {
    // ...
}
```
[Note]
> 与大部分注解一样，请记住注解元数据是绑定到类定义本身的，然而XML形式允许为相同类型提供多个bean并绑定不同的限定符，因为XML的元数据是绑定到每个实例的而不是每个类。
> As with most annotation-based alternatives, keep in mind that the annotation metadata is bound to the class definition itself, while the use of XML allows for multiple beans of the same type to provide variations in their qualifier metadata, because that metadata is provided per-instance rather than per-class.

## 7.11 使用JSR 330标准注解
7.11 Using JSR 330 Standard Annotations

从Spring 3.0开始，Spring开始支持JSR-330的标准注解用于依赖注入。这些注解与Spring自带的注解一样被扫描。仅仅只需要引入相关的jar包即可。

Starting with Spring 3.0, Spring offers support for JSR-330 standard annotations (Dependency Injection). Those annotations are scanned in the same way as the Spring annotations. You just need to have the relevant jars in your classpath.

[Note]
> 如果使用Maven，javax.inject也可以在标准Maven仓库中找到，添加如下配置到pom.xml即可。

> If you are using Maven, the javax.inject artifact is available in the standard Maven repository ( http://repo1.maven.org/maven2/javax/inject/javax.inject/1/). You can add the following dependency to your file pom.xml:
```xml
<dependency>
    <groupId>javax.inject</groupId>
    <artifactId>javax.inject</artifactId>
    <version>1</version>
</dependency>
```

### 7.11.1 使用@Inject和@Named依赖注入
7.11.1 Dependency Injection with @Inject and @Named

可以像下面这样使用@javax.inject.Inject代替@Autowired：

Instead of @Autowired, @javax.inject.Inject may be used as follows:
```java
import javax.inject.Inject;

public class SimpleMovieLister {

    private MovieFinder movieFinder;

    @Inject
    public void setMovieFinder(MovieFinder movieFinder) {
        this.movieFinder = movieFinder;
    }

    public void listMovies() {
        this.movieFinder.findMovies(...);
        ...
    }
}
```
与@Autowired一样，可以在字段级别、方法级别或构造参数级别使用@Inject。另外，也可以定义注入点为Provider，以便按需访问短作用域的bean或通过调用Provider.get()延迟访问其它的bean。上面例子的一种变体如下：

As with @Autowired, it is possible to use @Inject at the field level, method level and constructor-argument level. Furthermore, you may declare your injection point as a Provider, allowing for on-demand access to beans of shorter scopes or lazy access to other beans through a Provider.get() call. As a variant of the example above:
```java
import javax.inject.Inject;
import javax.inject.Provider;

public class SimpleMovieLister {

    private Provider<MovieFinder> movieFinder;

    @Inject
    public void setMovieFinder(Provider<MovieFinder> movieFinder) {
        this.movieFinder = movieFinder;
    }

    public void listMovies() {
        this.movieFinder.get().findMovies(...);
        ...
    }
}
```

如果你喜欢为依赖添加一个限定符，也可以像下面这样使用@Named注解：

If you would like to use a qualified name for the dependency that should be injected, you should use the @Named annotation as follows:
```java
import javax.inject.Inject;
import javax.inject.Named;

public class SimpleMovieLister {

    private MovieFinder movieFinder;

    @Inject
    public void setMovieFinder(@Named("main") MovieFinder movieFinder) {
        this.movieFinder = movieFinder;
    }

    // ...
}
```
Like @Autowired, @Inject can also be used with java.util.Optional or @Nullable. This is even more applicable here since @Inject does not have a required attribute.
```java
public class SimpleMovieLister {

    @Inject
    public void setMovieFinder(Optional<MovieFinder> movieFinder) {
        ...
    }
}
public class SimpleMovieLister {

    @Inject
    public void setMovieFinder(@Nullable MovieFinder movieFinder) {
        ...
    }
}
```

### 7.11.2 @Named：与@Component注解等价
7.11.2 @Named and @ManagedBean: standard equivalents to the @Component annotation

可以像下面这样使用@javax.inject.Named代替@Component：

Instead of @Component, @javax.inject.Named or javax.annotation.ManagedBean may be used as follows:
```java
import javax.inject.Inject;
import javax.inject.Named;

@Named("movieListener")  // @ManagedBean("movieListener") could be used as well
public class SimpleMovieLister {

    private MovieFinder movieFinder;

    @Inject
    public void setMovieFinder(MovieFinder movieFinder) {
        this.movieFinder = movieFinder;
    }

    // ...
}
```
通常使用@Component都不指定名字，同样地@Named也可以这么用：

It is very common to use @Component without specifying a name for the component. @Named can be used in a similar fashion:
```java
import javax.inject.Inject;
import javax.inject.Named;

@Named
public class SimpleMovieLister {

    private MovieFinder movieFinder;

    @Inject
    public void setMovieFinder(MovieFinder movieFinder) {
        this.movieFinder = movieFinder;
    }

    // ...
}
```
使用@Named时，也可以像使用Spring注解一样使用组件扫描：

When using @Named or @ManagedBean, it is possible to use component scanning in the exact same way as when using Spring annotations:

@Configuration
@ComponentScan(basePackages = "org.example")
public class AppConfig  {
    ...
}
[Note]
> 与@Component不同的是，JSR-330的@Named注解不能组合成其它的注解，因此，如果需要构建自定义的注解，请使用Spring的注解。
> In contrast to @Component, the JSR-330 @Named and the JSR-250 ManagedBean annotations are not composable. Please use Spring’s stereotype model for building custom component annotations.

### 7.11.3 JSR-330标准注解的局限性
7.11.3 Limitations of JSR-330 standard annotations

使用标准注解时，应该要了解以下不支持的特性：

When working with standard annotations, it is important to know that some significant features are not available as shown in the table below:

__表 7.6. Spring组件模型与JSR-330变种的对比__

Table 7.6. Spring component model elements vs. JSR-330 variants

Spring|	javax.inject.*	|javax.inject restrictions / comments
-- | -- | --
@Autowired| @Inject|@Inject has no 'required' attribute; can be used with Java 8’s Optional instead. @Inject没有require属性，可以使用Java 8的Optional代替。
@Component|@Named / @ManagedBean|JSR-330 does not provide a composable model, just a way to identify named components. JSR-330没有提供组合模型，仅仅只是一种标识组件的方式
@Scope("singleton")|@Singleton|The JSR-330 default scope is like Spring’s prototype. However, in order to keep it consistent with Spring’s general defaults, a JSR-330 bean declared in the Spring container is a singleton by default. In order to use a scope other than singleton, you should use Spring’s @Scope annotation. javax.inject also provides a @Scope annotation. Nevertheless, this one is only intended to be used for creating your own annotations. JSR-330默认的作用域类似于Spring的prototype。然而，为了与Spring一般的配置的默认值保持一致，JSR-330配置的bean在Spring中默认为singleton。为了使用singleton以外的作用域，必须使用Spring的@Scope注解。javax.inject也提供了一个@Scope注解，不过这仅仅被用于创建自己的注解。
@Qualifier|@Qualifier / @Named|javax.inject.Qualifier is just a meta-annotation for building custom qualifiers. Concrete String qualifiers (like Spring’s @Qualifier with a value) can be associated through javax.inject.Named. javax.inject.Qualifier仅使用创建自定义的限定符。可以通过javax.inject.Named创建与Spring中@Qualifier一样的限定符
@Value|-|no equivalent
@Required|-|no equivalent
@Lazy|-|no equivalent
ObjectFactory|Provider|javax.inject.Provider is a direct alternative to Spring’s ObjectFactory, just with a shorter get() method name. It can also be used in combination with Spring’s @Autowired or with non-annotated constructors and setter methods. javax.inject.Provider是对Spring的ObjectFactory的直接替代，仅仅使用简短的get()方法即可。它也可以与Spring的@Autowired或无注解的构造方法和setter方法一起使用。

## 7.12 基于Java的容器配置
7.12 Java-based container configuration

### 7.12.1 基本概念：@Bean和@Configuration
7.12.1 Basic concepts: @Bean and @Configuration

Spring中基于Java的配置的核心内容是@Configuration注解的类和@Bean注解的方法。

The central artifacts in Spring’s new Java-configuration support are @Configuration-annotated classes and @Bean-annotated methods.

@Bean注解表示一个方法将会实例化、配置并初始化一个对象，且这个对象会被Spring容器管理。这就像在XML中<beans/>元素中<bean/>元素一样。@Bean注解可用于任何Spring的@Component注解的类中，但大部分都只用于@Configuration注解的类中。

The @Bean annotation is used to indicate that a method instantiates, configures and initializes a new object to be managed by the Spring IoC container. For those familiar with Spring’s <beans/> XML configuration the @Bean annotation plays the same role as the <bean/> element. You can use @Bean annotated methods with any Spring @Component, however, they are most often used with @Configuration beans.

注解了@Configuration的类表示这个类的目的就是作为bean定义的地方。另外，@Configuration类内部的bean可以调用本类中定义的其它bean作为依赖。最简单的配置大致如下：

Annotating a class with @Configuration indicates that its primary purpose is as a source of bean definitions. Furthermore, @Configuration classes allow inter-bean dependencies to be defined by simply calling other @Bean methods in the same class. The simplest possible @Configuration class would read as follows:
```java
@Configuration
public class AppConfig {

    @Bean
    public MyService myService() {
        return new MyServiceImpl();
    }
}
```
上面的AppConfig类与下面的XML形式是等价的：

The AppConfig class above would be equivalent to the following Spring <beans/> XML:
```xml
<beans>
    <bean id="myService" class="com.acme.services.MyServiceImpl"/>
</beans>
```
全量的@Configuration和简化的@Bean模式？  
Full @Configuration vs 'lite' @Bean mode?

> 当@Bean方法不定义在@Configuration的类中时，它们会被一种简化的模式处理。例如，定义在@Component类或普通类中的@Bean方法。

> When @Bean methods are declared within classes that are not annotated with @Configuration they are referred to as being processed in a 'lite' mode. Bean methods declared in a @Component or even in a plain old class will be considered 'lite', with a different primary purpose of the containing class and an @Bean method just being a sort of bonus there. For example, service components may expose management views to the container through an additional @Bean method on each applicable component class. In such scenarios, @Bean methods are a simple general-purpose factory method mechanism.

> 不同于全量的@Configuration模式，简化的@Bean方法不能轻易地使用别的依赖。通常在简化械下一个@Bean方法不会调用另一个@Bean方法。

> Unlike full @Configuration, lite @Bean methods cannot declare inter-bean dependencies. Instead, they operate on their containing component’s internal state and optionally on arguments that they may declare. Such an @Bean method should therefore not invoke other @Bean methods; each such method is literally just a factory method for a particular bean reference, without any special runtime semantics. The positive side-effect here is that no CGLIB subclassing has to be applied at runtime, so there are no limitations in terms of class design (i.e. the containing class may nevertheless be final etc).

> 推荐在@Configuration类中使用@Bean方法，从而保证全量模式总是起作用。这样可以防止同一个@Bean方法被无意中调用多次，并减少一些狡猾的bug。

> In common scenarios, @Bean methods are to be declared within @Configuration classes, ensuring that 'full' mode is always used and that cross-method references will therefore get redirected to the container’s lifecycle management. This will prevent the same @Bean method from accidentally being invoked through a regular Java call which helps to reduce subtle bugs that can be hard to track down when operating in 'lite' mode.

@Bean和@Configuration注解将会在下面的章节中详细讨论。首先，我们来看看基于Java配置以不同的方式创建Spring的容器。

The @Bean and @Configuration annotations will be discussed in depth in the sections below. First, however, we’ll cover the various ways of creating a spring container using Java-based configuration.

### 7.12.2 使用AnnotationConfigApplicationContext实例化Spring容器
7.12.2 Instantiating the Spring container using AnnotationConfigApplicationContext

下面的章节介绍Spring 3.0中引入的AnnotationConfigApplicationContext。这个ApplicationContext的实现不仅可以把@Configuration类作为输入，同样普通的@Component类和使用JSR-330注解的类也可以作为输入。

The sections below document Spring’s AnnotationConfigApplicationContext, new in Spring 3.0. This versatile ApplicationContext implementation is capable of accepting not only @Configuration classes as input, but also plain @Component classes and classes annotated with JSR-330 metadata.

当使用@Configuration类作为输入时，这个类本身及其下面的所有@Bean方法都会被注册为bean。

When @Configuration classes are provided as input, the @Configuration class itself is registered as a bean definition, and all declared @Bean methods within the class are also registered as bean definitions.

当@Component和JSR-330类作为输入时，它们会被注册为bean，并且假设在必要的时候使用了@Autowired或@Inject。

When @Component and JSR-330 classes are provided, they are registered as bean definitions, and it is assumed that DI metadata such as @Autowired or @Inject are used within those classes where necessary.

__简单的构造方法__

Simple construction

与使用ClassPathXmlApplicationContext注入XML文件一样，可以使用AnnotationConfigApplicationContext注入@Configuration类。这样就完全不用在Spring容器中使用XML了：

In much the same way that Spring XML files are used as input when instantiating a ClassPathXmlApplicationContext, @Configuration classes may be used as input when instantiating an AnnotationConfigApplicationContext. This allows for completely XML-free usage of the Spring container:
```java
public static void main(String[] args) {
    ApplicationContext ctx = new AnnotationConfigApplicationContext(AppConfig.class);
    MyService myService = ctx.getBean(MyService.class);
    myService.doStuff();
}
```
如前面所述，AnnotationConfigApplicationContext不限于只注入@Configuration类，任何@Component或JSR-330注解的类都能被提供给这个构造方法。例如：

As mentioned above, AnnotationConfigApplicationContext is not limited to working only with @Configuration classes. Any @Component or JSR-330 annotated class may be supplied as input to the constructor. For example:
```java
public static void main(String[] args) {
    ApplicationContext ctx = new AnnotationConfigApplicationContext(MyServiceImpl.class, Dependency1.class, Dependency2.class);
    MyService myService = ctx.getBean(MyService.class);
    myService.doStuff();
}
```
上面假设了MyServiceImpl， Dependency1， Dependency2使用了Spring的依赖注入注解比如@Autowired。

The above assumes that MyServiceImpl, Dependency1 and Dependency2 use Spring dependency injection annotations such as @Autowired.

__使用register(Class?>…​)__
Building the container programmatically using register(Class<?>…​)

An AnnotationConfigApplicationContext may be instantiated using a no-arg constructor and then configured using the register() method. This approach is particularly useful when programmatically building an AnnotationConfigApplicationContext.
```java
public static void main(String[] args) {
    AnnotationConfigApplicationContext ctx = new AnnotationConfigApplicationContext();
    ctx.register(AppConfig.class, OtherConfig.class);
    ctx.register(AdditionalConfig.class);
    ctx.refresh();
    MyService myService = ctx.getBean(MyService.class);
    myService.doStuff();
}
```
__使用scan(String…)扫描组件__
Enabling component scanning with scan(String…​)
To enable component scanning, just annotate your @Configuration class as follows:
```java
@Configuration
@ComponentScan(basePackages = "com.acme")
public class AppConfig  {
    ...
}
```
[Tip]
> 有经验的用户可能更熟悉使用等价的XML形式配置： 

> Experienced Spring users will be familiar with the XML declaration equivalent from Spring’s context: namespace
```xml
<beans>
    <context:component-scan base-package="com.acme"/>
</beans>
```
上面的例子中，com.acme包会被扫描，只要是使用了@Component注解的类，都会被注册进容器中。同样地，AnnotationConfigApplicationContext也暴露了scan(String…)方法用于扫描组件：

In the example above, the com.acme package will be scanned, looking for any @Component-annotated classes, and those classes will be registered as Spring bean definitions within the container. AnnotationConfigApplicationContext exposes the scan(String…​) method to allow for the same component-scanning functionality:
```java
public static void main(String[] args) {
    AnnotationConfigApplicationContext ctx = new AnnotationConfigApplicationContext();
    ctx.scan("com.acme");
    ctx.refresh();
    MyService myService = ctx.getBean(MyService.class);
}
```
[Note]
> 请记住@Configuration类是被@Component元注解注解的类，所以它们也会被扫描到。上面的例子中，假设AppConfig定义在com.acme包中（或更深的包中），调用scan()时它也会被扫描到，并且它里面配置的所有@Bean方法会在refresh()的时候被注册到容器中。

> Remember that @Configuration classes are meta-annotated with @Component, so they are candidates for component-scanning! In the example above, assuming that AppConfig is declared within the com.acme package (or any package underneath), it will be picked up during the call to scan(), and upon refresh() all its @Bean methods will be processed and registered as bean definitions within the container.

__使用AnnotationConfigWebApplicationContext支持web应用__
Support for web applications with AnnotationConfigWebApplicationContext

一个WebApplicationContext与AnnotationConfigApplicationContext的变种是AnnotationConfigWebApplicationContext。这个实现可以用于配置Spring的ContextLoaderListener的servlet监听器、Spring MVC的DispatcherServlet等。下面是一个典型的配置Spring MVC web应用的片段。注意包含contextClass的context-param和init-param的用法：

A WebApplicationContext variant of AnnotationConfigApplicationContext is available with AnnotationConfigWebApplicationContext. This implementation may be used when configuring the Spring ContextLoaderListener servlet listener, Spring MVC DispatcherServlet, etc. What follows is a web.xml snippet that configures a typical Spring MVC web application. Note the use of the contextClass context-param and init-param:
```xml
<web-app>
    <!-- Configure ContextLoaderListener to use AnnotationConfigWebApplicationContext
        instead of the default XmlWebApplicationContext -->
    <context-param>
        <param-name>contextClass</param-name>
        <param-value>
            org.springframework.web.context.support.AnnotationConfigWebApplicationContext
        </param-value>
    </context-param>

    <!-- Configuration locations must consist of one or more comma- or space-delimited
        fully-qualified @Configuration classes. Fully-qualified packages may also be
        specified for component-scanning -->
    <context-param>
        <param-name>contextConfigLocation</param-name>
        <param-value>com.acme.AppConfig</param-value>
    </context-param>

    <!-- Bootstrap the root application context as usual using ContextLoaderListener -->
    <listener>
        <listener-class>org.springframework.web.context.ContextLoaderListener</listener-class>
    </listener>

    <!-- Declare a Spring MVC DispatcherServlet as usual -->
    <servlet>
        <servlet-name>dispatcher</servlet-name>
        <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
        <!-- Configure DispatcherServlet to use AnnotationConfigWebApplicationContext
            instead of the default XmlWebApplicationContext -->
        <init-param>
            <param-name>contextClass</param-name>
            <param-value>
                org.springframework.web.context.support.AnnotationConfigWebApplicationContext
            </param-value>
        </init-param>
        <!-- Again, config locations must consist of one or more comma- or space-delimited
            and fully-qualified @Configuration classes -->
        <init-param>
            <param-name>contextConfigLocation</param-name>
            <param-value>com.acme.web.MvcConfig</param-value>
        </init-param>
    </servlet>

    <!-- map all requests for /app/* to the dispatcher servlet -->
    <servlet-mapping>
        <servlet-name>dispatcher</servlet-name>
        <url-pattern>/app/*</url-pattern>
    </servlet-mapping>
</web-app>
```
### 7.12.3 使用@Bean注解
7.12.3 Using the @Bean annotation

@Bean是方法级别的注解，它与XML中的<bean/>类似，同样地，也支持<bean/>的一些属性，比如，init-method, destroy-method, autowring和name。

@Bean is a method-level annotation and a direct analog of the XML <bean/> element. The annotation supports some of the attributes offered by <bean/>, such as: init-method, destroy-method, autowiring and name.

可以在@Configuration或@Component注解的类中使用@Bean注解。

You can use the @Bean annotation in a @Configuration-annotated or in a @Component-annotated class.

__声明bean__
Declaring a bean

只要在方法上简单的加上@Bean注解就可以定义一个bean了，这样就在ApplicationContext中注册了一个类型为方法返回值的bean。默认地，bean的名字为方法的名称，如下所示：

To declare a bean, simply annotate a method with the @Bean annotation. You use this method to register a bean definition within an ApplicationContext of the type specified as the method’s return value. By default, the bean name will be the same as the method name. The following is a simple example of a @Bean method declaration:
```java
@Configuration
public class AppConfig {

    @Bean
    public TransferServiceImpl transferService() {
        return new TransferServiceImpl();
    }
}
```
上面的方式与下面的XML形式等价：
The preceding configuration is exactly equivalent to the following Spring XML:
```xml
<beans>
    <bean id="transferService" class="com.acme.TransferServiceImpl"/>
</beans>
```
两种方式都定义了一个名字为transferService的bean，且绑定了TransferServiceImpl的实例：

Both declarations make a bean named transferService available in the ApplicationContext, bound to an object instance of type TransferServiceImpl:
```java
transferService -> com.acme.TransferServiceImpl
```
You may also declare your @Bean method with an interface (or base class) return type:

@Configuration
public class AppConfig {

    @Bean
    public TransferService transferService() {
        return new TransferServiceImpl();
    }
}
However, this limits the visibility for advance type prediction to the specified interface type (TransferService) then, with the full type (TransferServiceImpl) only known to the container once the affected singleton bean has been instantiated. Non-lazy singleton beans get instantiated according to their declaration order, so you may see different type matching results depending on when another component tries to match by a non-declared type (such as @Autowired TransferServiceImpl which will only resolve once the "transferService" bean has been instantiated).

[Tip]
If you consistently refer to your types by a declared service interface, your @Bean return types may safely join that design decision. However, for components implementing several interfaces or for components potentially referred to by their implementation type, it is safer to declare the most specific return type possible (at least as specific as required by the injection points referring to your bean).

__Bean之间的依赖__
Bean dependencies

@Bean注解的方法可以有任意个参数用于描述这个bean的依赖关系。比如，如果TransferService需要一个AccountRepository，我们可以通过方法参数实现这种依赖注入。

A @Bean annotated method can have an arbitrary number of parameters describing the dependencies required to build that bean. For instance if our TransferService requires an AccountRepository we can materialize that dependency via a method parameter:
```java
@Configuration
public class AppConfig {

    @Bean
    public TransferService transferService(AccountRepository accountRepository) {
        return new TransferServiceImpl(accountRepository);
    }
}
```
这种机制与基于构造方法的依赖注入是完全相同的，更多详细内容可以查看相关章节。

The resolution mechanism is pretty much identical to constructor-based dependency injection, see the relevant section for more details.

__接收生命周期回调__
Receiving lifecycle callbacks

任何使用@Bean定义的类都有正常的生命周期回调，并且可以使用@PostConstruct和@PreDestroy注解，更多详细信息请参考JSR-250注解。

Any classes defined with the @Bean annotation support the regular lifecycle callbacks and can use the @PostConstruct and @PreDestroy annotations from JSR-250, see JSR-250 annotations for further details.

正常的生命周期回调被完美支持， 如果一个bean实现了InitializingBean, DisposableBean或者Lifecycle，它们的方法将被容器依次调用。

The regular Spring lifecycle callbacks are fully supported as well. If a bean implements InitializingBean, DisposableBean, or Lifecycle, their respective methods are called by the container.

同样地，也支持*Aware系列的接口，比如BeanFactoryAware, BeanNameAware, MessageSourceAware, ApplicationContextAware等等。

The standard set of *Aware interfaces such as BeanFactoryAware, BeanNameAware, MessageSourceAware, ApplicationContextAware, and so on are also fully supported.

@Bean注解也支持任意的初始化及销毁的回调方法，这与XML的init-method和destroy-method是非常类似的。

The @Bean annotation supports specifying arbitrary initialization and destruction callback methods, much like Spring XML’s init-method and destroy-method attributes on the bean element:
```java
public class Foo {

    public void init() {
        // initialization logic
    }
}

public class Bar {

    public void cleanup() {
        // destruction logic
    }
}

@Configuration
public class AppConfig {

    @Bean(initMethod = "init")
    public Foo foo() {
        return new Foo();
    }

    @Bean(destroyMethod = "cleanup")
    public Bar bar() {
        return new Bar();
    }
}
```
[Note]
> 默认地，使用Java配置的方式如果一个bean包含了公共的close或shutdown方法，它们将会被自动地包含在销毁的回调中。如果有公共的close或shutdown方法，但是我们并不想使用它们，那么只要加上@Bean(destroyMethod=”“)就可以屏蔽掉默认的推测了。 
我们可以使用这种特性在通过JNDI获得的资源上，并且这些资源是外部应用管理的。特别地，保证一定要在DataSource上使用它，因为DataSource在Java EE的应用服务器上是有问题的。 

> By default, beans defined using Java config that have a public close or shutdown method are automatically enlisted with a destruction callback. If you have a public close or shutdown method and you do not wish for it to be called when the container shuts down, simply add @Bean(destroyMethod="") to your bean definition to disable the default (inferred) mode.

> You may want to do that by default for a resource that you acquire via JNDI as its lifecycle is managed outside the application. In particular, make sure to always do it for a DataSource as it is known to be problematic on Java EE application servers.
```java
@Bean(destroyMethod="")
public DataSource dataSource() throws NamingException {
    return (DataSource) jndiTemplate.lookup("MyDS");
}
```
> 同样地，使用@Bean方法，可以很容易地选择编程式地JNDI查找：使用Spring的JndiTemplate/JndiLocatorDelegate帮助类或直接使用JNDI的InitialContext，但是不要使用JndiObjectFactoryBean变种，因为它会强制你去声明一个返回类型作为FactoryBean的类型代替实际的目标类型，这会使得交叉引用变得很困难。

> Also, with @Bean methods, you will typically choose to use programmatic JNDI lookups: either using Spring’s JndiTemplate/JndiLocatorDelegate helpers or straight JNDI InitialContext usage, but not the JndiObjectFactoryBean variant which would force you to declare the return type as the FactoryBean type instead of the actual target type, making it harder to use for cross-reference calls in other @Bean methods that intend to refer to the provided resource here.

当然，上面例子中的Foo，也可以直接在构造期间直接调用init()方法：

Of course, in the case of Foo above, it would be equally as valid to call the init() method directly during construction:
```java
@Configuration
public class AppConfig {

    @Bean
    public Foo foo() {
        Foo foo = new Foo();
        foo.init();
        return foo;
    }

    // ...
}
```
[Tip]
> 在Java中可以用你喜欢的方式直接操作对象，而不需要总是依赖容器的生命周期！
> When you work directly in Java, you can do anything you like with your objects and do not always need to rely on the container lifecycle!

__指定bean的作用域__
Specifying bean scope
__使用@Scope注解__
Using the @Scope annotation

可以使用任何标准的方式为@Bean注解的bean指定一个作用域，这些方式请参考Bean作用域章节。

You can specify that your beans defined with the @Bean annotation should have a specific scope. You can use any of the standard scopes specified in the Bean Scopes section.

默认地作用域为singleton，但是可以使用@Scope注解重写：

The default scope is singleton, but you can override this with the @Scope annotation:
```java
@Configuration
public class MyConfiguration {

    @Bean
    @Scope("prototype")
    public Encryptor encryptor() {
        // ...
    }
}
```
__@Scope and scoped-proxy__
Spring提供了一种简便地方式声明bean的作用域，它被称为scoped proxy。最简单地方式是创建那么一个代理，使用XML配置的形式则使用<aop:scoped-proxy/>元素。在Java中使用@Scope注解配置bean的方式提供了与XML代理模式属性同样的功能。默认是没有代理的(ScopedProxyMode.No)，但是可以指定ScopedProxyMode.TARGET_CLASS或ScopedProxyMode.INTERFACES。

Spring offers a convenient way of working with scoped dependencies through scoped proxies. The easiest way to create such a proxy when using the XML configuration is the <aop:scoped-proxy/> element. Configuring your beans in Java with a @Scope annotation offers equivalent support with the proxyMode attribute. The default is no proxy ( ScopedProxyMode.NO), but you can specify ScopedProxyMode.TARGET_CLASS or ScopedProxyMode.INTERFACES.

如果把xml形式改写为Java形式，看起来如下：

If you port the scoped proxy example from the XML reference documentation (see preceding link) to our @Bean using Java, it would look like the following:
```java
// an HTTP Session-scoped bean exposed as a proxy
@Bean
@SessionScope
public UserPreferences userPreferences() {
    return new UserPreferences();
}

@Bean
public Service userService() {
    UserService service = new SimpleUserService();
    // a reference to the proxied userPreferences bean
    service.setUserPreferences(userPreferences());
    return service;
}
```
__自定义bean的名称__
Customizing bean naming

默认地，使用@Bean默认的方法名为其bean的名字，然而这项功能可以使用name属性重写：

By default, configuration classes use a @Bean method’s name as the name of the resulting bean. This functionality can be overridden, however, with the name attribute.
```java
@Configuration
public class AppConfig {

    @Bean(name = "myFoo")
    public Foo foo() {
        return new Foo();
    }
}
```
__bean的别名__
Bean aliasing

正如7.3.1 命名bean中所讨论地一样，有时候可以想要给同一个bean多个名字，亦即别名，@Bean注解的name属性就可以达到这样的目的， 你可以为其提供一个String的数组。

As discussed in Section 7.3.1, “Naming beans”, it is sometimes desirable to give a single bean multiple names, otherwise known as bean aliasing. The name attribute of the @Bean annotation accepts a String array for this purpose.
```java
@Configuration
public class AppConfig {

    @Bean(name = { "dataSource", "subsystemA-dataSource", "subsystemB-dataSource" })
    public DataSource dataSource() {
        // instantiate, configure and return DataSource bean...
    }
}
```
__Bean描述__
Bean description

有时可能需要为一个bean提供更详细的描述。这对于监控bean很有用。

Sometimes it is helpful to provide a more detailed textual description of a bean. This can be particularly useful when beans are exposed (perhaps via JMX) for monitoring purposes.

可以使用@Description注解为其添加一段描述：

To add a description to a @Bean the @Description annotation can be used:
```java
@Configuration
public class AppConfig {

    @Bean
    @Description("Provides a basic example of a bean")
    public Foo foo() {
        return new Foo();
    }
}
```

### 使用@Configuration注解
7.12.4 Using the @Configuration annotation

@Configuration是类级别的注解，这表示一个对象是bean定义的来源。@Configuration注解的类里面使用@Bean注解的方法声明bean。对其中的@Bean方法的调用也能实现内部bean的依赖。参考7.12.1 基础概念：@Bean和@Configuration。

@Configuration is a class-level annotation indicating that an object is a source of bean definitions. @Configuration classes declare beans via public @Bean annotated methods. Calls to @Bean methods on @Configuration classes can also be used to define inter-bean dependencies. See Section 7.12.1, “Basic concepts: @Bean and @Configuration” for a general introduction.

__注入内部依赖__
Injecting inter-bean dependencies

当@Bean的方法对另外一个有依赖时，简单地调用另外一个@Bean注解的方法即可表达这种依赖：

When @Beans have dependencies on one another, expressing that dependency is as simple as having one bean method call another:
```java
@Configuration
public class AppConfig {

    @Bean
    public Foo foo() {
        return new Foo(bar());
    }

    @Bean
    public Bar bar() {
        return new Bar();
    }
}
```
在上例中，foo这个bean通过构造函数注入接收了bar的引用。

In the example above, the foo bean receives a reference to bar via constructor injection.

[Note]
> 这种方式仅仅适用于在@Configuration内部定义的@Bean方法。在普通的@Component类中不能声明内部依赖。
> This method of declaring inter-bean dependencies only works when the @Bean method is declared within a @Configuration class. You cannot declare inter-bean dependencies using plain @Component classes.

__查找方法注入__
Lookup method injection

正如之前提到的，查找方法注入是一项很少使用到的先进的技术。它对于一个单例bean依赖另一个原型bean很有用。在Java中使用了一种很自然的方法来实现了这种模式。

As noted earlier, lookup method injection is an advanced feature that you should use rarely. It is useful in cases where a singleton-scoped bean has a dependency on a prototype-scoped bean. Using Java for this type of configuration provides a natural means for implementing this pattern.
```java
public abstract class CommandManager {
    public Object process(Object commandState) {
        // grab a new instance of the appropriate Command interface
        Command command = createCommand();
        // set the state on the (hopefully brand new) Command instance
        command.setState(commandState);
        return command.execute();
    }

    // okay... but where is the implementation of this method?
    protected abstract Command createCommand();
}
```
使用Java配置，我们可以创建一个CommandManager的子类，实现其createCommand()方法，这样就可以让它查找到新的原型command对象。

Using Java-configuration support , you can create a subclass of CommandManager where the abstract createCommand() method is overridden in such a way that it looks up a new (prototype) command object:
```java
@Bean
@Scope("prototype")
public AsyncCommand asyncCommand() {
    AsyncCommand command = new AsyncCommand();
    // inject dependencies here as required
    return command;
}

@Bean
public CommandManager commandManager() {
    // return new anonymous implementation of CommandManager with command() overridden
    // to return a new prototype Command object
    return new CommandManager() {
        protected Command createCommand() {
            return asyncCommand();
        }
    }
}
```
__更多关于Java配置内部工作的信息__
Further information about how Java-based configuration works internally

下面的例子显示了@Bean注解的方法被调用了两次：

The following example shows a @Bean annotated method being called twice:
```java
@Configuration
public class AppConfig {

    @Bean
    public ClientService clientService1() {
        ClientServiceImpl clientService = new ClientServiceImpl();
        clientService.setClientDao(clientDao());
        return clientService;
    }

    @Bean
    public ClientService clientService2() {
        ClientServiceImpl clientService = new ClientServiceImpl();
        clientService.setClientDao(clientDao());
        return clientService;
    }

    @Bean
    public ClientDao clientDao() {
        return new ClientDaoImpl();
    }
}
```
clientDao()方法在clientService1()和clientService2()中分别被调用了一次。由于这个方法创建了一个ClientDaoImpl的实例并返回了它，但是你可能希望有两个实例（每个service一个）。这种定义可能是有问题的：在Spring中，实例化的bean默认是单例的。这就是神奇的地方：所有的@Configuration类都会在启动的时候被CGLIB子类化。在子类中，所有的子类方法都会在调用父类的方法之前检查有没有缓存的bean，如果没有再创建一个新的实例。注意，从Spring3.2开始，classpath不再需要包含CGLIB了因为CGLIB相关的类已经被打包在org.springframework.cglib中了，并且可以直接使用。

clientDao() has been called once in clientService1() and once in clientService2(). Since this method creates a new instance of ClientDaoImpl and returns it, you would normally expect having 2 instances (one for each service). That definitely would be problematic: in Spring, instantiated beans have a singleton scope by default. This is where the magic comes in: All @Configuration classes are subclassed at startup-time with CGLIB. In the subclass, the child method checks the container first for any cached (scoped) beans before it calls the parent method and creates a new instance. Note that as of Spring 3.2, it is no longer necessary to add CGLIB to your classpath because CGLIB classes have been repackaged under org.springframework.cglib and included directly within the spring-core JAR.

[Note]
> 这种行为可以会根据bean的作用域而变化，我们这里只讨论单例。
> The behavior could be different according to the scope of your bean. We are talking about singletons here.

[Tip]
> 实际上还会有一些限制，因为CGLIB是在启动的时候动态地添加这些特性，所以配置的类不能是final的。不过，从4.3开始，任何构造方法都允许放置在配置类中，包含@Autowired或一个非默认的构造方法用于默认注入即可。如果想避免任何CGLIB带来的限制，考虑在非@Configuration类中使用@Bean方法，比如普通的@Component类。这样在@Bean方法之中跨方法调用就不会被拦截了，所以这样只能依赖于构造方法或方法组织的注入了。
> There are a few restrictions due to the fact that CGLIB dynamically adds features at startup-time, in particular that configuration classes must not be final. However, as of 4.3, any constructors are allowed on configuration classes, including the use of @Autowired or a single non-default constructor declaration for default injection.

If you prefer to avoid any CGLIB-imposed limitations, consider declaring your @Bean methods on non-@Configuration classes, e.g. on plain @Component classes instead. Cross-method calls between @Bean methods won’t get intercepted then, so you’ll have to exclusively rely on dependency injection at the constructor or method level there.

### 7.12.5 组合的Java配置
7.12.5 Composing Java-based configurations

__使用@Import注解__
Using the @Import annotation

与XML中使用<import/>一样用于模块化配置，@Import注解允许从另一个配置类中加载@Bean定义。

Much as the <import/> element is used within Spring XML files to aid in modularizing configurations, the @Import annotation allows for loading @Bean definitions from another configuration class:
```java
@Configuration
public class ConfigA {

    @Bean
    public A a() {
        return new A();
    }
}

@Configuration
@Import(ConfigA.class)
public class ConfigB {

    @Bean
    public B b() {
        return new B();
    }
}
```
现在，我们不需要同时指定ConfigA.class和ConfigB.class了，只要明确地指定ConfigB即可：

Now, rather than needing to specify both ConfigA.class and ConfigB.class when instantiating the context, only ConfigB needs to be supplied explicitly:
```java
public static void main(String[] args) {
    ApplicationContext ctx = new AnnotationConfigApplicationContext(ConfigB.class);

    // now both beans A and B will be available...
    A a = ctx.getBean(A.class);
    B b = ctx.getBean(B.class);
}
```
这种方式简化了容器的实例化，只要处理一个类就行了，而不需要开发者记住大量的@Configuration类。

This approach simplifies container instantiation, as only one class needs to be dealt with, rather than requiring the developer to remember a potentially large number of @Configuration classes during construction.

[Tip]
> 从Spring 4.2开始，@Import也可以支持对普通组件类的引用了，与AnnotationConfigApplicationContext.register()方法类似。这在避免组件扫描的时候很有用，使用少量的配置类作为切入点用于明确定义所有的组件类。

> As of Spring Framework 4.2, @Import also supports references to regular component classes, analogous to the AnnotationConfigApplicationContext.register method. This is particularly useful if you’d like to avoid component scanning, using a few configuration classes as entry points for explicitly defining all your components.

__在导入的@Bean定义上注入依赖__
Injecting dependencies on imported @Bean definitions

上面的例子可以很好地工作，但是太简单了。在大部分场景下，bean都会依赖另一个配置类中的bean。使用XML时，这没有什么问题，因为不需要编译，只要简单地声明ref=”someBean”即可，并信任Spring可以很好地处理它。当然，使用配置类时，Java编译器会有一些限制，那就是必须符合Java的语法。

The example above works, but is simplistic. In most practical scenarios, beans will have dependencies on one another across configuration classes. When using XML, this is not an issue, per se, because there is no compiler involved, and one can simply declare ref="someBean" and trust that Spring will work it out during container initialization. Of course, when using @Configuration classes, the Java compiler places constraints on the configuration model, in that references to other beans must be valid Java syntax.

幸运地，解决这个问题也很简单。正如我们之前讨论的，@Bean方法可以有任意的参数用于描述其依赖。让我们考虑一下实际的场景，有几个配置类，并且每个都依赖于其它的类：

Fortunately, solving this problem is simple. As we already discussed, @Bean method can have an arbitrary number of parameters describing the bean dependencies. Let’s consider a more real-world scenario with several @Configuration classes, each depending on beans declared in the others:
```java
@Configuration
public class ServiceConfig {

    @Bean
    public TransferService transferService(AccountRepository accountRepository) {
        return new TransferServiceImpl(accountRepository);
    }
}

@Configuration
public class RepositoryConfig {

    @Bean
    public AccountRepository accountRepository(DataSource dataSource) {
        return new JdbcAccountRepository(dataSource);
    }
}

@Configuration
@Import({ServiceConfig.class, RepositoryConfig.class})
public class SystemTestConfig {

    @Bean
    public DataSource dataSource() {
        // return new DataSource
    }
}

public static void main(String[] args) {
    ApplicationContext ctx = new AnnotationConfigApplicationContext(SystemTestConfig.class);
    // everything wires up across configuration classes...
    TransferService transferService = ctx.getBean(TransferService.class);
    transferService.transfer(100.00, "A123", "C456");
}
```
也可以有其它的方法来实现相同的结果。请记住@Configuration类也是容器中的一个bean：这意味着它们可以像其它bean一样使用@Autowired和@Value注解。

There is another way to achieve the same result. Remember that @Configuration classes are ultimately just another bean in the container: This means that they can take advantage of @Autowired and @Value injection etc just like any other bean!

[Warning]
> 请确定你都是以最简单的方式注入的依赖。@Configuration类会在上下文初始化的早期被处理，所以它的依赖会在更早期被初始化。如果可能的话，请像上面这样使用参数化注入。
> Make sure that the dependencies you inject that way are of the simplest kind only. @Configuration classes are processed quite early during the initialization of the context and forcing a dependency to be injected this way may lead to unexpected early initialization. Whenever possible, resort to parameter-based injection as in the example above.

同样地，对于通过@Bean声明的BeanPostProcessor和BeanFactoryPostProcessor请谨慎对待。它们通常被声明为静态的@Bean方法，不会触发包含它们的配置类。另外，@Autowired和@Value在配置类本身上是不起作用的，因为它们太早被实例化了。

Also, be particularly careful with BeanPostProcessor and BeanFactoryPostProcessor definitions via @Bean. Those should usually be declared as static @Bean methods, not triggering the instantiation of their containing configuration class. Otherwise, @Autowired and @Value won’t work on the configuration class itself since it is being created as a bean instance too early.
```java
@Configuration
public class ServiceConfig {

    @Autowired
    private AccountRepository accountRepository;

    @Bean
    public TransferService transferService() {
        return new TransferServiceImpl(accountRepository);
    }
}

@Configuration
public class RepositoryConfig {

    private final DataSource dataSource;

    @Autowired
    public RepositoryConfig(DataSource dataSource) {
        this.dataSource = dataSource;
    }

    @Bean
    public AccountRepository accountRepository() {
        return new JdbcAccountRepository(dataSource);
    }
}

@Configuration
@Import({ServiceConfig.class, RepositoryConfig.class})
public class SystemTestConfig {

    @Bean
    public DataSource dataSource() {
        // return new DataSource
    }
}

public static void main(String[] args) {
    ApplicationContext ctx = new AnnotationConfigApplicationContext(SystemTestConfig.class);
    // everything wires up across configuration classes...
    TransferService transferService = ctx.getBean(TransferService.class);
    transferService.transfer(100.00, "A123", "C456");
}
```
[Tip]
> @Configuration类中的构造方法注入只在Spring 4.3以后才支持。另外请注意，如果目标bean只有一个构造方法也可以不指定@Autowried，在上例中，RepositoryConfig构造方法上的@Autowired是非必要的。
> Constructor injection in @Configuration classes is only supported as of Spring Framework 4.3. Note also that there is no need to specify @Autowired if the target bean defines only one constructor; in the example above, @Autowired is not necessary on the RepositoryConfig constructor.

在上面的场景中，@Autowired可以很好地工作，并提供希望的结果，但是被装配的bean的定义声明是模糊不清的。例如，当一个开发者查看ServiceConfig类时，你怎么知道@Autowired AccountRepository在哪定义的呢？它在代码中并不清楚，并且这可以很微小。记住Spring工具套件提供了一些工具，可以画出所有东西是怎么装配起来的——这可以是你需要的。同样地，你的IDE也可以很容易地找出所有的定义和AccountRepository类型引用的地方，并可以快速地找出@Bean方法定义的地方。

In the scenario above, using @Autowired works well and provides the desired modularity, but determining exactly where the autowired bean definitions are declared is still somewhat ambiguous. For example, as a developer looking at ServiceConfig, how do you know exactly where the @Autowired AccountRepository bean is declared? It’s not explicit in the code, and this may be just fine. Remember that the Spring Tool Suite provides tooling that can render graphs showing how everything is wired up - that may be all you need. Also, your Java IDE can easily find all declarations and uses of the AccountRepository type, and will quickly show you the location of @Bean methods that return that type.

在某些情况下，这种含糊是不被接受的，并且你希望可以在IDE中直接从一个@Configuration类到另一个，可以考虑装配配置类本身：

In cases where this ambiguity is not acceptable and you wish to have direct navigation from within your IDE from one @Configuration class to another, consider autowiring the configuration classes themselves:
```java
@Configuration
public class ServiceConfig {

    @Autowired
    private RepositoryConfig repositoryConfig;

    @Bean
    public TransferService transferService() {
        // navigate 'through' the config class to the @Bean method!
        return new TransferServiceImpl(repositoryConfig.accountRepository());
    }
}
```
在上面的场景下，AccountRepository的定义就很明确了。然而，ServiceConfig与RepositoryConfig耦合了；这是一种折衷的方法。这种耦合某种程度上可以通过接口或抽象解决，如下：

In the situation above, it is completely explicit where AccountRepository is defined. However, ServiceConfig is now tightly coupled to RepositoryConfig; that’s the tradeoff. This tight coupling can be somewhat mitigated by using interface-based or abstract class-based @Configuration classes. Consider the following:
```java
@Configuration
public class ServiceConfig {

    @Autowired
    private RepositoryConfig repositoryConfig;

    @Bean
    public TransferService transferService() {
        return new TransferServiceImpl(repositoryConfig.accountRepository());
    }
}

@Configuration
public interface RepositoryConfig {

    @Bean
    AccountRepository accountRepository();
}

@Configuration
public class DefaultRepositoryConfig implements RepositoryConfig {

    @Bean
    public AccountRepository accountRepository() {
        return new JdbcAccountRepository(...);
    }
}

@Configuration
@Import({ServiceConfig.class, DefaultRepositoryConfig.class})  // import the concrete config!
public class SystemTestConfig {

    @Bean
    public DataSource dataSource() {
        // return DataSource
    }

}

public static void main(String[] args) {
    ApplicationContext ctx = new AnnotationConfigApplicationContext(SystemTestConfig.class);
    TransferService transferService = ctx.getBean(TransferService.class);
    transferService.transfer(100.00, "A123", "C456");
}
```
现在ServiceConfig就与具体的DefaultRepositoryConfig松耦合了，并且内置的IDE工具也可以生效：对于 开发者可以很容易地获得RepositoryConfig实现类的继承体系。使用这种方式，操纵@Configuration类和它们的依赖与基于接口的代码没有区别。

Now ServiceConfig is loosely coupled with respect to the concrete DefaultRepositoryConfig, and built-in IDE tooling is still useful: it will be easy for the developer to get a type hierarchy of RepositoryConfig implementations. In this way, navigating @Configuration classes and their dependencies becomes no different than the usual process of navigating interface-based code.

[Tip]
If you would like to influence the startup creation order of certain beans, consider declaring some of them as @Lazy (for creation on first access instead of on startup) or as @DependsOn on certain other beans (making sure that specific other beans will be created before the current bean, beyond what the latter’s direct dependencies imply).

__有条件地包含@Configuration类或@Bean方法__
Conditionally include @Configuration classes or @Bean methods

有时候有条件地包含或不包含一个@Configuration类或@Bean方法很有用，这基于特定的系统状态。一种通用的方法是使用@Profile注解去激活bean，仅当指定的配置文件包含在了Spring的环境中才有效（参考7.13.1 bean定义配置文件）。

It is often useful to conditionally enable or disable a complete @Configuration class, or even individual @Bean methods, based on some arbitrary system state. One common example of this is to use the @Profile annotation to activate beans only when a specific profile has been enabled in the Spring Environment (see Section 7.13.1, “Bean definition profiles” for details).

@Profile注解实际是实现了一个更灵活的注解@Conditional。@Condition注解表明一个@Bean被注册之前会先询问@Condition。

The @Profile annotation is actually implemented using a much more flexible annotation called @Conditional. The @Conditional annotation indicates specific org.springframework.context.annotation.Condition implementations that should be consulted before a @Bean is registered.

Condition接口的实现只要简单地提供matches(…)方法，并返回true或false即可。例如，下面是一个实际的Condition实现用于@Profile：

Implementations of the Condition interface simply provide a matches(…​) method that returns true or false. For example, here is the actual Condition implementation used for @Profile:
```java
@Override
public boolean matches(ConditionContext context, AnnotatedTypeMetadata metadata) {
    if (context.getEnvironment() != null) {
        // Read the @Profile annotation attributes
        MultiValueMap<String, Object> attrs = metadata.getAllAnnotationAttributes(Profile.class.getName());
        if (attrs != null) {
            for (Object value : attrs.get("value")) {
                if (context.getEnvironment().acceptsProfiles(((String[]) value))) {
                    return true;
                }
            }
            return false;
        }
    }
    return true;
}
```
更多信息请参考@Conditional的javadoc。

See the @Conditional javadocs for more detail.

__绑定Java与XML配置__
Combining Java and XML configuration

Spring的@Configuration类并不能100%地替代XML配置。一些情况下使用XML的命名空间仍然是最理想的方式来配置容器。在某些场景下，XML是很方便或必要的，你可以选择以XML为主，比如ClassPathXmlApplicationContext，或者以Java为主使用AnnotationConfigApplicationContext并在需要的时候使用@ImportResource注解导入XML配置。

Spring’s @Configuration class support does not aim to be a 100% complete replacement for Spring XML. Some facilities such as Spring XML namespaces remain an ideal way to configure the container. In cases where XML is convenient or necessary, you have a choice: either instantiate the container in an "XML-centric" way using, for example, ClassPathXmlApplicationContext, or in a "Java-centric" fashion using AnnotationConfigApplicationContext and the @ImportResource annotation to import XML as needed.

__XML为主使用@Configuration类__
XML-centric use of @Configuration classes

更受人喜爱的方法是从XML启动容器并包含@Configuration类。例如，在大型的已存在的系统中，以前是使用XML配置的，所以很容易地创建@Configuration类，并包含他们到XML文件中，下面我们会讲解以XML为主的案例。

It may be preferable to bootstrap the Spring container from XML and include @Configuration classes in an ad-hoc fashion. For example, in a large existing codebase that uses Spring XML, it will be easier to create @Configuration classes on an as-needed basis and include them from the existing XML files. Below you’ll find the options for using @Configuration classes in this kind of "XML-centric" situation.

记住@Configuration类仅仅用于bean的定义。在这个例子中，我们创建了一个叫做AppConfig的配置类，并把它包含到system-test-config.xml中。因为<context:annotation-config/>打开了，所以容器能够识别到@Configuration注解并处理其中的@Bean方法。

Remember that @Configuration classes are ultimately just bean definitions in the container. In this example, we create a @Configuration class named AppConfig and include it within system-test-config.xml as a <bean/> definition. Because <context:annotation-config/> is switched on, the container will recognize the @Configuration annotation and process the @Bean methods declared in AppConfig properly.
```java
@Configuration
public class AppConfig {

    @Autowired
    private DataSource dataSource;

    @Bean
    public AccountRepository accountRepository() {
        return new JdbcAccountRepository(dataSource);
    }

    @Bean
    public TransferService transferService() {
        return new TransferService(accountRepository());
    }
}
```
system-test-config.xml:
```xml
<beans>
    <!-- enable processing of annotations such as @Autowired and @Configuration -->
    <context:annotation-config/>
    <context:property-placeholder location="classpath:/com/acme/jdbc.properties"/>

    <bean class="com.acme.AppConfig"/>

    <bean class="org.springframework.jdbc.datasource.DriverManagerDataSource">
        <property name="url" value="${jdbc.url}"/>
        <property name="username" value="${jdbc.username}"/>
        <property name="password" value="${jdbc.password}"/>
    </bean>
</beans>
```
```java
jdbc.properties:

jdbc.url=jdbc:hsqldb:hsql://localhost/xdb
jdbc.username=sa
jdbc.password=
public static void main(String[] args) {
    ApplicationContext ctx = new ClassPathXmlApplicationContext("classpath:/com/acme/system-test-config.xml");
    TransferService transferService = ctx.getBean(TransferService.class);
    // ...
}
```
[Note]
> 在上面的system-test-config.xml中，AppConfig的<bean/>并没有声明一个id。当然如果声明了也是可以授受的，但是对于没有被其它bean引用的bean并有必要声明id，并且它也没可能从容器获取一个明确的名字。同样地，DataSource也不需要一个明确的id，因为它是通过类型装配的。
> In system-test-config.xml above, the AppConfig <bean/> does not declare an id element. While it would be acceptable to do so, it is unnecessary given that no other bean will ever refer to it, and it is unlikely that it will be explicitly fetched from the container by name. Likewise with the DataSource bean - it is only ever autowired by type, so an explicit bean id is not strictly required.

因为@Configuration是被元注解@Component注解的，所以@Configuration注解的类也可以被自动扫描。同样使用上面的例子，我们可以重新定义system-test-config.xml来使用组件扫描。注意，这种情况下，我们就没必要明确地声明<context:annotation-config/>了，因为<context:component-scan/&tl;已经包含了同样的功能。

Because @Configuration is meta-annotated with @Component, @Configuration-annotated classes are automatically candidates for component scanning. Using the same scenario as above, we can redefine system-test-config.xml to take advantage of component-scanning. Note that in this case, we don’t need to explicitly declare <context:annotation-config/>, because <context:component-scan/> enables the same functionality.

system-test-config.xml:
```xml
<beans>
    <!-- picks up and registers AppConfig as a bean definition -->
    <context:component-scan base-package="com.acme"/>
    <context:property-placeholder location="classpath:/com/acme/jdbc.properties"/>

    <bean class="org.springframework.jdbc.datasource.DriverManagerDataSource">
        <property name="url" value="${jdbc.url}"/>
        <property name="username" value="${jdbc.username}"/>
        <property name="password" value="${jdbc.password}"/>
    </bean>
</beans>
```
__以@Configuration类为主使用@ImportResource引入XML__
@Configuration class-centric use of XML with @ImportResource

在一些应用中，@Configuration类是主要的配置方式，也需要使用一些XML配置。在这些场景下，简单地使用@ImportResource并按需要定义XML文件即可。这种方式可以以Java为主，并保持少量的XML配置。

In applications where @Configuration classes are the primary mechanism for configuring the container, it will still likely be necessary to use at least some XML. In these scenarios, simply use @ImportResource and define only as much XML as is needed. Doing so achieves a "Java-centric" approach to configuring the container and keeps XML to a bare minimum.
```java
@Configuration
@ImportResource("classpath:/com/acme/properties-config.xml")
public class AppConfig {

    @Value("${jdbc.url}")
    private String url;

    @Value("${jdbc.username}")
    private String username;

    @Value("${jdbc.password}")
    private String password;

    @Bean
    public DataSource dataSource() {
        return new DriverManagerDataSource(url, username, password);
    }
}
```
properties-config.xml
```xml
<beans>
    <context:property-placeholder location="classpath:/com/acme/jdbc.properties"/>
</beans>
```
```properties
jdbc.properties
jdbc.url=jdbc:hsqldb:hsql://localhost/xdb
jdbc.username=sa
jdbc.password=
```
```java
public static void main(String[] args) {
    ApplicationContext ctx = new AnnotationConfigApplicationContext(AppConfig.class);
    TransferService transferService = ctx.getBean(TransferService.class);
    // ...
}
```

7.13 Environment abstraction
The Environment is an abstraction integrated in the container that models two key aspects of the application environment: profiles and properties.

A profile is a named, logical group of bean definitions to be registered with the container only if the given profile is active. Beans may be assigned to a profile whether defined in XML or via annotations. The role of the Environment object with relation to profiles is in determining which profiles (if any) are currently active, and which profiles (if any) should be active by default.

Properties play an important role in almost all applications, and may originate from a variety of sources: properties files, JVM system properties, system environment variables, JNDI, servlet context parameters, ad-hoc Properties objects, Maps, and so on. The role of the Environment object with relation to properties is to provide the user with a convenient service interface for configuring property sources and resolving properties from them.

7.13.1 Bean definition profiles
Bean definition profiles is a mechanism in the core container that allows for registration of different beans in different environments. The word environment can mean different things to different users and this feature can help with many use cases, including:

working against an in-memory datasource in development vs looking up that same datasource from JNDI when in QA or production
registering monitoring infrastructure only when deploying an application into a performance environment
registering customized implementations of beans for customer A vs. customer B deployments
Let’s consider the first use case in a practical application that requires a DataSource. In a test environment, the configuration may look like this:

@Bean
public DataSource dataSource() {
    return new EmbeddedDatabaseBuilder()
        .setType(EmbeddedDatabaseType.HSQL)
        .addScript("my-schema.sql")
        .addScript("my-test-data.sql")
        .build();
}
Let’s now consider how this application will be deployed into a QA or production environment, assuming that the datasource for the application will be registered with the production application server’s JNDI directory. Our dataSource bean now looks like this:

@Bean(destroyMethod="")
public DataSource dataSource() throws Exception {
    Context ctx = new InitialContext();
    return (DataSource) ctx.lookup("java:comp/env/jdbc/datasource");
}
The problem is how to switch between using these two variations based on the current environment. Over time, Spring users have devised a number of ways to get this done, usually relying on a combination of system environment variables and XML <import/> statements containing ${placeholder} tokens that resolve to the correct configuration file path depending on the value of an environment variable. Bean definition profiles is a core container feature that provides a solution to this problem.

If we generalize the example use case above of environment-specific bean definitions, we end up with the need to register certain bean definitions in certain contexts, while not in others. You could say that you want to register a certain profile of bean definitions in situation A, and a different profile in situation B. Let’s first see how we can update our configuration to reflect this need.

@Profile
The @Profile annotation allows you to indicate that a component is eligible for registration when one or more specified profiles are active. Using our example above, we can rewrite the dataSource configuration as follows:

@Configuration
@Profile("development")
public class StandaloneDataConfig {

    @Bean
    public DataSource dataSource() {
        return new EmbeddedDatabaseBuilder()
            .setType(EmbeddedDatabaseType.HSQL)
            .addScript("classpath:com/bank/config/sql/schema.sql")
            .addScript("classpath:com/bank/config/sql/test-data.sql")
            .build();
    }
}
@Configuration
@Profile("production")
public class JndiDataConfig {

    @Bean(destroyMethod="")
    public DataSource dataSource() throws Exception {
        Context ctx = new InitialContext();
        return (DataSource) ctx.lookup("java:comp/env/jdbc/datasource");
    }
}
[Note]
As mentioned before, with @Bean methods, you will typically choose to use programmatic JNDI lookups: either using Spring’s JndiTemplate/JndiLocatorDelegate helpers or the straight JNDI InitialContext usage shown above, but not the JndiObjectFactoryBean variant which would force you to declare the return type as the FactoryBean type.

@Profile can be used as a meta-annotation for the purpose of creating a custom composed annotation. The following example defines a custom @Production annotation that can be used as a drop-in replacement for @Profile("production"):

@Target(ElementType.TYPE)
@Retention(RetentionPolicy.RUNTIME)
@Profile("production")
public @interface Production {
}
[Tip]
If a @Configuration class is marked with @Profile, all of the @Bean methods and @Import annotations associated with that class will be bypassed unless one or more of the specified profiles are active. If a @Component or @Configuration class is marked with @Profile({"p1", "p2"}), that class will not be registered/processed unless profiles 'p1' and/or 'p2' have been activated. If a given profile is prefixed with the NOT operator (!), the annotated element will be registered if the profile is not active. For example, given @Profile({"p1", "!p2"}), registration will occur if profile 'p1' is active or if profile 'p2' is not active.

@Profile can also be declared at the method level to include only one particular bean of a configuration class, e.g. for alternative variants of a particular bean:

@Configuration
public class AppConfig {

    @Bean("dataSource")
    @Profile("development")
    public DataSource standaloneDataSource() {
        return new EmbeddedDatabaseBuilder()
            .setType(EmbeddedDatabaseType.HSQL)
            .addScript("classpath:com/bank/config/sql/schema.sql")
            .addScript("classpath:com/bank/config/sql/test-data.sql")
            .build();
    }

    @Bean("dataSource")
    @Profile("production")
    public DataSource jndiDataSource() throws Exception {
        Context ctx = new InitialContext();
        return (DataSource) ctx.lookup("java:comp/env/jdbc/datasource");
    }
}
[Note]
With @Profile on @Bean methods, a special scenario may apply: In the case of overloaded @Bean methods of the same Java method name (analogous to constructor overloading), an @Profile condition needs to be consistently declared on all overloaded methods. If the conditions are inconsistent, only the condition on the first declaration among the overloaded methods will matter. @Profile can therefore not be used to select an overloaded method with a particular argument signature over another; resolution between all factory methods for the same bean follows Spring’s constructor resolution algorithm at creation time.

If you would like to define alternative beans with different profile conditions, use distinct Java method names pointing to the same bean name via the @Bean name attribute, as indicated in the example above. If the argument signatures are all the same (e.g. all of the variants have no-arg factory methods), this is the only way to represent such an arrangement in a valid Java class in the first place (since there can only be one method of a particular name and argument signature).

XML bean definition profiles
The XML counterpart is the profile attribute of the <beans> element. Our sample configuration above can be rewritten in two XML files as follows:

<beans profile="development"
    xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns:jdbc="http://www.springframework.org/schema/jdbc"
    xsi:schemaLocation="...">

    <jdbc:embedded-database id="dataSource">
        <jdbc:script location="classpath:com/bank/config/sql/schema.sql"/>
        <jdbc:script location="classpath:com/bank/config/sql/test-data.sql"/>
    </jdbc:embedded-database>
</beans>
<beans profile="production"
    xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns:jee="http://www.springframework.org/schema/jee"
    xsi:schemaLocation="...">

    <jee:jndi-lookup id="dataSource" jndi-name="java:comp/env/jdbc/datasource"/>
</beans>
It is also possible to avoid that split and nest <beans/> elements within the same file:

<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns:jdbc="http://www.springframework.org/schema/jdbc"
    xmlns:jee="http://www.springframework.org/schema/jee"
    xsi:schemaLocation="...">

    <!-- other bean definitions -->

    <beans profile="development">
        <jdbc:embedded-database id="dataSource">
            <jdbc:script location="classpath:com/bank/config/sql/schema.sql"/>
            <jdbc:script location="classpath:com/bank/config/sql/test-data.sql"/>
        </jdbc:embedded-database>
    </beans>

    <beans profile="production">
        <jee:jndi-lookup id="dataSource" jndi-name="java:comp/env/jdbc/datasource"/>
    </beans>
</beans>
The spring-bean.xsd has been constrained to allow such elements only as the last ones in the file. This should help provide flexibility without incurring clutter in the XML files.

Activating a profile
Now that we have updated our configuration, we still need to instruct Spring which profile is active. If we started our sample application right now, we would see a NoSuchBeanDefinitionException thrown, because the container could not find the Spring bean named dataSource.

Activating a profile can be done in several ways, but the most straightforward is to do it programmatically against the Environment API which is available via an ApplicationContext:

AnnotationConfigApplicationContext ctx = new AnnotationConfigApplicationContext();
ctx.getEnvironment().setActiveProfiles("development");
ctx.register(SomeConfig.class, StandaloneDataConfig.class, JndiDataConfig.class);
ctx.refresh();
In addition, profiles may also be activated declaratively through the spring.profiles.active property which may be specified through system environment variables, JVM system properties, servlet context parameters in web.xml, or even as an entry in JNDI (see Section 7.13.2, “PropertySource abstraction”). In integration tests, active profiles can be declared via the @ActiveProfiles annotation in the spring-test module (see the section called “Context configuration with environment profiles”).

Note that profiles are not an "either-or" proposition; it is possible to activate multiple profiles at once. Programmatically, simply provide multiple profile names to the setActiveProfiles() method, which accepts String…​ varargs:

ctx.getEnvironment().setActiveProfiles("profile1", "profile2");
Declaratively, spring.profiles.active may accept a comma-separated list of profile names:

-Dspring.profiles.active="profile1,profile2"
Default profile
The default profile represents the profile that is enabled by default. Consider the following:

@Configuration
@Profile("default")
public class DefaultDataConfig {

    @Bean
    public DataSource dataSource() {
        return new EmbeddedDatabaseBuilder()
            .setType(EmbeddedDatabaseType.HSQL)
            .addScript("classpath:com/bank/config/sql/schema.sql")
            .build();
    }
}
If no profile is active, the dataSource above will be created; this can be seen as a way to provide a default definition for one or more beans. If any profile is enabled, the default profile will not apply.

The name of the default profile can be changed using setDefaultProfiles() on the Environment or declaratively using the spring.profiles.default property.

7.13.2 PropertySource abstraction
Spring’s Environment abstraction provides search operations over a configurable hierarchy of property sources. To explain fully, consider the following:

ApplicationContext ctx = new GenericApplicationContext();
Environment env = ctx.getEnvironment();
boolean containsFoo = env.containsProperty("foo");
System.out.println("Does my environment contain the 'foo' property? " + containsFoo);
In the snippet above, we see a high-level way of asking Spring whether the foo property is defined for the current environment. To answer this question, the Environment object performs a search over a set of PropertySource objects. A PropertySource is a simple abstraction over any source of key-value pairs, and Spring’s StandardEnvironment is configured with two PropertySource objects — one representing the set of JVM system properties (a la System.getProperties()) and one representing the set of system environment variables (a la System.getenv()).

[Note]
These default property sources are present for StandardEnvironment, for use in standalone applications. StandardServletEnvironment is populated with additional default property sources including servlet config and servlet context parameters. StandardPortletEnvironment similarly has access to portlet config and portlet context parameters as property sources. Both can optionally enable a JndiPropertySource. See the javadocs for details.

Concretely, when using the StandardEnvironment, the call to env.containsProperty("foo") will return true if a foo system property or foo environment variable is present at runtime.

[Tip]
The search performed is hierarchical. By default, system properties have precedence over environment variables, so if the foo property happens to be set in both places during a call to env.getProperty("foo"), the system property value will 'win' and be returned preferentially over the environment variable. Note that property values will not get merged but rather completely overridden by a preceding entry.

For a common StandardServletEnvironment, the full hierarchy looks as follows, with the highest-precedence entries at the top:

ServletConfig parameters (if applicable, e.g. in case of a DispatcherServlet context)
ServletContext parameters (web.xml context-param entries)
JNDI environment variables ("java:comp/env/" entries)
JVM system properties ("-D" command-line arguments)
JVM system environment (operating system environment variables)
Most importantly, the entire mechanism is configurable. Perhaps you have a custom source of properties that you’d like to integrate into this search. No problem — simply implement and instantiate your own PropertySource and add it to the set of PropertySources for the current Environment:

ConfigurableApplicationContext ctx = new GenericApplicationContext();
MutablePropertySources sources = ctx.getEnvironment().getPropertySources();
sources.addFirst(new MyPropertySource());
In the code above, MyPropertySource has been added with highest precedence in the search. If it contains a foo property, it will be detected and returned ahead of any foo property in any other PropertySource. The MutablePropertySources API exposes a number of methods that allow for precise manipulation of the set of property sources.

7.13.3 @PropertySource
The @PropertySource annotation provides a convenient and declarative mechanism for adding a PropertySource to Spring’s Environment.

Given a file "app.properties" containing the key/value pair testbean.name=myTestBean, the following @Configuration class uses @PropertySource in such a way that a call to testBean.getName() will return "myTestBean".

@Configuration
@PropertySource("classpath:/com/myco/app.properties")
public class AppConfig {

    @Autowired
    Environment env;

    @Bean
    public TestBean testBean() {
        TestBean testBean = new TestBean();
        testBean.setName(env.getProperty("testbean.name"));
        return testBean;
    }
}
Any ${…​} placeholders present in a @PropertySource resource location will be resolved against the set of property sources already registered against the environment. For example:

@Configuration
@PropertySource("classpath:/com/${my.placeholder:default/path}/app.properties")
public class AppConfig {

    @Autowired
    Environment env;

    @Bean
    public TestBean testBean() {
        TestBean testBean = new TestBean();
        testBean.setName(env.getProperty("testbean.name"));
        return testBean;
    }
}
Assuming that "my.placeholder" is present in one of the property sources already registered, e.g. system properties or environment variables, the placeholder will be resolved to the corresponding value. If not, then "default/path" will be used as a default. If no default is specified and a property cannot be resolved, an IllegalArgumentException will be thrown.

[Note]
The @PropertySource annotation is repeatable according to Java 8 conventions. However, all such @PropertySource annotations need to be declared at the same level: either directly on the configuration class or as meta-annotations within the same custom annotation. Mixing of direct annotations and meta-annotations is not recommended since direct annotations will effectively override meta-annotations.

7.13.4 Placeholder resolution in statements
Historically, the value of placeholders in elements could be resolved only against JVM system properties or environment variables. No longer is this the case. Because the Environment abstraction is integrated throughout the container, it’s easy to route resolution of placeholders through it. This means that you may configure the resolution process in any way you like: change the precedence of searching through system properties and environment variables, or remove them entirely; add your own property sources to the mix as appropriate.

Concretely, the following statement works regardless of where the customer property is defined, as long as it is available in the Environment:

<beans>
    <import resource="com/bank/service/${customer}-config.xml"/>
</beans>
7.14 Registering a LoadTimeWeaver
The LoadTimeWeaver is used by Spring to dynamically transform classes as they are loaded into the Java virtual machine (JVM).

To enable load-time weaving add the @EnableLoadTimeWeaving to one of your @Configuration classes:

@Configuration
@EnableLoadTimeWeaving
public class AppConfig {
}
Alternatively for XML configuration use the context:load-time-weaver element:

<beans>
    <context:load-time-weaver/>
</beans>
Once configured for the ApplicationContext. Any bean within that ApplicationContext may implement LoadTimeWeaverAware, thereby receiving a reference to the load-time weaver instance. This is particularly useful in combination with Spring’s JPA support where load-time weaving may be necessary for JPA class transformation. Consult the LocalContainerEntityManagerFactoryBean javadocs for more detail. For more on AspectJ load-time weaving, see Section 11.8.4, “Load-time weaving with AspectJ in the Spring Framework”.

7.15 Additional capabilities of the ApplicationContext
As was discussed in the chapter introduction, the org.springframework.beans.factory package provides basic functionality for managing and manipulating beans, including in a programmatic way. The org.springframework.context package adds the ApplicationContext interface, which extends the BeanFactory interface, in addition to extending other interfaces to provide additional functionality in a more application framework-oriented style. Many people use the ApplicationContext in a completely declarative fashion, not even creating it programmatically, but instead relying on support classes such as ContextLoader to automatically instantiate an ApplicationContext as part of the normal startup process of a Java EE web application.

To enhance BeanFactory functionality in a more framework-oriented style the context package also provides the following functionality:

Access to messages in i18n-style, through the MessageSource interface.
Access to resources, such as URLs and files, through the ResourceLoader interface.
Event publication to namely beans implementing the ApplicationListener interface, through the use of the ApplicationEventPublisher interface.
Loading of multiple (hierarchical) contexts, allowing each to be focused on one particular layer, such as the web layer of an application, through the HierarchicalBeanFactory interface.
7.15.1 Internationalization using MessageSource
The ApplicationContext interface extends an interface called MessageSource, and therefore provides internationalization (i18n) functionality. Spring also provides the interface HierarchicalMessageSource, which can resolve messages hierarchically. Together these interfaces provide the foundation upon which Spring effects message resolution. The methods defined on these interfaces include:

String getMessage(String code, Object[] args, String default, Locale loc): The basic method used to retrieve a message from the MessageSource. When no message is found for the specified locale, the default message is used. Any arguments passed in become replacement values, using the MessageFormat functionality provided by the standard library.
String getMessage(String code, Object[] args, Locale loc): Essentially the same as the previous method, but with one difference: no default message can be specified; if the message cannot be found, a NoSuchMessageException is thrown.
String getMessage(MessageSourceResolvable resolvable, Locale locale): All properties used in the preceding methods are also wrapped in a class named MessageSourceResolvable, which you can use with this method.
When an ApplicationContext is loaded, it automatically searches for a MessageSource bean defined in the context. The bean must have the name messageSource. If such a bean is found, all calls to the preceding methods are delegated to the message source. If no message source is found, the ApplicationContext attempts to find a parent containing a bean with the same name. If it does, it uses that bean as the MessageSource. If the ApplicationContext cannot find any source for messages, an empty DelegatingMessageSource is instantiated in order to be able to accept calls to the methods defined above.

Spring provides two MessageSource implementations, ResourceBundleMessageSource and StaticMessageSource. Both implement HierarchicalMessageSource in order to do nested messaging. The StaticMessageSource is rarely used but provides programmatic ways to add messages to the source. The ResourceBundleMessageSource is shown in the following example:

<beans>
    <bean id="messageSource"
            class="org.springframework.context.support.ResourceBundleMessageSource">
        <property name="basenames">
            <list>
                <value>format</value>
                <value>exceptions</value>
                <value>windows</value>
            </list>
        </property>
    </bean>
</beans>
In the example it is assumed you have three resource bundles defined in your classpath called format, exceptions and windows. Any request to resolve a message will be handled in the JDK standard way of resolving messages through ResourceBundles. For the purposes of the example, assume the contents of two of the above resource bundle files are…​

# in format.properties
message=Alligators rock!
# in exceptions.properties
argument.required=The {0} argument is required.
A program to execute the MessageSource functionality is shown in the next example. Remember that all ApplicationContext implementations are also MessageSource implementations and so can be cast to the MessageSource interface.

public static void main(String[] args) {
    MessageSource resources = new ClassPathXmlApplicationContext("beans.xml");
    String message = resources.getMessage("message", null, "Default", null);
    System.out.println(message);
}
The resulting output from the above program will be…​

Alligators rock!
So to summarize, the MessageSource is defined in a file called beans.xml, which exists at the root of your classpath. The messageSource bean definition refers to a number of resource bundles through its basenames property. The three files that are passed in the list to the basenames property exist as files at the root of your classpath and are called format.properties, exceptions.properties, and windows.properties respectively.

The next example shows arguments passed to the message lookup; these arguments will be converted into Strings and inserted into placeholders in the lookup message.

<beans>

    <!-- this MessageSource is being used in a web application -->
    <bean id="messageSource" class="org.springframework.context.support.ResourceBundleMessageSource">
        <property name="basename" value="exceptions"/>
    </bean>

    <!-- lets inject the above MessageSource into this POJO -->
    <bean id="example" class="com.foo.Example">
        <property name="messages" ref="messageSource"/>
    </bean>

</beans>
public class Example {

    private MessageSource messages;

    public void setMessages(MessageSource messages) {
        this.messages = messages;
    }

    public void execute() {
        String message = this.messages.getMessage("argument.required",
            new Object [] {"userDao"}, "Required", null);
        System.out.println(message);
    }
}
The resulting output from the invocation of the execute() method will be…​

The userDao argument is required.
With regard to internationalization (i18n), Spring’s various MessageSource implementations follow the same locale resolution and fallback rules as the standard JDK ResourceBundle. In short, and continuing with the example messageSource defined previously, if you want to resolve messages against the British (en-GB) locale, you would create files called format_en_GB.properties, exceptions_en_GB.properties, and windows_en_GB.properties respectively.

Typically, locale resolution is managed by the surrounding environment of the application. In this example, the locale against which (British) messages will be resolved is specified manually.

# in exceptions_en_GB.properties
argument.required=Ebagum lad, the {0} argument is required, I say, required.
public static void main(final String[] args) {
    MessageSource resources = new ClassPathXmlApplicationContext("beans.xml");
    String message = resources.getMessage("argument.required",
        new Object [] {"userDao"}, "Required", Locale.UK);
    System.out.println(message);
}
The resulting output from the running of the above program will be…​

Ebagum lad, the 'userDao' argument is required, I say, required.
You can also use the MessageSourceAware interface to acquire a reference to any MessageSource that has been defined. Any bean that is defined in an ApplicationContext that implements the MessageSourceAware interface is injected with the application context’s MessageSource when the bean is created and configured.

[Note]
As an alternative to ResourceBundleMessageSource, Spring provides a ReloadableResourceBundleMessageSource class. This variant supports the same bundle file format but is more flexible than the standard JDK based ResourceBundleMessageSource implementation. In particular, it allows for reading files from any Spring resource location (not just from the classpath) and supports hot reloading of bundle property files (while efficiently caching them in between). Check out the ReloadableResourceBundleMessageSource javadocs for details.

7.15.2 Standard and custom events
Event handling in the ApplicationContext is provided through the ApplicationEvent class and ApplicationListener interface. If a bean that implements the ApplicationListener interface is deployed into the context, every time an ApplicationEvent gets published to the ApplicationContext, that bean is notified. Essentially, this is the standard Observer design pattern.

[Tip]
As of Spring 4.2, the event infrastructure has been significantly improved and offer an annotation-based model as well as the ability to publish any arbitrary event, that is an object that does not necessarily extend from ApplicationEvent. When such an object is published we wrap it in an event for you.

Spring provides the following standard events:

Table 7.7. Built-in Events

Event	Explanation
ContextRefreshedEvent

Published when the ApplicationContext is initialized or refreshed, for example, using the refresh() method on the ConfigurableApplicationContext interface. "Initialized" here means that all beans are loaded, post-processor beans are detected and activated, singletons are pre-instantiated, and the ApplicationContext object is ready for use. As long as the context has not been closed, a refresh can be triggered multiple times, provided that the chosen ApplicationContext actually supports such "hot" refreshes. For example, XmlWebApplicationContext supports hot refreshes, but GenericApplicationContext does not.

ContextStartedEvent

Published when the ApplicationContext is started, using the start() method on the ConfigurableApplicationContext interface. "Started" here means that all Lifecycle beans receive an explicit start signal. Typically this signal is used to restart beans after an explicit stop, but it may also be used to start components that have not been configured for autostart , for example, components that have not already started on initialization.

ContextStoppedEvent

Published when the ApplicationContext is stopped, using the stop() method on the ConfigurableApplicationContext interface. "Stopped" here means that all Lifecycle beans receive an explicit stop signal. A stopped context may be restarted through a start() call.

ContextClosedEvent

Published when the ApplicationContext is closed, using the close() method on the ConfigurableApplicationContext interface. "Closed" here means that all singleton beans are destroyed. A closed context reaches its end of life; it cannot be refreshed or restarted.

RequestHandledEvent

A web-specific event telling all beans that an HTTP request has been serviced. This event is published after the request is complete. This event is only applicable to web applications using Spring’s DispatcherServlet.


You can also create and publish your own custom events. This example demonstrates a simple class that extends Spring’s ApplicationEvent base class:

public class BlackListEvent extends ApplicationEvent {

    private final String address;
    private final String content;

    public BlackListEvent(Object source, String address, String content) {
        super(source);
        this.address = address;
        this.content = content;
    }

    // accessor and other methods...
}
To publish a custom ApplicationEvent, call the publishEvent() method on an ApplicationEventPublisher. Typically this is done by creating a class that implements ApplicationEventPublisherAware and registering it as a Spring bean. The following example demonstrates such a class:

public class EmailService implements ApplicationEventPublisherAware {

    private List<String> blackList;
    private ApplicationEventPublisher publisher;

    public void setBlackList(List<String> blackList) {
        this.blackList = blackList;
    }

    public void setApplicationEventPublisher(ApplicationEventPublisher publisher) {
        this.publisher = publisher;
    }

    public void sendEmail(String address, String content) {
        if (blackList.contains(address)) {
            publisher.publishEvent(new BlackListEvent(this, address, content));
            return;
        }
        // send email...
    }
}
At configuration time, the Spring container will detect that EmailService implements ApplicationEventPublisherAware and will automatically call setApplicationEventPublisher(). In reality, the parameter passed in will be the Spring container itself; you’re simply interacting with the application context via its ApplicationEventPublisher interface.

To receive the custom ApplicationEvent, create a class that implements ApplicationListener and register it as a Spring bean. The following example demonstrates such a class:

public class BlackListNotifier implements ApplicationListener<BlackListEvent> {

    private String notificationAddress;

    public void setNotificationAddress(String notificationAddress) {
        this.notificationAddress = notificationAddress;
    }

    public void onApplicationEvent(BlackListEvent event) {
        // notify appropriate parties via notificationAddress...
    }
}
Notice that ApplicationListener is generically parameterized with the type of your custom event, BlackListEvent. This means that the onApplicationEvent() method can remain type-safe, avoiding any need for downcasting. You may register as many event listeners as you wish, but note that by default event listeners receive events synchronously. This means the publishEvent() method blocks until all listeners have finished processing the event. One advantage of this synchronous and single-threaded approach is that when a listener receives an event, it operates inside the transaction context of the publisher if a transaction context is available. If another strategy for event publication becomes necessary, refer to the javadoc for Spring’s ApplicationEventMulticaster interface.

The following example shows the bean definitions used to register and configure each of the classes above:

<bean id="emailService" class="example.EmailService">
    <property name="blackList">
        <list>
            <value>known.spammer@example.org</value>
            <value>known.hacker@example.org</value>
            <value>john.doe@example.org</value>
        </list>
    </property>
</bean>

<bean id="blackListNotifier" class="example.BlackListNotifier">
    <property name="notificationAddress" value="blacklist@example.org"/>
</bean>
Putting it all together, when the sendEmail() method of the emailService bean is called, if there are any emails that should be blacklisted, a custom event of type BlackListEvent is published. The blackListNotifier bean is registered as an ApplicationListener and thus receives the BlackListEvent, at which point it can notify appropriate parties.

[Note]
Spring’s eventing mechanism is designed for simple communication between Spring beans within the same application context. However, for more sophisticated enterprise integration needs, the separately-maintained Spring Integration project provides complete support for building lightweight, pattern-oriented, event-driven architectures that build upon the well-known Spring programming model.

Annotation-based event listeners
As of Spring 4.2, an event listener can be registered on any public method of a managed bean via the EventListener annotation. The BlackListNotifier can be rewritten as follows:

public class BlackListNotifier {

    private String notificationAddress;

    public void setNotificationAddress(String notificationAddress) {
        this.notificationAddress = notificationAddress;
    }

    @EventListener
    public void processBlackListEvent(BlackListEvent event) {
        // notify appropriate parties via notificationAddress...
    }
}
As you can see above, the method signature once again declares the event type it listens to, but this time with a flexible name and without implementing a specific listener interface. The event type can also be narrowed through generics as long as the actual event type resolves your generic parameter in its implementation hierarchy.

If your method should listen to several events or if you want to define it with no parameter at all, the event type(s) can also be specified on the annotation itself:

@EventListener({ContextStartedEvent.class, ContextRefreshedEvent.class})
public void handleContextStart() {
    ...
}
It is also possible to add additional runtime filtering via the condition attribute of the annotation that defines a SpEL expression that should match to actually invoke the method for a particular event.

For instance, our notifier can be rewritten to be only invoked if the content attribute of the event is equal to foo:

@EventListener(condition = "#blEvent.content == 'foo'")
public void processBlackListEvent(BlackListEvent blEvent) {
    // notify appropriate parties via notificationAddress...
}
Each SpEL expression evaluates against a dedicated context. The next table lists the items made available to the context so one can use them for conditional event processing:

Table 7.8. Event SpEL available metadata

Name	Location	Description	Example
Event

root object

The actual ApplicationEvent

#root.event

Arguments array

root object

The arguments (as array) used for invoking the target

#root.args[0]

Argument name

evaluation context

Name of any of the method arguments. If for some reason the names are not available (e.g. no debug information), the argument names are also available under the #a<#arg> where #arg stands for the argument index (starting from 0).

#blEvent or #a0 (one can also use #p0 or #p<#arg> notation as an alias).


Note that #root.event allows you to access to the underlying event, even if your method signature actually refers to an arbitrary object that was published.

If you need to publish an event as the result of processing another, just change the method signature to return the event that should be published, something like:

@EventListener
public ListUpdateEvent handleBlackListEvent(BlackListEvent event) {
    // notify appropriate parties via notificationAddress and
    // then publish a ListUpdateEvent...
}
[Note]
This feature is not supported for asynchronous listeners.

This new method will publish a new ListUpdateEvent for every BlackListEvent handled by the method above. If you need to publish several events, just return a Collection of events instead.

Asynchronous Listeners
If you want a particular listener to process events asynchronously, simply reuse the regular @Async support:

@EventListener
@Async
public void processBlackListEvent(BlackListEvent event) {
    // BlackListEvent is processed in a separate thread
}
Be aware of the following limitations when using asynchronous events:

If the event listener throws an Exception it will not be propagated to the caller, check AsyncUncaughtExceptionHandler for more details.
Such event listener cannot send replies. If you need to send another event as the result of the processing, inject ApplicationEventPublisher to send the event manually.
Ordering listeners
If you need the listener to be invoked before another one, just add the @Order annotation to the method declaration:

@EventListener
@Order(42)
public void processBlackListEvent(BlackListEvent event) {
    // notify appropriate parties via notificationAddress...
}
Generic events
You may also use generics to further define the structure of your event. Consider an EntityCreatedEvent<T> where T is the type of the actual entity that got created. You can create the following listener definition to only receive EntityCreatedEvent for a Person:

@EventListener
public void onPersonCreated(EntityCreatedEvent<Person> event) {
    ...
}
Due to type erasure, this will only work if the event that is fired resolves the generic parameter(s) on which the event listener filters on (that is something like class PersonCreatedEvent extends EntityCreatedEvent<Person> { …​ }).

In certain circumstances, this may become quite tedious if all events follow the same structure (as it should be the case for the event above). In such a case, you can implement ResolvableTypeProvider to guide the framework beyond what the runtime environment provides:

public class EntityCreatedEvent<T> extends ApplicationEvent implements ResolvableTypeProvider {

    public EntityCreatedEvent(T entity) {
        super(entity);
    }

    @Override
    public ResolvableType getResolvableType() {
        return ResolvableType.forClassWithGenerics(getClass(),
                ResolvableType.forInstance(getSource()));
    }
}
[Tip]
This works not only for ApplicationEvent but any arbitrary object that you’d send as an event.

7.15.3 Convenient access to low-level resources
For optimal usage and understanding of application contexts, users should generally familiarize themselves with Spring’s Resource abstraction, as described in the chapter Chapter 8, Resources.

An application context is a ResourceLoader, which can be used to load Resources. A Resource is essentially a more feature rich version of the JDK class java.net.URL, in fact, the implementations of the Resource wrap an instance of java.net.URL where appropriate. A Resource can obtain low-level resources from almost any location in a transparent fashion, including from the classpath, a filesystem location, anywhere describable with a standard URL, and some other variations. If the resource location string is a simple path without any special prefixes, where those resources come from is specific and appropriate to the actual application context type.

You can configure a bean deployed into the application context to implement the special callback interface, ResourceLoaderAware, to be automatically called back at initialization time with the application context itself passed in as the ResourceLoader. You can also expose properties of type Resource, to be used to access static resources; they will be injected into it like any other properties. You can specify those Resource properties as simple String paths, and rely on a special JavaBean PropertyEditor that is automatically registered by the context, to convert those text strings to actual Resource objects when the bean is deployed.

The location path or paths supplied to an ApplicationContext constructor are actually resource strings, and in simple form are treated appropriately to the specific context implementation. ClassPathXmlApplicationContext treats a simple location path as a classpath location. You can also use location paths (resource strings) with special prefixes to force loading of definitions from the classpath or a URL, regardless of the actual context type.

7.15.4 Convenient ApplicationContext instantiation for web applications
You can create ApplicationContext instances declaratively by using, for example, a ContextLoader. Of course you can also create ApplicationContext instances programmatically by using one of the ApplicationContext implementations.

You can register an ApplicationContext using the ContextLoaderListener as follows:

<context-param>
    <param-name>contextConfigLocation</param-name>
    <param-value>/WEB-INF/daoContext.xml /WEB-INF/applicationContext.xml</param-value>
</context-param>

<listener>
    <listener-class>org.springframework.web.context.ContextLoaderListener</listener-class>
</listener>
The listener inspects the contextConfigLocation parameter. If the parameter does not exist, the listener uses /WEB-INF/applicationContext.xml as a default. When the parameter does exist, the listener separates the String by using predefined delimiters (comma, semicolon and whitespace) and uses the values as locations where application contexts will be searched. Ant-style path patterns are supported as well. Examples are /WEB-INF/*Context.xml for all files with names ending with "Context.xml", residing in the "WEB-INF" directory, and /WEB-INF/**/*Context.xml, for all such files in any subdirectory of "WEB-INF".

7.15.5 Deploying a Spring ApplicationContext as a Java EE RAR file
It is possible to deploy a Spring ApplicationContext as a RAR file, encapsulating the context and all of its required bean classes and library JARs in a Java EE RAR deployment unit. This is the equivalent of bootstrapping a standalone ApplicationContext, just hosted in Java EE environment, being able to access the Java EE servers facilities. RAR deployment is more natural alternative to scenario of deploying a headless WAR file, in effect, a WAR file without any HTTP entry points that is used only for bootstrapping a Spring ApplicationContext in a Java EE environment.

RAR deployment is ideal for application contexts that do not need HTTP entry points but rather consist only of message endpoints and scheduled jobs. Beans in such a context can use application server resources such as the JTA transaction manager and JNDI-bound JDBC DataSources and JMS ConnectionFactory instances, and may also register with the platform’s JMX server - all through Spring’s standard transaction management and JNDI and JMX support facilities. Application components can also interact with the application server’s JCA WorkManager through Spring’s TaskExecutor abstraction.

Check out the javadoc of the SpringContextResourceAdapter class for the configuration details involved in RAR deployment.

For a simple deployment of a Spring ApplicationContext as a Java EE RAR file: package all application classes into a RAR file, which is a standard JAR file with a different file extension. Add all required library JARs into the root of the RAR archive. Add a "META-INF/ra.xml" deployment descriptor (as shown in SpringContextResourceAdapters javadoc) and the corresponding Spring XML bean definition file(s) (typically "META-INF/applicationContext.xml"), and drop the resulting RAR file into your application server’s deployment directory.

[Note]
Such RAR deployment units are usually self-contained; they do not expose components to the outside world, not even to other modules of the same application. Interaction with a RAR-based ApplicationContext usually occurs through JMS destinations that it shares with other modules. A RAR-based ApplicationContext may also, for example, schedule some jobs, reacting to new files in the file system (or the like). If it needs to allow synchronous access from the outside, it could for example export RMI endpoints, which of course may be used by other application modules on the same machine.

7.16 The BeanFactory
The BeanFactory API provides the underlying basis for Spring’s IoC functionality. Its specific contracts are mostly used in integration with other parts of Spring and related third-party frameworks, and its DefaultListableBeanFactory implementation is a key delegate within the higher-level GenericApplicationContext container.

BeanFactory and related interfaces such as BeanFactoryAware, InitializingBean, DisposableBean are important integration points for other framework components: not requiring any annotations or even reflection, they allow for very efficient interaction between the container and its components. Application-level beans may use the same callback interfaces but will typically prefer declarative dependency injection instead, either via annotations or through programmatic configuration.

Note that the core BeanFactory API level and its DefaultListableBeanFactory implementation do not make assumptions about the configuration format or any component annotations to be used. All of these flavors come in through extensions such as XmlBeanDefinitionReader and AutowiredAnnotationBeanPostProcessor, operating on shared BeanDefinition objects as a core metadata representation. This is the essence of what makes Spring’s container so flexible and extensible.

The following section explains the differences between the BeanFactory and ApplicationContext container levels and the implications on bootstrapping.

7.16.1 BeanFactory or ApplicationContext?
Use an ApplicationContext unless you have a good reason for not doing so, with GenericApplicationContext and its subclass AnnotationConfigApplicationContext as the common implementations for custom bootstrapping. These are the primary entry points to Spring’s core container for all common purposes: loading of configuration files, triggering a classpath scan, programmatically registering bean definitions and annotated classes.

Because an ApplicationContext includes all functionality of a BeanFactory, it is generally recommended over a plain BeanFactory, except for a scenarios where full control over bean processing is needed. Within an ApplicationContext such as the GenericApplicationContext implementation, several kinds of beans will be detected by convention (i.e. by bean name or by bean type), in particular post-processors, whereas a plain DefaultListableBeanFactory is agnostic about any special beans.

For many extended container features such as annotation processing and AOP proxying, the BeanPostProcessor extension point is essential. If you use only a plain DefaultListableBeanFactory, such post-processors will not get detected and activated by default. This situation could be confusing because nothing is actually wrong with your bean configuration; it is rather the container which needs to be fully bootstrapped through additional setup in such a scenario.

The following table lists features provided by the BeanFactory and ApplicationContext interfaces and implementations.

Table 7.9. Feature Matrix

Feature	BeanFactory	ApplicationContext
Bean instantiation/wiring

Yes

Yes

Integrated lifecycle management

No

Yes

Automatic BeanPostProcessor registration

No

Yes

Automatic BeanFactoryPostProcessor registration

No

Yes

Convenient MessageSource access (for internalization)

No

Yes

Built-in ApplicationEvent publication mechanism

No

Yes


To explicitly register a bean post-processor with a DefaultListableBeanFactory, you need to programmatically call addBeanPostProcessor:

DefaultListableBeanFactory factory = new DefaultListableBeanFactory();
// populate the factory with bean definitions

// now register any needed BeanPostProcessor instances
factory.addBeanPostProcessor(new AutowiredAnnotationBeanPostProcessor());
factory.addBeanPostProcessor(new MyBeanPostProcessor());

// now start using the factory
To apply a BeanFactoryPostProcessor to a plain DefaultListableBeanFactory, you need to call its postProcessBeanFactory method:

DefaultListableBeanFactory factory = new DefaultListableBeanFactory();
XmlBeanDefinitionReader reader = new XmlBeanDefinitionReader(factory);
reader.loadBeanDefinitions(new FileSystemResource("beans.xml"));

// bring in some property values from a Properties file
PropertyPlaceholderConfigurer cfg = new PropertyPlaceholderConfigurer();
cfg.setLocation(new FileSystemResource("jdbc.properties"));

// now actually do the replacement
cfg.postProcessBeanFactory(factory);
In both cases, the explicit registration steps are inconvenient, which is why the various ApplicationContext variants are preferred over a plain DefaultListableBeanFactory in Spring-backed applications, especially when relying on BeanFactoryPostProcessors and BeanPostProcessors for extended container functionality in a typical enterprise setup.

[Note]
An AnnotationConfigApplicationContext has all common annotation post-processors registered out of the box and may bring in additional processors underneath the covers through configuration annotations such as @EnableTransactionManagement. At the abstraction level of Spring’s annotation-based configuration model, the notion of bean post-processors becomes a mere internal container detail.

7.16.2 Glue code and the evil singleton
It is best to write most application code in a dependency-injection (DI) style, where that code is served out of a Spring IoC container, has its own dependencies supplied by the container when it is created, and is completely unaware of the container. However, for the small glue layers of code that are sometimes needed to tie other code together, you sometimes need a singleton (or quasi-singleton) style access to a Spring IoC container. For example, third-party code may try to construct new objects directly ( Class.forName() style), without the ability to get these objects out of a Spring IoC container.If the object constructed by the third-party code is a small stub or proxy, which then uses a singleton style access to a Spring IoC container to get a real object to delegate to, then inversion of control has still been achieved for the majority of the code (the object coming out of the container). Thus most code is still unaware of the container or how it is accessed, and remains decoupled from other code, with all ensuing benefits. EJBs may also use this stub/proxy approach to delegate to a plain Java implementation object, retrieved from a Spring IoC container. While the Spring IoC container itself ideally does not have to be a singleton, it may be unrealistic in terms of memory usage or initialization times (when using beans in the Spring IoC container such as a Hibernate SessionFactory) for each bean to use its own, non-singleton Spring IoC container.

Looking up the application context in a service locator style is sometimes the only option for accessing shared Spring-managed components, such as in an EJB 2.1 environment, or when you want to share a single ApplicationContext as a parent to WebApplicationContexts across WAR files. In this case you should look into using the utility class ContextSingletonBeanFactoryLocator locator that is described in this Spring team blog entry.


[1] See Background

[2] See Section 7.4.1, “Dependency Injection”

# 参考：
https://blog.csdn.net/tangtong1/article/details/51960382