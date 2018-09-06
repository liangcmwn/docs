【java规范】Java spi机制浅谈
----------------------------------
我们系统里抽象的各个模块，往往有很多不同的实现方案，比如日志模块的方案，xml解析模块、jdbc模块的方案等。面向的对象的设计里，我们一般推荐模块之间基于接口编程，模块之间不对实现类进行硬编码。一旦代码里涉及具体的实现类，就违反了可拔插的原则，如果需要替换一种实现，就需要修改代码。

为了实现在模块装配的时候能不在程序里动态指明，这就需要一种服务发现机制。java spi就是提供这样的一个机制：为某个接口寻找服务实现的机制。有点类似IOC的思想，就是将装配的控制权移到程序之外，在模块化设计中这个机制尤其重要。

## java spi的具体约定如下 ：
当服务的提供者，提供了服务接口的一种实现之后，在jar包的META-INF/services/目录里同时创建一个以服务接口命名的文件。该文件里就是实现该服务接口的具体实现类。而当外部程序装配这个模块的时候，就能通过该jar包META-INF/services/里的配置文件找到具体的实现类名，并装载实例化，完成模块的注入。 

基于这样一个约定就能很好的找到服务接口的实现类，而不需要再代码里制定。

jdk提供服务实现查找的一个工具类：`java.util.ServiceLoader`


例子
#### 1.common-logging

apache最早提供的日志的门面接口。只有接口，没有实现。具体方案由各提供商实现，发现日志提供商是通过扫描 META-INF/services/org.apache.commons.logging.LogFactory配置文件，通过读取该文件的内容找到日志提工商实现类。只要我们的日志实现里包含了这个文件，并在文件里制定 LogFactory工厂接口的实现类即可。

#### 2.jdbc

jdbc4.0以前，开发人员还需要基于Class.forName("xxx")的方式来装载驱动，jdbc4也基于spi的机制来发现驱动提供商了，可以通过META-INF/services/java.sql.Driver文件里指定实现类的方式来暴露驱动提供者。

#### 3.自己编写简单例子

假设有一个内容搜索系统，分为展示和搜索两个模块。展示和搜索基于接口编程。搜索的实现可能是基于文件系统的搜索，也可能是基于数据库的搜索。实例代码如下

`Search.java`: 搜索接口
```java
package search;  
  
import java.util.List;  
  
import definition.Doc;  
  
public interface Search {  
    List<Doc> search(String keyword);  
}  
```

`FileSearch.java`:文件系统的搜索实现
```java
package search;  
  
import java.util.List;  
  
import definition.Doc;  
  
public class FileSearch implements Search {  
  
    @Override  
    public List<Doc> search(String keyword) {  
        System.out.println("now use file system search. keyword:" + keyword);  
        return null;  
    }  
  
}  
```

`DatabaseSearch.java`
```
package search;  
  
import java.util.List;  
  
import definition.Doc;  
  
public class DatabaseSearch implements Search {  
  
    @Override  
    public List<Doc> search(String keyword) {  
        System.out.println("now use database search. keyword:" + keyword);  
        return null;  
    }  
  
}  
```

`SearchTest.java`
```java
package search;  
  
import java.util.Iterator;  
import java.util.ServiceLoader;  
  
public class SearchTest {  
  
    public static void main(String[] args) {  
        ServiceLoader<Search> s = ServiceLoader.load(Search.class);  
        Iterator<Search> searchs = s.iterator();  
        if (searchs.hasNext()) {  
            Search curSearch = searchs.next();  
            curSearch.search("test");  
        }  
    }  
}  
```

最后创建在`META-INF/searvices/search.Search`文件。

当`search.Search`文件内容是"search.FileSearch"时，程序输出是：

`now use file system search. keyword:test`

当`search.Search`文件内容是"search.DatabaseSearch"时，程序输出是：

`now use database search. keyword:test `
可以看出SearchTest里没有任何和具体实现有关的代码，而是基于spi的机制去查找服务的实现。

参考文献：

http://docs.oracle.com/javase/1.4.2/docs/guide/jar/jar.html#Service%20Provider