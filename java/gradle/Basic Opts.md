### 使用maven仓库
```
apply 'maven'
repositories {
    //使用maven中心仓库
    mavenCentral() 
    //使用本地仓库
    maven { 
        url "http://localhost:8081/nexus/content/groups/public/"
    }
}
```

### Java Task
`compileJava` 编绎java class   
`processResource`  

依赖关系图
<img src="javaPluginTasks.png">



### 相关中文文档  
[Gradle中文使用文档](http://yuedu.baidu.com/ebook/f23af265998fcc22bcd10da2)  

