# Docker Compose

**Docker Compose** 是简化与 Dockers engine 通信的工具。目前我们与 Docker engine 的通信都是通过 Docker CLI 方式，但是 Docker Compose 可以让我们在 `compose.yml` 文件中定义容器，这样我们可以将一些配置转移到 docker compose 文件中。

在使用 `docker-compose` 之前，需要在 `docker-compose.yml` 文件中指定 **services（服务）**，service 描述了 container 运行所需要的 image 和其他与 container 相关的配置，例如环境变量、暴露的端口、使用的 volumes 等等。

> **为什么使用 “service” 这个词语？**
> 
> service 指的是 `compose.yml` 文件中定义的用来描述 Docker containers 的文本片段。名词 “service” 经常用来表示组成一个大的应用的某个部分。整个[微服务体系结构](https://medium.com/hashmapinc/the-what-why-and-how-of-a-microservices-architecture-4179579423a9)都基于此思想。

这里有一个 `docker-compose.yml` 文件的示例，他描述了三个 services：一个 web app，一个 API 和一个数据库（database）。
```yml
version: "3.8"
services:
  web-app:
    container_name: web-app
    build: ./web-app
    ports:
      - "3000:80"
  api:
    container_name: api
    build: ./api
    ports:
      - "5000:5000"
  db:
    container_name: db
    image: postgres
    ports:
      - "5432:5432"
    environment:
      POSTGRES_PASSWORD: password
    volumes:
      - db-volume:/var/lib/postgresql/data
volumes:
  db-volume:
```

以此从上至下分析这个文件，首先指定了 Docker Compose 的版本为 `3.8`。

下面又描述了三个 services：`web-app`,`api` 和 `db`。每一个 service 都定义了一个 `container_name`，它指定了 Docker Compose 运行 service 时关联的 container。使用 `docker-compose up` 命令可以启动一个 service。如果指定了 service 的名字，它将会启动这个 service。如果没有提供 service 的名字，那么所有的 service 都将会被启动。

```shell
# 启动 api service
$ docker-compose up api

# 启动所有 services
$ docker-compose up
```

services 也可以在分离模式（detached mode）下运行。
```shell
$ docker-compose up api -d
```

> 在[这里](https://pengfeixc.com/tutorial/docker/docker-containers)查看分离模式相关信息。

`web-app` 和 `api` services 都有一个 `build` 选项，指定了每个 service 的 `Dockerfile` 文件所在的位置。有了 `Dockerfile`，Docker Compose 知道如何通过这些 services 去构建 images。

使用 `docker-compose build` 命令构建 images。
```shell
$ docker-cmopose build api
```

上面的命令会为 `api` service 创建一个带有默认名称的 image，默认名称格式为 `[folder_name]_[service_name]`，如果 `docker-compose.yml` 所在的目录名称为 `my-app`，所以创建的 image 默认名称为 `my-app_api`，这个 image 也会被给予一个默认的 tag `latest`。

大多数情况不需要使用 `docker-compose build` 命令去创建 images，因为 `docker-compose up` 命令会自动创建任何缺少的 images。只有在希望重新创建一个 image 时，才会使用 `docker-compose build` 命令，例如被 tag 为 `latest` 的 image 是旧的，现在需要更新一些代码，然后重新创建 image。

你可能也注意到了 `docker-compose.yml` 文件中的 `db` service 没有 `build` 字段，这是因为数据库 service 使用的是 Docker Hub 官方的 PostgreSQL image，所以不需要构建一个 image。

通过 `ports` 字段指定想要在 Docker 主机上暴露的端口，格式为 `[first]:[second]`，`first` 表示 Docker 主机上的端口，`second` 表示 Docker container 上的端口，`8000:3000` 代表我们将 Docker container 中的 3000 映射到 Docker 主机上的 8000，所以我们可以通过 8000 端口访问运行的 container 中的应用。

另一个值得注意的是，`db` service 中定义了一个环境变量：`POSTGRES_PASSWORD`，值是一个密码（这种将密码设置为环境变量的行为是一种不安全的做法，实际开发中不要使用）。启动 service 之后，关联的 container 可以在运行的时候访问 service 中定义的环境变量，但是无法在 Dockerfile 中访问环境变量，所以环境变量只在运行阶段有效，在构建阶段无效。当然也有另一种可以在 Dockerfile 中访问的变量，通过 `args` 字段指定，`args` 指定的变量，可以在构建阶段被访问。

`db` service 也使用了一个 volume。在文件底部的 `volumes` 字段中指定命名的 volumes。在 `db` service 也有一个 `volumes` 字段，指定了 `db` service 使用的 volumes，将 `db-volume` volume 映射到 conainer 目录下的 `/var/lib/postgresql/data`，当 `db` 重启时，可以将数据库数据存储在 volumes 中。

下面列出一些常用的 `docker-compose.yml` 文件中的字段。

| Option | Description |
| ------ | ----------- |
| build  | 指定如何创建一个 image |
| build.args | 指定构建阶段使用的变量 |
| command | 覆盖 `Dockerfile` 中定义的默认 CMD |
| container_name | service 的 container 使用的名称 |
| depends_on | 定义 container 启动的顺序 |
| env_file | 从一个文件中添加环境变量 |
| environment | 定义环境变量 |
| image | 指定 container 使用的 image |
| networks | 指定 container 使用的 networks |
| ports | 指定 container 的端口映射 |
| volumes | 声明命名的 volume |

可以在[官方文档](https://docs.docker.com/compose/compose-file/)中查看所有的 Docker Compose 选项。

下面列了一些经常使用的 `docker-compose` CLI 命令：

| Command | Description |
| ------- | ----------- |
| docker-compose build |  创建或者重新创建 service 的 image |
| docker-compose up | 创建或者启动 service 的 container |
| docker-compose down | 终止或者移除 service 关联的 containers, images 和 networks |
| docker-compose exec | 在 container 中执行一个命令 |
| docker-compose images | 显示与 service 关联的 images |
| docker-compose kill | 杀掉 conainer |
| docker-compose logs | 查看 service 的 log 信息 |
| docker-compose ps | 显示所有 service containers 和它们的状态 |
| docker-compose pull | 为 service 拉取 image |
| docker-compose push | 为 service 推送 image |
| docker-compose rm |  移除所有终止的 containers |
| docker-compose run | 在 service 中执行单个命令 |
| docker-compose start | 启动一个 service |
| docker-compose stop | 终止一个 service |

可以使用 `docker-compose help` 命令查看所有 `docker-compose` 命令。

> [Command-line completion](https://docs.docker.com/compose/completion/)
>
> [Docker Compose file reference](https://docs.docker.com/compose/compose-file/)
>
> [Docker Compose CLI reference](https://docs.docker.com/compose/reference/)
