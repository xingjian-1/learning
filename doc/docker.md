#### docker简介
一款开源软件能否在商业上成功，很大程度上依赖三件事 - 成功的 user case(用例), 活跃的社区和一个好故事。Docker是一个开源的应用容器引擎，让开发者可以打包他们的应用到一个可移植的容器中,
然后发布到Linux机器或Windows机器上。采用客户端-服务器(C/S)架构模式，使用远程API来管理和创建Docker容器，Docker容器通过Docker镜像来创建。
#### docker请求流程
一个完整的Docker有以下几个部分组成：DockerClient客户端、Docker Daemon守护进程、Docker Image镜像、DockerContainer容器 <br/>
Docker daemon作为服务端接受来自客户的请求，并处理这些请求（创建、运行、分发容器）。客户端和服务端既可以运行在一个机器上，也可通过socket或者RESTful API来进行通信。
#### docker部署流程

#### docker常用命令
* 进入命令界面：win+R 输入cmd
* 搜索mysql：docker search mysql
* 下载mysql：docker pull mysql:5.7
* 查看下载后的镜像：docker images
* 启动mysql服务：docker run -p 3306:3306 --name mysql -e MYSQL_ROOT_PASSWORD=123456 -d mysql:5.7
* 查看服务启动是否成功：docker ps -a
