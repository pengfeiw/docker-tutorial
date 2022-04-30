# 介绍

介绍 docker、虚拟机以及 Docker engine 的概念。

## Docker 是什么？

**Docker** 是一个为软件运行提供独立的环境的工具。

我们经常需要**在不同机器或者系统中，为应用提供一个独立的运行环境**。例如我们在本地环境开发软件时，并且希望软件同样可以在部署环境中正常运行。开发者也希望应用可以在任何电脑上能够快速正常运行，而不用考虑电脑是如何配置的，这样可以极大的方便开发团队的协作。

要确保一个应用正常运行，通常在运行之前要确保这个应用的相关依赖必须被安装，而且所有依赖的版本必须正确，所以确保在不同机器中都可以正常运行一个应用，通常是比较困难的。例如，你尝试运行一个 Node.js 的 API，并且这个 API 在安装了 Node.js 12.8 的电脑上通过了测试，它或许会在安装了 Node.js 10.18 的机器上运行失败。Docker 的作用就是应用运行提供一个稳定的环境，这个环境安装了应用正确运行所需要的全部正确版本的依赖，使应用可以在不同机器上正确运行。

Docker 能够创建独立运行环境的四个重要对象：**images、containers、volumes 和 networks**。其中 images 和 containers 是用户经常会打交道的两个东西。

![Docker 的概念](https://robertcooper.me/posts/docker-guide/docker-objects.jpg)

**Docker images** 包含应用运行所需要的代码。**Docker containers** 为 Docker images 中的代码运行提供一个独立隔离的运行环境。一个 Docker images 可以看做被 Docker container 使用的蓝图和代码。Docker images 可以被保存到 **image registries**，以供其他人下载 images。最著名的 Docker image registry 是 **Docker Hub**。你可以认为 image registries 就是 Docker images 的 NPM。

**Docker volumes** 用来存储 Docker containers 运行时产生的持久化数据。通过将数据存储为一个 Docker volumes，即使删除、重建或者重新启动 Docker container，应用也可以拥有同样的数据。不同的 Docker containers 也可以指向不同的 volume，所以不同的 Docker containers 可以使用同样的数据。

**Docker networks** 可以确保不同 containers 之间独立的环境，也允许它们之间可以互相通信。

## 虚拟机（virtual machines）是什么？

**Virtual machines（vms）** 经常会与 Docker 在一起做比较，因为它们都是为了提供一个隔离的环境。

使用 Docker，应用可以在 containers 中运行，并且所有 containers 共享的是机器上的同一个操作系统内核。另一方面，在 vms 中运行的应用，运行在虚拟机自己的操作系统中，并且不共享内核。 虚拟机在管理程序的帮助下运行，管理程序负责管理运行哪个操作系统。

与虚拟机相比，Docker container 可以在几秒内启动，它更轻量级，易于配置，消耗资源更少。唯一需要使用虚拟机的原因，可能是需要一个更高级的独立运行环境，担心 Docker container 在主机操作系统上共享内核，会带来安全漏洞。

## Docker engine

使用 Docker 前，必须安装 **Docker engine**。启动引擎，意味着一个长时间运行的后台进程（与 `dockerd` 命令相对应的 docker 守护进程）被启动，**Docker engine** 是用来创建一个可以通过 REST API 交互的服务。Docker CLI 是用户使用 REST API 管理 Docker 对象的最常用的方式，同样还有一些第三方的应用或者 Docker engine SDKS 也可以使用 REST API 管理 Docker 对象。
