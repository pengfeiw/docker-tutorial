# Docker volumes

Docker volumes 的作用是存储 Docker containers 的数据。因此 contaier 可以被随时终止和重启，并且应用的数据不会丢失。多个 container 也可以通过指向同一个 volume 来共享数据。

Docker volumes 保存在服务器的 Docker 存储目录下。Linux 系统中，Docker 存储目录为 `/var/lib/docker/volumes`，通常用户并不需要去关心 Docker 存储目录的位置，因为可以通过 CLI 与 volumes 沟通。

通过 `docker run` 命令的 `-v`（`--volume`）选项将 volume 挂载到一个 container 上。

```bash
$ docker run -d --name my-app -v my-volume:/usr/src/app my-app:1.0
```

上面的命令将一个名为 `my-volume` 的卷映射到 Docker container 中的 `/usr/src/app` 目录下的代码。

使用 `docker volume ls` 打印所有 Docker volumes：
```bash
$ docker volume ls
DRIVER    VOLUME NAME
local     my-volume  
```

也可以使用 `docker volume create` 命令，创建 volumes。
```bash
$ docker volume create my-volume
```

一般很少使用 `docker volume create` 命令，因为大多数都是通过 `docker run -v` 命令或者 Docker Compose 创建。

如果创建 volume 时没有传递名称，Docker 会自动生成一个随机的名称。
```bash
$ docker run -d --name my-app -v /usr/src/app my-app:1.0
```

可以通过 `docker inspect` 命令获得 container 的详细信息，来查看 volume 的名称。
```bash
$ docker inspect my-app
```

`docker inspect` 命令的输出结果会包含 "Mounts" 字段信息，里面包含了 volume 的相关信息。
```bash
...
        "Mounts": [
            {
                "Type": "volume",
                "Name": "my-volume",
                "Source": "/var/lib/docker/volumes/my-volume/_data",
                "Destination": "/usr/src/app",
                "Driver": "local",
                "Mode": "z",
                "RW": true,
                "Propagation": ""
            }
        ],
...
```

Docker 还提供了 **bind mounts** 选项，bind mounts 与 Docker volumes 十分类似，bind mount 是一个将会挂载到 Docker container 上的主机上的目录。bind mount 与 Docker volume 的主要区别是，Docker engine 可以通过 `docker volume` 命令管理 docker volume，并且 docker volume 都被保存到 Docker 的存储目录中。

例如，用户桌面上的一个目录可以被当作 container 的 bind mount。
```bash
$ docker run -d --name my-app -v ~/Desktop/my-app:/usr/src/app my-app:1.0
```

当输入 `docker volume ls` 命令时，bind mounts 也不会显示在打印结果里，因为 bind mounts 并不归 Docker 管理。

> [Manage data in Docker](https://docs.docker.com/storage/)
>
> [Use bind mounts](https://robertcooper.me/post/docker-guide#docker-volumes)
>
> [YouTube: "Docker Volumes explained in 6 minutes"](https://www.youtube.com/watch?v=p2PH_YPCsis&ab_channel=TechWorldwithNana)
>
> [YouTube: "What is Docker Volume | How to create Volumes | What is Bind Mount | Docker Storage"](https://www.youtube.com/watch?v=VOK06Q4QqvE&ab_channel=AutomationStepbyStep-RaghavPal)
