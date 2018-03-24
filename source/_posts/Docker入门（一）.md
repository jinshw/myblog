---
title: Docker入门（一）
date: 2018-03-17 22:46:11
tags: [Docker]
comments: true
categories: Docker
layout: post
keywords: Hexo, Docker
description: 故不积跬步，无以至千里；不积小流，无以成江海。
---
# Docker 是什么？
    Docker属于Linux容器的一种封装，提供简单易用的容器使用接口
    Docker 将应用程序与该程序的依赖，打包在一个文件里面。运行这个文件，就会生成一个虚拟容器。程序在这个虚拟容器里运行，就好像在真实的物理机上运行一样。有了 Docker，就不用担心环境问题。
    总体来说，Docker 的接口相当简单，用户可以方便地创建和使用容器，把自己的应用放入容器。容器还可以进行版本管理、复制、分享、修改，就像管理普通的代码一样。

<!-- more -->

# Docker的用途
Docker的主要用途，目前有三大类：   
1. **提供一次性运行环境**：比如，本地测试其他人的软件、持续集成时提供单元测试和构建环境。 
2. **提供弹性的云服务**
3. **组件微服务架构**。通过多个容器，一台机器可以跑多个服务，因此在本机上可以模拟出微服务架构。



# Docker 架构

​	Docker使用一个客户端服务器架构。Docker客户端和Docker守护进程交流，Docker守护进程做非常重要的工作，构建，运行和分发你的Docker容器。Docker客户端和守护进程可以运行在同样的系统上，或者是你可以连接一个Docker客户端到一个远程Docker守护进程中。Docker客户端和守护进程通过sockets或通过RESTful API进行沟通交流。

![](/images/jiagou.jpg)

## Docker 守护进程

Docker daemon

## Docker 客户端

Client

## Docker 内部



## Docker 镜像

Image

## Docker容器

Container





# Docker 安装

# image文件

**Docker 把应用程序及其依赖，打包在 image 文件里面。**只有通过这个文件，才能生成 Docker 容器。image 文件可以看作是容器的模板。Docker 根据 image 文件生成容器的实例。同一个 image 文件，可以生成多个同时运行的容器实例。

image 是二进制文件。实际开发中，一个 image 文件往往通过继承另一个 image 文件，加上一些个性化设置而生成。举例来说，你可以在 Ubuntu 的 image 基础上，往里面加入 Apache 服务器，形成你的 image。

```
# 列出本机的所有 image 文件。
$ docker image ls

# 删除 image 文件
$ docker image rm [imageName]
```
image 文件是通用的，一台机器的 image 文件拷贝到另一台机器，照样可以使用。一般来说，为了节省时间，我们应该尽量使用别人制作好的 image 文件，而不是自己制作。即使要定制，也应该基于别人的 image 文件进行加工，而不是从零开始制作。
为了方便共享，image 文件制作完成后，可以上传到网上的仓库。Docker 的官方仓库 Docker Hub 是最重要、最常用的 image 仓库。此外，出售自己制作的 image 文件也是可以的。

# 实例 Hello World

首先，将image文件从仓库中抓取到本地

~~~
# 从docker仓库中抓取到本地
docker image pull library/hello-world
# 由于Docker官方提供的image文件都放在library组里面，所以它是默认组，可以省略，因此，上面的命令可以写成下面这样。
docker image pull hello-world
~~~

在执行下面命令查看是否抓取成功

~~~
docker image ls 
# 或者
docker images
~~~

运行hello-world容器文件

~~~
docker container run hello-world
# 或者
docker run hello-world

Hello from Docker!
This message shows that your installation appears to be working correctly.
... ...
~~~

`docker container run`命令会从 image 文件，生成一个正在运行的容器实例。

注意，`docker container run`命令具有自动抓取 image 文件的功能。如果发现本地没有指定的 image 文件，就会从仓库自动抓取。因此，前面的`docker image pull`命令并不是必需的步骤。



输出这段提示以后，`hello world`就会停止运行，容器自动终止。

有些容器不会自动终止，因为提供的是服务。比如，安装运行 Ubuntu 的 image，就可以在命令行体验 Ubuntu 系统。

