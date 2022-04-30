# Docker containers

Docker containers 为应用运行提供了一个隔离的环境。

## 运行 containers

通过运行 Docker image 指定的环境，可以创建 Docker containers。使用 `docker run` 命令可以启动 Docker containers。
```bash
$ docker run my-app:1.0
```

上面的命令会使用 `my-app:1.0` image 创建一个 container。执行 `docker run` 会启动 container，并且执行 `Dockerfile` 文件中的 `CMD` 字段指示的指令。

为了验证 docker container 是否正在运行，可以执行 `docker ps` 来显示正在运行的 docker containers。
```bash
$ docker ps
CONTAINER ID        IMAGE                  COMMAND                  CREATED             STATUS              PORTS                               NAMES
ec488b832e0a        my-app:1.0             "docker-entrypoint.s…"   13 minutes ago      Up 13 minutes                                           practical_buck
```

上面的结果表示当前正在运行的 container 只有一个，container ID 为 `ec488b832e0a`，名称为 `practical_buck`，container ID 和 名称都是随机生成的，并且可以被其他 Docker CLI 命令使用。你也可以使用 `--name` 选项，为 container 提供一个指定的名称。
```bash
$ docker run --name my-app my-app:1.0
```

```bash
$ docker ps
CONTAINER ID        IMAGE                  COMMAND                  CREATED             STATUS              PORTS                               NAMES
6cf7fd48703e        my-app:1.0             "docker-entrypoint.s…"   13 minutes ago      Up 13 minutes                                           my-app
```

