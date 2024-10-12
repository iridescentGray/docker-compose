# 其他

## Docker Compose 文件官方文档

https://docs.docker.com/compose/compose-file/

## Docker Compose 文件配置详解

### tty

用法:

    services:
      python_base:
        image: python:3.11.9-slim-bookworm
        command: /bin/bash
        tty: true

- 含义: configures service container to run with a TTY.
- 作用:保持容器运行
- tty=true 相当于 docker run -t
- 通俗解释： 将用户的“终端”与 stdin 和 stdout 流连接起来,产生能够输入输出的效果

### environment

- 添加环境变量。您可以使用数组或字典。任何布尔值（true、false、yes、no）
- 需要用引号括起来，以确保它们不会被 YML 解析器转换为 True 或 False

### depends_on

表示服务之间的依赖关系。服务依赖会导致以下行为：

- docker-compose up 按依赖顺序启动服务。在下面的例子中，db 和 redis 在 web 之前启动。
- docker-compose stop 按依赖顺序停止服务。在以下示例中，web 在 db 和 redis 之前停止
-     version: "3.9"
      services:
        web:
          build: .
          depends_on:
            - db
            - redis
        redis:
          image: redis
        db:
          image: postgres

### networks

#### name

为此网络设置自定义名称

    networks:
      network1:
        name: my-app-net

#### driver

指定该网络应使用哪个驱动程序。

- 默认使用 bridge 单个主机上的网络
- overlay 代表跨多个节点的网络群

#### driver_opts

指定为键值对以传递给此网络的驱动程序

#### enable_ipv6

在此网络上启用 IPv6 网络。

#### labels

添加元数据

#### external

如果设置为 true，则指定此网络是在 Compose 之外创建的。docker-compose up 不会尝试创建它，如果它不存在，则会引发错误

    networks:
      outside:
        external: true

#### attachable

仅在 driver 设置为 overlay 时可用。
如果设置为 true，那么除了服务之外，独立容器也可以连接到此网 络。

    networks:
      mynet1:
        driver: overlay
        attachable: true

#### internal

默认情况下，Docker 会将桥接网络连接到它提供外部连接。
如果要创建外部隔离的覆盖网络，可以将此选项设置为 true

    networks:
      outside:
        internal: true

#### ipam（IP 地址管理）

自定义 ip 地址

    services:
      app:
        image: nginx:alpine
        networks:
          app_net:
            ipv4_address: 172.16.238.10
            ipv6_address: 2001:3984:3989::10

    networks:
      app_net:
        ipam:
          driver: default
          config:
            - subnet: "172.16.238.0/24"
            - subnet: "2001:3984:3989::/64"

### extra_hosts

- 添加主机名映射，会往/etc/hosts 文件中添加一些记录，启动之后查看,容器内部 hosts 可以看到
- 使用与 docker 客户端--add-host 类似

样例如下

    extra_hosts:
      - "somehost:162.242.195.82"
      - "otherhost:50.31.209.229"

相当于

    162.242.195.82 somehost
    50.31.209.229 otherhost

### restart

- no: 默认，在任何情况下都不会重启容器。
- always: 容器总是重新启动
- on-failure: 如果退出代码指示失败错误，则该策略会重新启动容器。
- unless-stopped: 总是重新启动容器，除非容器停止（手动或其他方式）。

### security_opt

为每个容器覆盖默认的标签。简单说来就是管理全部服务的标签

例子:设置全部服务的 user 标签值为 USER

    security_opt:
      - label:user:USER
      - label:role:ROLE

### tmpfs

在容器内挂载一个临时文件系统。可以是单个值或列表

    tmpfs: /run
    tmpfs:
      - /run
      - /tmp

### ulimits

- 当前进程以及其子进程的资源使用量，覆盖容器的默认限制
- 可以单一地将限制值设为一个整数，也可以将 soft/hard 限制指定为映射

      ulimits:
        nproc: 65535
        nofile:
          soft: 20000
          hard: 40000

### volumes

- 挂载一个目录或者一个已存在的数据卷容器
- 格式 HOST:CONTAINER 或 HOST:CONTAINER:ro (数据卷只读)

### cap_add cap_drop:
- 添加/删除容器的内核功能
- https://docs.docker.com/reference/compose-file/services/#cap_add
- 所有内核功能: https://man7.org/linux/man-pages/man7/capabilities.7.html



### sysctls 
- 容器内核参数
- https://docs.docker.com/reference/compose-file/services/#sysctls