```
docker container run -it ubuntu bash
```

对于那些不会自动终止的容器，必须使用[`docker container kill`](https://docs.docker.com/engine/reference/commandline/container_kill/) 命令手动终止。

 ```
 $ docker container kill [containID]
 ```

# 容器文件

**image文件生成的容器实例，本身也是个文件，称位容器文件。**也就是说，一旦容器生成，就会同时存在两个文件，image和容器文件。而且关闭容器并不会删除容器文件，只是容器文件停止运行而已。

~~~
# 列出本机运行的容器
$ docker ps 
# 列出本机所有的容器（包括停止和运行）
$ docker ps -a
~~~

上面命令执行完毕后，例如下

~~~
CONTAINER ID        IMAGE                          COMMAND                  CREATED             STATUS              PORTS                    NAMES
f818cf258d37        hub.c.163.com/library/tomcat   "catalina.sh run"        25 hours ago        Up 25 hours         0.0.0.0:8080->8080/tcp   jolly_easley
c5fc06bc40bb        hub.c.163.com/library/mysql    "docker-entrypoint.s…"   6 days ago          Up 25 hours         0.0.0.0:3306->3306/tcp   ecstatic_pare
66afb1061bed        hub.c.163.com/library/nginx    "nginx -g 'daemon of…"   6 days ago          Up 25 hours         0.0.0.0:32768->80/tcp    naughty_clarke

~~~

停止容器

~~~
$ docker stop [容器ID]
或者
$ docker kill [容器ID]
~~~

启动已有容器

~~~
$ docker start [容器ID]
~~~

进入运行的容器命令窗口(bash)

~~~
$ docker exec -it [容器ID]
~~~



# Dockerfile

如何生成image镜像文件？如果想推广自己的软件，势必要自己制作image文件。这就需要Dockerfile文件，它是一个文本文件，用做配置image。Docker根据该文件生成二进制的image文件。

# 实例： 制作自己的Docker容器

**准备工作：** 下载jpress项目的war包

## 1. 编写Dockerfile文件

   首先，在项目的根目录下，新建一个文本文件 `.dockerignore` ，写入下面内容

   ~~~
   .git
   node_modules
   npm-debug.log
   ~~~

   上面代码表示，这三个路径要排除，不要打包进入image文件。如果你没有路径要排除，这个文件可以不创建。

   然后，在项目根目录下创建Dockerfile文件，写入下面内容：

   ~~~
   FROM node:8.4
   COPY . /app
   WORKDIR /app
   RUN npm install --registry=https://registry.npm.taobao.org
   EXPOSE 3000
   ~~~

   上面代码一共5行，含义如下：

   ~~~
   FROM node:8.4：该 image 文件继承官方的 node image，冒号表示标签，这里标签是8.4，即8.4版本的 node。
   COPY . /app：将当前目录下的所有文件（除了.dockerignore排除的路径），都拷贝进入 image 文件的/app目录。
   WORKDIR /app：指定接下来的工作路径为/app。
   RUN npm install：在/app目录下，运行npm install命令安装依赖。注意，安装后所有的依赖，都将打包进入 image 文件。
   EXPOSE 3000：将容器 3000 端口暴露出来， 允许外部连接这个端口。
   ~~~

   

## 2. 创建image文件

有了Dockerfile文件后，就可以使用`docker image build` 命令创建image文件了。

~~~
$ docker image build -t koa-demo .
# 或者
$ docker image build -t koa-demo:0.0.1 .
~~~

上面代码中，`-t` 参数用来指定image文件名称，后面还可以用冒号指定标签。如果不指定，默认的标签就是`latest`。最后的那个点标识Dockerfile文件所在的路径，上例是当前路径，所以是一个点。

如果运行成功，就可以看到新生产的image文件koa-demo了。

~~~
$ docker images
~~~



## 3. 生成容器

`docker run` 命令从image文件生成容器

~~~
$ docker run -p 9999:3000 -it koa-demo /bin/bash
# 或者
$ docker container run -p 8000:3000 -it koa-demo:0.0.1 /bin/bash
~~~

上面命令的各个参数含义如下：

`-p`参数：容器的 3000 端口映射到本机的 8000 端口。
 `-it`参数：容器的 Shell 映射到当前的 Shell，然后你在本机窗口输入的命令，就会传入容器。
 `koa-demo:0.0.1`：image 文件的名字（如果有标签，还需要提供标签，默认是 latest 标签）。
`/bin/bash`：容器启动以后，内部第一个执行的命令。这里是启动 Bash，保证用户可以使用 Shell。

`docker container run`命令的`--rm`参数，在容器终止运行后自动删除容器文件。

~~~
$ docker container run --rm -p 8000:3000 -it koa-demo /bin/bash
~~~



## 4. CMD命令

上一节的例子里面，容器启动以后，需要手动输入命令`node demos/01.js`。我们可以把这个命令写在 Dockerfile 里面，这样容器启动以后，这个命令就已经执行了，不用再手动输入了。

 ```
 FROM node:8.4
 COPY . /app
 WORKDIR /app
 RUN npm install --registry=https://registry.npm.taobao.org
 EXPOSE 3000
 CMD node demos/01.js
 ```

上面的 Dockerfile 里面，多了最后一行`CMD node demos/01.js`，它表示容器启动后自动执行`node demos/01.js`。

你可能会问，`RUN`命令与`CMD`命令的区别在哪里？简单说，`RUN`命令在 image 文件的构建阶段执行，执行结果都会打包进入 image 文件；`CMD`命令则是在容器启动后执行。另外，一个 Dockerfile 可以包含多个`RUN`命令，但是只能有一个`CMD`命令。

注意，指定了`CMD`命令以后，`docker container run`命令就不能附加命令了（比如前面的`/bin/bash`），否则它会覆盖`CMD`命令。现在，启动容器可以使用下面的命令。

 ```
$ docker container run --rm -p 8000:3000 -it koa-demo:0.0.1
 ```

## 5. 发布image文件

首先，去 [hub.docker.com](https://hub.docker.com/) 或 [cloud.docker.com](https://cloud.docker.com/) 注册一个账户。然后，用下面的命令登录。



# 其他有用的命令

1. docker container start

   前面的`docker run` 命令是新建容器，每次运行一次就有一个新容器。同样的命令运行两次，就会生成2个一模一样的容器文件。如果希望重复使用容器，就要使用`docker start`命令，使用它启动已生成、已停止运行的容器文件。

   ~~~
   $ docker start [containerID]
   ~~~

2. docker stop

   前面的`docker container kill`命令终止容器运行，相当于向容器里面的主进程发出 SIGKILL 信号。而`docker container stop`命令也是用来终止容器运行，相当于向容器里面的主进程发出 SIGTERM 信号，然后过一段时间再发出 SIGKILL 信号。

   ```
   $ docker container stop [containerID]
   ```
   这两个信号的差别是，应用程序收到 SIGTERM 信号以后，可以自行进行收尾清理工作，但也可以不理会这个信号。如果收到 SIGKILL 信号，就会强行立即终止，那些正在进行中的操作会全部丢失


3. docker logs

   `docker logs`命令用来查看docker容器的输出，即容器里面的shell的标准输出。如果`docker run`命令运行容器的时候，没有使用`-it`参数，就要用这个命令查看输出。

   ~~~
   docker logs [containerID]
   ~~~

4. docker exec

   `docker exec`命令是用于进入一个正在运行的docker容器。如果`docker run`命令运行容器的时候，没有使用`-it`参数，就要使用这个命令进入容器。一旦进入容器，就可以在容器的shell执行命令了。

   ~~~
   docker exec -it [containerID] /bin/bash
   ~~~

5. docker cp

   `docker cp`命令用于从正在运行的Docker容器里面，将文件拷贝到本机。下面是拷贝当前目录的写法。

   ~~~
   docker cp [containerID]:[/path/to/file] .

   #从主机复制到容器
   docker cp host_path containerID:container_path

   #从容器复制到主机
   docker cp containerID:container_path host_path
   ~~~

   ​