> [`docker run`](https://docs.docker.com/engine/reference/commandline/run/) 命令
> [`docker ps`](https://docs.docker.com/engine/reference/commandline/ps/) 命令

## 分离模式和 log 信息

默认情况下，执行 `docker run` 命令，containers 进程将会绑定到输入命令的控制台中，用于打印 log 信息。这意味着所有的 log 信息，都会实时的打印在控制台中。然后，我们可以使用 `-d` 选项，以分离模式运行 container，这样可以在 container 运行时释放控制台，用于执行更多的命令。
```bash
$ docker run -d my-app:1.0 # 以分离模式运行 docker container
```

可以使用 `docker log` 命令查看 container 运行时输出的 log 信息：
```bash
$ docker logs ec488b832e0a
```

`ec488b832e0a` 是 container 的 id，同样也可以使用 conatiner 的名称查看 log 信息：
```bash
$ docker logs my-app
```

> [docker logs](https://docs.docker.com/engine/reference/commandline/logs/) 命令

## 暴露端口

如果运行的 containers 暴露了一个端口（e.g. port 3000), 那么就需要一个 Docker 主机和 Docker container 之间的端口映射来访问 Docker 之外的应用程序（e.g 在浏览器中输入 [http://localhost:3000](http://localhost:3000) 查看 Docker 中 web 应用的运行)。

```javascript
docker run -p 3000:3000 my-app:1.0
```

上面的命令会运行一个 container，并且你可以从 [http://localhost:3000](http://localhost:3000) 访问这个应用。

通常 Docker host 和 Docker container 之间的端口号是相同的，但是你也可以使用不同的端口号映射。
```bash
$ docker run -p 8000:3000 my-app:1.0
```

执行上面的命令后，你可以访问 [http://localhost:8000](http://localhost:8000) 查看 app。

## 停止和删除 containers

使用 `docker stop` 或者 `docker container stop` 命令停止运行 container。
```bash
$ docker stop my-app
```

`docker stop` 将会在 container 中发送一个 `SIGTERM` 信号，渐进的终止正在运行的进程。如果在 10 秒后，container 任然没有被终止，会继续发送一个 `SIGKILL` 信号，表示会强制终止运行的进程。

也可以通过 `docker kill` 或者 `docker container kill` 命令，立即发送 `SIGKILL` 信号给 container，立即终止 container 进程。

```bash
$ docker kill my-app
```

可以使用 `docker start` 或者 `docker container start` 命令来重新启动 container。
```bash
$ docker start my-app
```

使用 `docker rm` 删除不再使用的 container。
```bash
$ docker rm my-app
```

默认情况下，`docker rm` 命令只能删除停止运行的 containers，想要删除正在运行的 containers，可以使用 `-f`(`--force`) 选项。
```bash
$ docker rm -f my-app
```

使用 `-f` 选项，会发送一个 `SIGKILL` 信号给正在运行的 container，可以终止 container 的运行，然后删除。

> [docker stop](https://docs.docker.com/engine/reference/commandline/stop/) 命令
>
> [docker kill](https://docs.docker.com/engine/reference/commandline/kill/) 命令
>
> [docker start](https://docs.docker.com/engine/reference/commandline/start/) 命令
>
> [docker rm](https://docs.docker.com/engine/reference/commandline/rm/) 命令

## 显示 containers 列表

使用 `docker ps` 或者 `docker container ls` 命令显示 containers 列表。默认情况下，`docker ps` 命令只会显示正在运行的 containers。为了显示终止的 containers，需要使用 `-a`（`--all`）选项。
```bash
$ docker ps
$ docker ps -a  # includes stopped containers
```

> [`docker ps`](https://docs.docker.com/engine/reference/commandline/ps/) 命令

## 在运行的 container 中执行命令

使用 `docker exec` 命令，可以在运行的 conainer 中执行命令。
```bash
$ docker exec my-app ls
Dockerfile
README.md
node_modules
package.json
pages
public
styles
yarn.lock
```

每次在 container 中执行命令，都需要使用 `docker exec` 比较麻烦，所以你可以使用下面的命令绑定 container 的 shell 至当前命令行，方便你输入更多的命令。
```bash
$ docker exec -it my-app sh
```

绑定命令行时，`-it` 选项是必须的，你可以阅读[这篇文章](https://devconnected.com/docker-exec-command-with-examples/)获取有关 `-it` 更多的信息。

> 注意：bash != sh
> 在上面的命令中，我们使用 `sh` 而不是 `bash`，因为 Docker image 可能没有安装 `bash`。例如，Alpine Linux Docker images 在默认情况下就没有安装 `bash`，所以需要在 `Dockerfile` 中指定 `bash` 安装，或者使用 `sh`,`sh` 是默认安装在 Alpine Linux 上的。几乎所有的 Linux 发行版都安装了 `sh`，但是 很有可能没有安装 `bash`。`bash` 是 `sh` 的超集，就像 typescript 是 javascript 的超集一样，这意味着 `bash` 包含了一些 `sh` 没有的功能。
> 更多的细节可以阅读[stack overflow 上的这个答案](https://stackoverflow.com/questions/5725296/difference-between-sh-and-bash)，它解释了“sh 和 bash 之间的区别”。

`docker run` 命令也可以用来在 docker container 内部执行命令。`docker run` 和 `docker exec` 之间的区别是，`docker exec` 命令需要在一个已经运行的 container 中执行，而 `docker run` 命令则会创建一个新的 container 来执行指定的命令。

```bash
$ docker run my-app:1.0 ls
```

上面的命令会为 `my-app:1.0` 启动一个新的容器，并且执行 `ls` 命令。使用 `docker ps --all` 会显示刚刚创建的新的容器。

记住 `docker run` 命令也被用来创建 docker container 并且运行容器关联映像中指定的任何命令，这也是为什么 `docker run` 命令是最常用的命令之一，同时也可以用它创建一个新的 container 来执行指定的命令。

> ["Docker Exec Command With Examples"](https://devconnected.com/docker-exec-command-with-examples/)
>
> [docker exec](https://docs.docker.com/engine/reference/commandline/exec/) 命令
>
> [docker run](https://docs.docker.com/engine/reference/commandline/run/) 命令

## 获取 container 的详细信息

使用 `docker inspect` 命令获取 docker container 的详细信息。
```bash
$ docker inspect my-app
```

打印信息会比较多，包含了 docker container 的所有信息，例如它的 id、何时被创建的、启动时执行的命令、网络设置等等。

`docker inspect` 也可以用来查看其他对象的信息，例如 docker images。
```bash
$ docker inspect my-app:1.0 # inspect a Docker image
```

可以使用 `docker stats` 获取容器的资源消耗信息，录入 cpu、内存和 I/O。
```bash
$ docker stats my-app
CONTAINER ID        NAME                CPU %               MEM USAGE / LIMIT     MEM %               NET I/O             BLOCK I/O           PIDS
4eef7af61c6f        my-app              0.03%               46.73MiB / 1.944GiB   2.35%               906B / 0B           0B / 0B             18
```

> [`docker inspect`](https://docs.docker.com/engine/reference/commandline/inspect/) 命令
>
> [`docker stats`](https://docs.docker.com/engine/reference/commandline/stats/) 命令
