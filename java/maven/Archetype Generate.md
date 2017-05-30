## Maven 根据已有工程自定义模板以备快速复制项目
### 1.配置了Maven环境后，我们就可以使用maven的命令使用它内建的模板快速生成对应的工程项目  
```java
mvn archetype:generate  
```
此命令会列出一系列的maven工程模板，可以选择自己想要创建的工程模板对应的编号从而创建一个新的工程项目；  
```
mvn archetype:generate -DarchetypeCatalog=local  
```
此命令会列出本地所存在的模板，包括从maven仓库下载到本地的和本地自己创建的，我们后面可以用此命令查看我们自定义的模板；

### 2.进入要制作模板的项目根目录，执行生成模板的命令
```java
mvn archetype:create-from-project  
```
这条命令便是从当前工程中生成一个模板，生成的模板存放在target\generated-sources\

### 3.进入模板所在目录，执行安装命令把模板安装到本地maven仓库里面
```java
mvn install 
```
### 4.这里可以用上面的查看本地模板的命令验证是否成功把模板安装到本地成功了
```
mvn archetype:generate -DarchetypeCatalog=local  
```
### 5.新建一个文件夹，从命令行进入到该文件夹下，执行使用模板生成工程的命令，就快速的创建了一个新的工程；
```
mvn archetype:generate -DarchetypeGroupId= 模板的groupid -DarchetypeArtifactId=模板的artifactId-archetype -DarchetypeVersion=模板的version    
```
或者
```
mvn archetype:generate -DarchetypeCatalog=local  
```
跟着这个命令一步一步往下走，先选择要创建项目的模板编号，然后依次输入groupId，artifactId，version，package，etc；

