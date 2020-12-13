# Docker Platform

## 架构

APP 与底层 Infrastructure （基础设施）相隔离

```
graph TB
A(Application 应用)-->B(Docker Image 镜像)
B(Docker Image 镜像)-->C(Infrastructure Physical/Vertical 物理/虚拟机基本层)
```

### Docker Architecture

```
graph LR
A[Client]-->|docker build| B[Docker Host]
A[Client]-->|docker pull| B[Docker Host]
A[Client]-->|docker run| B[Docker Host]
B[Docker Host]-->C[Registry 镜像存储服务]

B[Docker Host]-->F[Docker Daemon]
F[Docker Daemon]-->D[Images]
D[Images 镜像]-->E[Containers 容器]
```

1. 后台进程（dockerd）
2. REST API Server
3. CLI 接口（Docker）

使用 `docker version` 查看上述相关版本  
使用 `ps -ef | grep docker` 查看后台进程  

### 底层技术支持

- NameSpace 做隔离 `pid`，`net`，`ipc`，`mnt`，`uts`
- Control Groups 做资源限制
- Union File Systems 做 `Container` 和 `Image` 的分层

## Docker Image

- 文件和 Meta Data 的集合（root filesystem）
- Image 是分层的。每一层都可以添加/删除/编辑文件，可以作为一个新的 Image
- 不同的 Image 可以共享使用相同的 Layer
- 本身 Image 是不可编辑的

### Image 的获取

1. Dockerfile 文件构建

```dockerfile
FROM ubuntu:20.04
LABEL maintainer="tabll<wts@tabll.cn>"
RUN apt get update && apt get install -y redis-server
EXPOSE 6379
ENTRYPOINT ["/usr/bin/redis-server"]
```

2. Pull from Registry 拉取

```sh
docker pull XXX:XX
```

### 构建 Base Image

```dockerfile
FROM scratch
ADD hello /
CMD ["/hello"]
```

查看历史： `docker history`

## Container 容器

通过 Image 创建，在此之上有一层 Container Layer（可读写）

Image 负责 APP 的存储与分发，Container 负责运行 APP

查看当前/所有的容器：
```sh
docker container ls
docker container ls -a
```

### 交互式运行

执行结束后不退出

```
docker run -it XXX:XX
```

参数说明：  
-i （--interactive：Keep STDIN open even if not attached）  
-t （--tty：Allocate a pseudo-TTY）  

### Docker 命令

```
Management Commands:
  builder     Manage builds
  config      Manage Docker configs
  container   Manage containers
  context     Manage contexts
  image       Manage images
  network     Manage networks
  node        Manage Swarm nodes
  plugin      Manage plugins
  scan*       Docker Scan (Docker Inc., v0.3.4)
  secret      Manage Docker secrets
  service     Manage services
  stack       Manage Docker stacks
  swarm       Manage Swarm
  system      Manage Docker
  trust       Manage trust on Docker images
  volume      Manage volumes

Commands:
  attach      Attach local standard input, output, and error streams to a running container
  build       Build an image from a Dockerfile
  commit      Create a new image from a container's changes
  cp          Copy files/folders between a container and the local filesystem
  create      Create a new container
  diff        Inspect changes to files or directories on a container's filesystem
  events      Get real time events from the server
  exec        Run a command in a running container
  export      Export a container's filesystem as a tar archive
  history     Show the history of an image
  images      List images
  import      Import the contents from a tarball to create a filesystem image
  info        Display system-wide information
  inspect     Return low-level information on Docker objects
  kill        Kill one or more running containers
  load        Load an image from a tar archive or STDIN
  login       Log in to a Docker registry
  logout      Log out from a Docker registry
  logs        Fetch the logs of a container
  pause       Pause all processes within one or more containers
  port        List port mappings or a specific mapping for the container
  ps          List containers
  pull        Pull an image or a repository from a registry
  push        Push an image or a repository to a registry
  rename      Rename a container
  restart     Restart one or more containers
  rm          Remove one or more containers 移除容器
  rmi         Remove one or more images 移除镜像
  run         Run a command in a new container
  save        Save one or more images to a tar archive (streamed to STDOUT by default)
  search      Search the Docker Hub for images
  start       Start one or more stopped containers
  stats       Display a live stream of container(s) resource usage statistics
  stop        Stop one or more running containers
  tag         Create a tag TARGET_IMAGE that refers to SOURCE_IMAGE
  top         Display the running processes of a container
  unpause     Unpause all processes within one or more containers
  update      Update configuration of one or more containers
  version     Show the Docker version information
  wait        Block until one or more containers stop, then print their exit codes
```

