## Docker container：

Docker container是image的实例，共享内核

Docker  container里可以运行不同os的image，比如Ubuntu的或者centos

Docker container不建议内部开启一个sshd服务，1.3版本后新增了docker exec命令进入容器进行排查问题

Docker container没有ip地址，通常不会有服务端口暴露，是一个封闭的沙盒

## Docker daemon：

Docker daemon是创建和运行container的Linux守护进程，也是Docker 最主要的核心组件

Docker daemon可以理解为Docker container的container

Docker daemon可以绑定本地端口并提供REST API服务，用来远程访问和控制