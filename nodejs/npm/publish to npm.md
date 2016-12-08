## 前言
我们npm publish发布的时候，一定是本地文件发布到远程仓库，并且登录到http://registry.npmjs.org（即npm adduser或npmlogin）之后，才可以进行发布。

## 一、npm包结构（编写）
npm包实际是一个存档文件，即一个目录直接打包为.zip或tar.gz格式的文件，安装后解压还原为目录。完全符合CommonJS规范的包目录应该包含如下这些文件。  
`package.json` : 包描述文件。  
`bin`: 用于存放可执行二进制文件的目录。  
`lib`：用于存放javascript代码的目录。  
`doc`：用于存放文档的目录。  
`test`: 用于存放单元测试用例的代码。  
我们开发npm包模块的时候，就可以按照以上目录结构，进行开发。  

## 二、npm包发布
### 1.npm init  
生产package.json文件，里面要注意参数，repository:""一定要填写仓库地址，因为最后npmjs，会从线上仓库获取。
### 2.注册包仓库账号  
为了维护包，NPM必须要使用仓库账号才允许将包发布到仓库中。注册账号的命令是npm adduser。这也是提问式的交互，按顺序进行即可：
```
npm adduser
Username:pingfan
Email:(xxx@sinaapp.com)
```
如果已经注册账号：  
则我们用npm login 登录即可  
证明是否登录成功：  
`npm who am i`  
### 3.上传npm包
上传包的命令是 npm publish <floder> .在刚刚创建的package.json文件所在的目录下，执行npm publish .开始上传，相关代码如下：
```
npm publish .
npm http PUT http://registry.npmjs.org/net-scan
npm http 400 http://registry.npmjs.org/net-scan
npm http PUT http://registry.npmjs.org/net-scan
npm http 201 http://registry.npmjs.org/net-scan
npm http GET http://registry.npmjs.org/net-scan
npm http 200 http://registry.npmjs.org/net-scan
npm http PUT http://registry.npmjs.org/net-scan/-/net-scan-0.0.0.tgz/-rev/1-96a1
2fd6f9e8e5359489a9f59d114f90
npm http 201 http://registry.npmjs.org/net-scan/-/net-scan-0.0.0.tgz/-rev/1-96a1
2fd6f9e8e5359489a9f59d114f90
npm http PUT http://registry.npmjs.org/net-scan/0.0.0/-tag/latest
npm http 201 http://registry.npmjs.org/net-scan/0.0.0/-tag/latest
+ net-scan@0.0.0
```
如果你以后修改了代码，然后想要同步到 npm 上的话请修改 `package.json` 中的 `version` 然后再次 `publish`，更新的版本上传的版本要大于上次

### 4.安装包
为了体验和测试自己上传的包，可以换一个目录执行 npm install xxx --save 安装它：
`npm install xxx -save --registory=http://registory.npmjs.org`
管理包权限：  
通常，一个包只有一个拥有权限进行发布。如果需要多人进行发布，可以使用npm owner 命令帮助你管理包的所有者：  
`npm owner ls eventproxy`  
使用这个命令，也可以添加包的拥有者，删除一个包的拥有者：  
```
npm owner ls <package name>
npm owner add <user> <package name>
npm owner rm <user> <package name>
```
## 三、npm包发布问题及解决  
### 1.使用 cnpm 的注意报错：  
`no_perms Private mode enable, only admin can publish this module`
设置回原本的就可以了：  
`npm config set registry http://registry.npmjs.org`  
### 2.npm包`package.json`中`registory`属性一定要填写，每次`publish npm`时`package.json`中`version`版本一定要大于上一次。
### 3.`npm publish failed put 500  unexpected status code 401`这样的报错信息，往往是没有登录成功，操作`npm login`

### 发布到`nexus npm`
Since the .npmrc file usually contains a registry value intended only for getting new packages, 
a simple way to override this value is to provide a registry to the publish command:  
`npm publish --registry http://localhost:8081/nexus/content/repositories/npm-internal/`  
Alternately, you can edit your package.json file and add a publishConfig section:
```
  "publishConfig" : {
    "registry" : "http://localhost:8081/nexus/content/repositories/npm-internal/"
  },
```

see more : `http://books.sonatype.com/nexus-book/reference/npm-deploying-packages.html`