显示镜像ID（-q 只列出ID）
```
docker container ls -aq
```
等同于
```
docker container ls -a | awk {'print$1'}
```
删除所有的 Cantainer
```
docker rm $(docker container ls -aq)
```
删除所有退出的镜像
```
docker rm $(docker container ls -f "status=exited" -q)
```

## 构建 Docker 镜像

可以通过直接提交或者构建文件的方式

```
docker container commit
# 或者
docker commit
# 直接 commit 有风险，无法确保安全

docker image build
# 或者
docker build
```

### DockerFile 常用

#### FROM

```
FRPM scratch # 从头制作
FROM centos
FROM ubuntu:20.04
```

#### LABEL

```
LABEL maintainer="wts@tabll.cn"
LABEL version="1.0"
LABEL description="描述"
```

#### RUN

执行命令并创建新的 `Image Layer`

```
RUN yum update && yum install -y vim \
    python-dev # 使用反斜杠换行
RUN apt update && apt install -y perl \
    pwgen --no-install-recommends && rm -rf \
    /var/lib/apt/lists/* # 清理Cache 文件
RUN /bin/bash -c 'source $HOME/.bashrc;echo $HOME'
```

#### WORKDIR

设定当前工作目录

```
WORKDIR /test # 不存在将自动创建
WORKDIR demo
RUN pwd # 此时输出 /test/demo
```
不推荐使用 `RUN cd` 命令，且 `WORKDIR` 尽量使用绝对路径

#### ADD/COPY

```
ADD hello /

ADD test.tar.gz / # 添加到根目录并解压
```

```
WORKDIR /root
ADD hello test/ # /root/test/hello
```

```
WORKDIR /root
COPY hello test/ # /root/test/hello
```

多数情况下有限使用 `COPY`，`ADD` 具有额外的解压功能

如果要添加原创文件/目录，使用 `curl` 或 `wget`

#### ENV

```
# 设置常量
ENV MYSQL_VERSION 8.0
# 引用常量
RUN apt install -y mysql-server="${MYSQL_VERSION}" \
    && rm -rf /var/lib/apt/lists/*
```

#### CMD and ENTRYPOINT

CMD：设置容器启动后默认执行的命令和参数  
- 如果 `docker run` 指定了其他命令，则 `CMD` 的命令会被忽略
- 如果定义了多个 `CMD` 则只有最后一个会被执行

ENTRYPOINT：设置容器启动时运行的命令
- 让容器以应用程序或服务的形式运行
- 不会被忽略，一定会被执行
- 最好写一个 `shell` 脚本来作为 `entrypoint`

##### Shell 格式：

```
RUN apt install -y vim
CMD echo "hello docker"
ENTRYPOINT echo "hello docker"
```
将命令以 Shell 命令执行

##### Exec 格式：

```
RUN ["apt", "install", "-y", "vim"]
CMD ["/bin/echo", "hello docker"]
ENTRYPOINT ["/bin/echo", "hello docker"]
```

`Exec` 格式无法识别 `ENV` 变量 `$var`

例如：
```
ENV name XXX
ENTRYPOINT ["bin/echo", "$name"]
```
上面返回的其实是 `$name` 而不是 `XXX`

需要写成下面这样才可以：
```
ENV name XXX
ENTRYPOINT ["bin/bash", "-c", "echo $name"]
```

## 底层技术实现

Namespace：做隔离（pid、net、ipc、mnt、uts）
Control Groups：做资源限制（如 CPU 占用比例）
Union File Systems：做Container 和 Image 的分层

## 其他

Docker 官方提供的构建文件：
https://github.com/docker-library

DockerFile 官方指南
https://docs.docker.com/engine/reference/builder/
