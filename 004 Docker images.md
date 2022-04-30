# Docker images

Docker images 包含了 Docker containers 可以使用的代码和指令，Docker containers 从 Docker images 中获取如何设置应用运行环境的相关信息。

## 创建和标记 images

使用 `docker build` 命令创建 Docker images。在创建 Docker images 时，需要通过 `--tag` 选项指定 images 的 tag。tag 表示 images 的名称和版本信息，这样 containers 才能知道它需要运行的 images。

这里有一个创建并标记 images 的例子：
```bash
$ docker build --tag my-app:1.0 .
``` 

拆分这个命令：
- `docker build` 指定正在创建 Docker image
- `--tag my-app:1.0` 指定正在创建的 image 的仓库名称为 `my-app`，版本为 `1.0`
- `.` 表示从当前目录下的 `Dockerfile` 文件创建 Docker image

可以使用 `docker images` 命令查看所有 Docker images:

```bash
$ docker images
REPOSITORY             TAG                 IMAGE ID            CREATED             SIZE
my-app                 1.0                 964baf4833d3        8 minutes ago       322MB
```

你也可以仅指定名称而不指定版本，这样会使用默认的 `latest` tag。
```bash
$ docker build --tag my-app .
$ docker images
REPOSITORY             TAG                 IMAGE ID            CREATED             SIZE
my-app                 latest              964baf4833d3        8 minutes ago       322MB
```

你还可以使用 `docker tag` 命令来标记 images。因为同一个 image 可以拥有多个标记，`docker tag` 命令允许给一个已经被标记了的 image 添加一个新的 tag。

例如：
```bash
$ docker tag my-app:1.0 robertcooper/my-app:1.0
$ docker images
REPOSITORY             TAG                 IMAGE ID            CREATED             SIZE
robertcooper/my-app    1.0                 964baf4833d3        46 minutes ago      322MB
my-app                 1.0                 964baf4833d3        46 minutes ago      322MB
```

上面的命令，给 `my-app:1.0` 提供了一个新的 tag `robertcooper/my-app:1.0`。另外需要注意的是，两个 images 共享的 IMAGE ID 相同，它们仅 REPOSITORY 名称不同。当运行 `docker push` 命令时，与 images 相关联的 REPOSITORY 名称决定了哪个 images 将会被 push。

