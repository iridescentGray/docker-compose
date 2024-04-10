# docker-compose

## 环境准备

### containerd

推荐使用 [containerd](https://containerd.io/) 作为后端容器工具，即使未来再安装 kubernetes 时，也可以轻松应对。

参考 [Getting started](https://containerd.io/docs/getting-started/) 安装 containerd 。

参考 [containerd/nerdctl](https://github.com/containerd/nerdctl) 安装为 containerd 适配的 Docker 命令。让你像使用 Docker 和 docker-compose 一样
使用 containerd 。

### Docker

请参照 [Get Docker](https://docs.docker.com/get-docker/) 中安装最新的 Docker 环境。

然后参照 [Install Docker Compose](https://docs.docker.com/compose/install/) 安装最新版本的 docker-compose 。

## 初始化网络

推荐先对容器环境做子网规划，并预先初始化外部子网。在启动服务时，将容器关联到子网。

### 原因

所有 docker-compose 都不应该依赖具体主机 IP ，例如 `192.168.22.102`

- 在服务间容器通信时，不依赖宿主机地址，通过内部主机名(容器名)即可通信。
- 避免了一些不需要暴露出来的端口。
  例如对于一个 Web 服务，有三个容器，分别是 `app` 、`restapi` 和 `pgsql` 。其中 `app` 容器对外暴露 `8080` 端口，可以将该端口映射到宿主机，然后通过宿主机的 `9090` 访问前端页面。对于 `app` 访问后端 `restapi` 容器，只需要通过内部主机名和端口就可以了。而后端容器 `restapi` 在访问 `pgsql` 时，也是以同样的方式。外部其实无法访问到服务的后端容器，甚至不知道后端数据库的内容。既减少了端口占用，又能保证后端和数据库服务的安全性。

## 如何初始化网络

通常创建三个网络：

- app 子网：应用相关容器所在子网。
- db 子网：存储数据相关容器所在子网
- other 子网：不符合上面的两种情况下，放在该子网。

创建网络:
docker network create app
docker network create db
docker network create other

查看网络:
docker network ls
