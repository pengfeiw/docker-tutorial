# Docker networks

Docker networks 在 containers 之间的隔离和通信起到了关键的作用。

Docker networks 为不同的网络提供不同的驱动（drivers）。默认的驱动是 "bridge" 驱动。当启动一个 container 时，没有指定具体的 network，那么这个 container 将会在名叫 "bridge" 的 bridge network 上运行。然而，如果不同的 containers 之间需要通信，那么 Docker 建议使用一个用户定义的 bridge network，而不是采用默认的。

```bash
$ docker run -d --name my-app my-app:1.0
```

通过上面的命令运行一个 container，将会采用默认的 bridge network。你可以使用 `docker inspect` 查看。
```bash
...
        "NetworkSettings": {
            "Bridge": "",
            "SandboxID": "9b092e2301eec5d7fa31c85cdd6a617ed83fcce062c9e644bad2b66cd26d4461",
            "HairpinMode": false,
            "LinkLocalIPv6Address": "",
            "LinkLocalIPv6PrefixLen": 0,
            "Ports": {},
            "SandboxKey": "/var/run/docker/netns/9b092e2301ee",
            "SecondaryIPAddresses": null,
            "SecondaryIPv6Addresses": null,
            "EndpointID": "",
            "Gateway": "",
            "GlobalIPv6Address": "",
            "GlobalIPv6PrefixLen": 0,
            "IPAddress": "",
            "IPPrefixLen": 0,
            "IPv6Gateway": "",
            "MacAddress": "",
            "Networks": {
                "bridge": {
                    "IPAMConfig": null,
                    "Links": null,
                    "Aliases": null,
                    "NetworkID": "0dcbee6cae33b71554e83473308e4eabaecdbf07ba4ff9d8233ac082b2b683da",
                    "EndpointID": "",
                    "Gateway": "",
                    "IPAddress": "",
                    "IPPrefixLen": 0,
                    "IPv6Gateway": "",
                    "GlobalIPv6Address": "",
                    "GlobalIPv6PrefixLen": 0,
                    "MacAddress": "",
                    "DriverOpts": null
                }
            }
        }
...
```

可以使用 `docker network create` 创建一个用户定义的 netowrk。
```bash
$ docker network create my-network
```

使用 `docker run` 命令的 `--network` 选项，连接一个用户定义的 network。
```bash
$ docker run -d --name my-app --network my-network my-app:1.0
```

使用 `docker network connect` 命令将正在运行的 container 连至某个 network。
```bash
$ docker network connect my-network my-app
```

目前我们仅提到 bridge network，它也是大部分用户使用 docker 时最常使用的 network。还有其他 3 种 network，分别是：

- **host**：host 驱动是 Docker 主机使用的网络。 如果使用 bridge netowrk 时可用端口的数量太有限，那么可以使用 Docker host network。
- **none**：不需要连接网络的 container 可以不适用 network。所有输入和输出都通过 `STDIN` 和 `STDOUT`，或者通过 Docker volume 中的镜像文件。
- **overlay**：用于连接不同 Docker 主机上的 container。经常用于在 [swarm mode](https://docs.docker.com/engine/swarm/swarm-mode/) 下运行 docker。
- **maclan**：为 container 分配 MAC 地址。

使用 `docker network ls` 命令，查看 Docker 主机上的 networks。
```bash
$ docker network ls
NETWORK ID     NAME         DRIVER    SCOPE
0dcbee6cae33   bridge       bridge    local
202317a10699   host         host      local
3cbfeea17fe0   my-network   bridge    local
50aa20c9c46a   none         null      local
```

> [Networking overview](https://docs.docker.com/network/)
>
> [Use bridge networks](https://docs.docker.com/network/bridge/)