> ["A quick introduction to Docker tags"](https://www.freecodecamp.org/news/an-introduction-to-docker-tags-9b5395636c2a/) by Shubheksha Jalan
> Docker 文档中的 [`docker build`](https://docs.docker.com/engine/reference/commandline/build/) 命令
> Docker 文档中的 [`docker tag`](https://docs.docker.com/engine/reference/commandline/tag/) 命令

## 显示 images 列表

使用 `docker images` 或者 `docker image ls` 命令展示 Docker host 上所有的 images。

```bash
$ docker images
REPOSITORY             TAG                 IMAGE ID            CREATED             SIZE
robertcooper/my-app    1.0                 964baf4833d3        46 minutes ago      322MB
my-app                 1.0                 964baf4833d3        46 minutes ago      322MB
```

> Docker 文档中的 [`docker images`](https://docs.docker.com/engine/reference/commandline/images/) 命令

## Image registries 和 pulling/pushing images

Docker images 存储在远程 Docker **image registries**，默认的 Docker image registry 是 [**Docker Hub**](https://hub.docker.com/)。Docker image registries 提供 Docker images 的存储和下载功能，你可以从 Docker image registries 上下载 images，然后在 Docker container 中运行。

> Docker image registry 的作用和 github 类似，你可以在 github 上下载和上传代码。

使用 `docker pull` 命令，从 Docker Hub 上拉取（pull）image：
```bash
docker pull nginx:1.18.0
```

上面的命令会从 Docker Hub 上下载标记为 `1.18.0` 的 `nginx` Docker image。 执行 `docker pull` 命令后，被下载的 image 将会显示在 `docker images` 的列表中。

```bash
$ docker images
REPOSITORY             TAG                 IMAGE ID            CREATED             SIZE
nginx                  1.18.0              20ade3ba43bf        6 days ago          133MB
```

如果在下载 image 时没有指定 tag 版本，那么将会下载标记为 `latest` 的 image。
```bash
$ docker pull nginx
$ docker images
REPOSITORY             TAG                 IMAGE ID            CREATED             SIZE
nginx                  latest              992e3b7be046        6 days ago          133MB
```

上面例子使用的 [nginx image](https://hub.docker.com/_/nginx) 是一个官方的 Docker Hub image。官方 images 是一些被 Docker Hub 维护的 images，它们都经过了测试，并且是安全的。你可以在[这里](https://docs.docker.com/docker-hub/official_images/)阅读更多官方 Docker images 的信息。

任何人可以在 Docker Hub 上创建账号和 repository（仓库），然后 push （上传/推送）images 至仓库。push images 至 Docker Hub 意味着，images 将会存储到 Docker Hub 上的仓库，仓库名由 `docker push` 命令指定。`docker push` 命令格式如下：
```bash
$ docker push <hub-user>/<repo-name>:<tag>
```

一个简单的例子：
```bash
$ docker push robertcooper/my-app:1.0
```

上面的例子，将本地仓库名称为 `robertcooper/my-app` 并且标记为 `1.0` 的 image push 到 Docker Hub 上。

为了获得 push image 的权限，首先需要使用 `docker login` 命令进行登录：
```bash
$ docker login --username robertcooper
```

执行上面的命令，会提示你输入用户密码，如果成功了，那么你可以将本地 images 推送到 Docker Hub 的 `robertcooper` 账户。如果你需要下载私有的仓库，那么你也需要使用 `docker login` 登录账号。

Docker Hub 并不是唯一的 image registry，同样也有很多其他的 images registry：

- [Amazon ECR](https://aws.amazon.com/cn/ecr/)
- [Google Cloud Container Registry](https://cloud.google.com/container-registry)
- [Azure Container Registry](https://azure.microsoft.com/en-us/services/container-registry/)
- [GitHub Container Registry](https://docs.github.com/en/packages/working-with-a-github-packages-registry/working-with-the-container-registry)

每个 docker registry 的价格不一样。如果使用 Docker Hub 的免费账户，你可以在 Docker Hub 上上传无数的公共仓库和一个私有仓库，如果需要更多的私有仓库，那么你可以选择充钱支持他们。如果 push/pull 非 Docker Hub 的仓库，那么需要指定 hostname，`docker push` 和 `docker pull` 命令在没有指定 hostname 的情况下，默认使用 Docker Hub 作为 image registry。例如要拉取 Amazon ECR 上的 image，命令如下：
```bash
$ docker pull 183746294028.dkr.ecr.us-east-1.amazonaws.com/robertcooper/my-app:1.0
```

上面的例子中，`183746294028.dkr.ecr.us-east-1.amazonaws.com` 是 image 仓库的 hostname，`robertcooper/my-app` 是仓库名称，`1.0` 是 tag。

记住，无论 push/pull 的 registry 是哪个，都需要使用 `docker login` 进行登录。

> Docker 文档中的 ["Docker Hub Quickstart"](https://docs.docker.com/docker-hub/)，讲解了如何在 Docker Hub 上创建 image repository
> [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) 命令
> [`docker push`](https://docs.docker.com/engine/reference/commandline/push/) 命令
> [`docker login`](https://docs.docker.com/engine/reference/commandline/login/) 命令

## 删除 images

使用 `docker rmi` 或者 `docker image rm` 命令删除 docker image。
```bash
$ docker rmi my-app:1.0
```

在删除已经被 containers 使用的 images 之前，需要先删除 containers。或者使用 `--force` 选项强制删除。
```bash
docker rmi --force my-app:1.0
```

这里有两个很有用的命令，可以一次性清除所有 images。

```bash
$ docker rmi $(docker images -a -q)  # remove all images
$ docker rmi $(docker images -a -q) -f  # same as above, but forces the images associated with running containers to also be removed
```

> [`Docker rim`](https://docs.docker.com/engine/reference/commandline/rmi/) 命令

## 保存和加载 images

我们可以将 Docker image 保存到一个文件，然后重新加载到另一个 Docker host。我们通常需要这种功能，例如，一个 CI check 创建了一个 Docker image，在另一个 CI check 中需要使用相同的 image。我们除了将 image 上传到仓库，然后在另一个 CI check 中下载下来，还可以将 image 保存成文件，然后在另一个 CI check 中加载这个 image 文件。

使用 `docker save` 命令，保存 Docker image：
```bash
$ docker save --output my-app.tar my-app:1.0
```

上面的命令将会把 `my-app:1.0` 的 image 保存成 `my-app.tar` 的 [tarball](https://en.wikipedia.org/wiki/Tar_(computing)) 文件。

然后可以使用 `docker load` 命令加载这个文件到 Docker host 中。
```bash
$ docker load --input my-app.tar
Loaded image: my-app:1.0
```

> [`docker save`](https://docs.docker.com/engine/reference/commandline/save/) 命令
>
> [`docker load`](https://docs.docker.com/engine/reference/commandline/load/) 命令
