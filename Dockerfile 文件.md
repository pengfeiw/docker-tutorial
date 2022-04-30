# Dockerfile

**Dockerfile** 是一个包含了如何创建一个 image 的指示的文件。文件开头通常指定用于创建这个 image 所使用的 **base image**。例如，想要创建一个基于 python 的 API，可以使用安装了 python 的 Linux 系统组成的 base image。指定了 base image 后，其他指令需要指定以下信息：

- container 需要设置的环境变量
- image 暴露的端口
- 需要被拷贝至 image 的文件
- 需要安装的依赖
- 运行 container 需要的命令（e.g `yarn start`）
- 更多其他信息...

下面是一个 Node.js API 的 image 的 Dockerfile：

```dockerfile
# Base image that has Node version 12.16.1 installed on a Linux OS.
# This is an alpine Linux image that is smaller than the non-alpine Linux equivalent images.
FROM node:12.16.1-alpine3.11

# Installs some dependencies required for the Node.js API
RUN apk add --no-cache make g++

# Indicates that the API exposes port 3000
EXPOSE 3000

# Specifies the working directory. All the paths referenced after this point will be relative to this directory.
WORKDIR /usr/src/app

# Copy all local source files into the Docker container's working directory
COPY . .

# Installs NPM dependencies using yarn
RUN yarn install

# Command to start the API which will get executed when a Docker container using this image is started.
CMD [ "yarn", "start" ]
```

下面表格列出了一些经常用的的指令。完整的指令可以在 [Docker reference](https://docs.docker.com/engine/reference/builder/) 文档中找到。

| Instruction | Description |
| ----------- | ----------- |
| FROM      | 定义一个 base image |
| RUN   | 需要执行的命令 |
| CMD | 运行 container 需要执行的命令|
| EXPOSE | 指定暴露的端口信息 |
| ENV | 设定环境变量 |
| COPY | 拷贝文件或者目录至 image |
| ADD | COPY 指令的高级版本，COPY 比 ADD 命令更好 |
| ENTRYPOINT | 定义容器的可执行文件，查看与 CMD 之间的[区别](https://phoenixnap.com/kb/docker-cmd-vs-entrypoint)|
| VOLUME | 指定 image 中的一个目录存储 volume。volume 将会被给予一个随机的名称，可以用 `docker inspect` 命令查看 |
| WORKDIR | 为 Dockerfile 中后续的指令指定工作目录 |
| ARG | 定义可以传递给 `docker build --build-arg` 命令的变量，并且可以用在 Dockerfile 文件中 |

为了防止某些文件被拷贝到 Docker image 中，可以在 `Dockerfile` 同级目录下创建一个 `.dockerignore` 文件，文件内容指定了那些不允许拷贝到 Docker image 中的文件，类似于 `.gitignore`。使用 `.dockerignore` 文件之前，[确保你清楚它的正确语法](https://docs.docker.com/engine/reference/builder/#dockerignore-file)。
