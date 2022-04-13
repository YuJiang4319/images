# Docker学习笔记

> docker学习大纲

- docker概述
- docker安装
- docker命令
  - 镜像命令
  - 容器命令
  - 操作命令
  - ...
- docker镜像
- 容器数据卷
- dockerfile
- docker网络原理
- IDEA整合docker
- docker compose
- docker swarm

## Docker概述

### docker为什么出现？

一款产品：开发到上线，是两套环境

开发... 运维。问题：现在在我的电脑上可以运行，而版本更新，导致服务不可用。对于运维来说很麻烦，每个机器都要部署环境（集群Redis，ES，Hadoop）

所以我们希望发布一个项目时，带上所有环境打包

传统：开发jar，运维部署

现在：开发，打包，部署一套

![image-20220318095019373](.\images\image-20220318095019373.png)

docker思想来源于集装箱

核心思想--隔离

## Docker历史

2010年，几个搞IT的年轻人，就在美国成立了一家公司dotCloud，做一些 pass的云计算服务，LXC 有关的容器技术！
他们将自己的技术 (容器化技术) 命名 就是 Docker 
Docker 刚刚诞生的时候，没有引起行业的注意！ dotCloud，就活不下去
2013年，Docker开源！
Docker越来越多的人发现了docker的优点！火了，Docker每个月都会更新一个版本！
2014年4月9日，Docker1.0发布！
Docker为什么这么火？十分的轻巧！
在容器技术出来之前，我们都是使用虚拟机技术！
虚拟机：在windows中装一个Vmware，通过这个软件我们可以虚拟出来一台或者多台电脑！笨重！
虚拟机也是属于虚拟化技术，Docker 容器技术，也是一种 虚拟化技术！

```shell
vm ;1inux centos原生镜像（一个电脑！） 隔离，需要开启多个虚拟机！几个G几分钟
docker：隔离，镜像（最核心的环境 4m+jdk+mysq1）十分的小巧， 运行镜像就可以了 小巧 几个M  KB  秒级启动！
```

> 聊聊docker

Docker是基于Go语言开发的

官网：https://www.docker.com/

文档：https://docs.docker.com/

Docker仓库：https://hub.docker.com/

## Docker能干嘛

> 之前的虚拟化技术

![image-20220318100850581](.\images\image-20220318100850581.png)

- 虚拟机技术缺点：
  - 资源占用多
  - 冗余步骤多
  - 启动慢

> 容器化技术

![image-20220318101113843](.\images\image-20220318101113843.png)

- docker和虚拟机比较
  - 传统虚拟机，虚拟出一套硬件，运行一个完整的操作系统，然后在这个系统上运行软件
  - 容器内的应用直接运行在宿主机的内核上，没有自己单独的内核，也没有虚拟我们的硬件，轻便
  - 每个容器间相互隔离，每个容器内都有一个自己的文件系统 ，互不影响

> DevOps(开发、运维)

**应用更快的交付部署**

- 传统运维：一堆帮助文档，安装程序
- docker：打报镜像发布测试，一键运行

**更便捷的升级和扩缩容**

- 使用docker后，部署应用就和搭积木一样
- 项目打包为一个镜像，服务器A性能瓶颈，直接一键在服务器B上运行做负载均衡

**更简单的系统运维**

- 在容器化之后，我们的开发、测试环境都是高度一致的

**更高效的计算资源利用**

- docker是内核级别的虚拟化，可以在一个物理机上运行很多的容器，极致压榨服务器性能

## Docker安装

### Docker基本组成

> docker架构图

![image-20220318102401999](.\images\image-20220318102401999.png)

**镜像(image)：**

docker镜像就好比是一个模板，可以通过这个模板来创建容器服务，如Tomcat镜像=》run=》tomcat01容器(提供服务器)，通过这个镜像可以创建多个容器(最终服务或者项目就是运行在容器中)

**容器(container)：**

- docker利用容器技术，独立运行一个或者一个组应用，通过镜像来创建
- 启动、停止、删除等基本命令
- 可以理解为一个建议的Linux系统

**仓库(repository)：**

- 存放镜像的地方
- 分为私有仓库和公有仓库
  - dockerhub、阿里云...都有容器服务器

### 安装Docker

> 环境准备

- Linux操作基础
- CentOS 7
- Xshell远程连接

> 环境查看

```shell
# 系统内核3.10以上
[root@Mir ~]# uname -r
3.10.0-1160.53.1.el7.x86_64
```

```shell
# 查看系统版本
[root@Mir etc]# cat /etc/os-release 
NAME="CentOS Linux"
VERSION="7 (Core)"
ID="centos"
ID_LIKE="rhel fedora"
VERSION_ID="7"
PRETTY_NAME="CentOS Linux 7 (Core)"
ANSI_COLOR="0;31"
CPE_NAME="cpe:/o:centos:centos:7"
HOME_URL="https://www.centos.org/"
BUG_REPORT_URL="https://bugs.centos.org/"

CENTOS_MANTISBT_PROJECT="CentOS-7"
CENTOS_MANTISBT_PROJECT_VERSION="7"
REDHAT_SUPPORT_PRODUCT="centos"
REDHAT_SUPPORT_PRODUCT_VERSION="7"
```

> 安装

**查看官方帮助文档**

- **卸载旧版本**

```shell
sudo yum remove docker \
                  docker-client \
                  docker-client-latest \
                  docker-common \
                  docker-latest \
                  docker-latest-logrotate \
                  docker-logrotate \
                  docker-engine
```

- **需要的安装包**

```shell
sudo yum install -y yum-utils
```

- **设置镜像仓库**

```shell
sudo yum-config-manager \
    --add-repo \
    https://download.docker.com/linux/centos/docker-ce.repo # 默认为国外的，较慢
# 建议使用以下仓库(阿里云)
sudo yum-config-manager \
    --add-repo \
	http://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo
```

- **更新yum软件包索引**

```shell
[root@Mir etc]# yum makecache fast
```

- **安装docker**  

```shell
sudo yum install docker-ce docker-ce-cli containerd.io  # ce为社区版  ee企业版
```

- **启动docker**

```shell
systemctl start docker
```

- **使用docker version查看是否安装成功**

```shell
docker version
```

![image-20220318110211003](.\images\image-20220318110211003.png)

- **测试docker引擎**

```shell
sudo docker run hello-world
```

![image-20220318110549899](.\images\image-20220318110549899.png)

- **查看下载的hello-world镜像**

```shell
[root@Mir etc]# docker images
REPOSITORY    TAG       IMAGE ID       CREATED        SIZE
hello-world   latest    feb5d9fea6a5   5 months ago   13.3kB
```

- **卸载docker**

```shell
# 卸载依赖
sudo yum remove docker-ce docker-ce-cli containerd.io
# 删除资源
sudo rm -rf /var/lib/docker  # docker默认工作路径 
sudo rm -rf /var/lib/containerd
```

### 阿里云镜像加速

- **找到容器镜像服务**

![image-20220318111739605](.\images\image-20220318111739605.png)

- **配置**

```shell
sudo mkdir -p /etc/docker
sudo tee /etc/docker/daemon.json <<-'EOF'
{
  "registry-mirrors": ["https://sfvejie6.mirror.aliyuncs.com"]
}
EOF
sudo systemctl daemon-reload
sudo systemctl restart docker
```

- **回顾hello-world流程**

![image-20220318112222961](.\images\image-20220318112222961.png)

### 底层原理

**docker是怎么工作的？**

- Docker是一个Client - Server结构的系统，Docker的守护进程运行在主机上，通过socket从客户端访问
- DockerServer 接收到 Docker-Client 的指令，就会执行这个命令

![image-20220318112838931](.\images\image-20220318112838931.png)

**Docker为什么比虚拟机快？**

- docker有着比虚拟机更少的抽象层
- docker利用的是宿主机的内核，VM需要的是Guest OS

![image-20220318113420867](.\images\image-20220318113420867.png)

- 所以说，新建一个容器的时候，docker不需要想虚拟机一样重新加载一个操作系统内核，避免引导
- 虚拟机是加载Guest OS，分钟级别的，而docker 是利用 宿主机的操作系统，省略了这个复杂的过程，秒级

## Docker常用命令

### 帮助命令

```shell
docker version   # 显示docker版本信息
docker info		# 显示docker系统信息，包括镜像和容器的数量
docker command --help		# 帮助命令
```

- 帮助文档地址：https://docs.docker.com/reference/

### 镜像命令

- **docker images  查看所有本地主机上的镜像**

```shell
[root@Mir etc]# docker images
REPOSITORY    TAG       IMAGE ID       CREATED        SIZE
hello-world   latest    feb5d9fea6a5   5 months ago   13.3kB
# 解释
REPOSITORY	镜像的仓库源
TAG			镜像的标签
IMAGE ID	镜像的id
CREATED		镜像的创建时间
SIZE		镜像的大小
# 可选参数
  -a, --all             # 列出所有镜像
  -q, --quiet           # 只显示镜像的id

```

- **docker search 搜索镜像**

```shell
[root@Mir etc]# docker search mysql
```

![image-20220318121511354](.\images\image-20220318121511354.png)

```shell
# 可选参数
--filter=STARS=3000  # 搜索STARS大于3000的镜像
[root@Mir etc]# docker search mysql --filter=STARS=3000
NAME      DESCRIPTION                                     STARS     OFFICIAL   AUTOMATED
mysql     MySQL is a widely used, open-source relation…   12268     [OK]       
mariadb   MariaDB Server is a high performing open sou…   4718      [OK] 
```

- **docker pull** **下载镜像**

```shell
# 下载镜像  docker pull 镜像名[:tag]
[root@Mir etc]# docker pull mysql
Using default tag: latest   # 不加tag默认为latest最新版
latest: Pulling from library/mysql
72a69066d2fe: Pull complete   # 分层下载，docker image的核心，联合文件系统
93619dbc5b36: Pull complete 
99da31dd6142: Pull complete 
626033c43d70: Pull complete 
37d5d7efb64e: Pull complete 
ac563158d721: Pull complete 
d2ba16033dad: Pull complete 
688ba7d5c01a: Pull complete 
00e060b6d11d: Pull complete 
1c04857f594f: Pull complete 
4d7cfa90e6ea: Pull complete 
e0431212d27d: Pull complete 
Digest: sha256:e9027fe4d91c0153429607251656806cc784e914937271037f7738bd5b8e7709 # 签名防伪
Status: Downloaded newer image for mysql:latest
docker.io/library/mysql:latest   # 真实地址

# 上面命令等价于：
docker pull docker.io/library/mysql:lates

# 指定版本下载：
[root@Mir etc]# docker pull mysql:5.7
```

![image-20220318122304230](.\images\image-20220318122304230.png)

- 上面的Already exists体现了联合文件系统的优势，检查到不同版本镜像之间有相同文件则只下载差异部分

![image-20220318122647575](.\images\image-20220318122647575.png)

- **docker rmi删除镜像**

```shell
docker rmi -f 容器id  # 删除指定id容器
docker rmi -f 容器id 容器id 容器id ...   # 删除多个容器
docker rmi -f $(docker images -aq)    # 删除所有容器             
```

### 容器命令

**说明：我们有了镜像才可以创建容器，这里我们使用centOS测试学习**

```shell
docker pull centos
```

**新建容器并启动**

```shell
docker run [args] iamge
# 参数说明
--name="Name"  	# 容器名称 如Tomcat01 Tomcat02来区分容器
-d  			# 以后台方式运行
-it				# 使用交互方式运行，进入容器查看内容
-p				# 指定容器的端口  如-p 8080：8080
	-p ip：主机端口：容器端口
	-p 主机端口：容器端口(常用)
	-p 容器端口
	容器端口(直接写，不加-p)
-P				# 随机指定端口
```

```shell
# 启动并进入容器
[root@Mir etc]# docker run -it centos /bin/bash 

# 查看容器内的centos，基础版本，很多命令不完善
[root@a39748e1c63f /]# ls
bin  dev  etc  home  lib  lib64  lost+found  media  mnt  opt  proc  root  run  sbin  srv  sys  tmp  usr  var

# 从容器中返回主机
[root@a39748e1c63f /]# exit
exit

# 
```

**列出所有运行中的容器**

```shell
[root@Mir /]# docker ps    # 列出当前正在运行的容器
CONTAINER ID   IMAGE     COMMAND   CREATED   STATUS    PORTS     NAMES
# 参数说明
-a 		# 列出当前正在运行的容器以及历史运行过的容器
-n=?  	# 显示最近创建的容器，可指定个数
-q 		# 只显示容器的编号

[root@Mir /]# docker ps
CONTAINER ID   IMAGE     COMMAND   CREATED   STATUS    PORTS     NAMES
[root@Mir /]# docker ps -a
CONTAINER ID   IMAGE         COMMAND       CREATED         STATUS                      PORTS     NAMES
a39748e1c63f   centos        "/bin/bash"   3 minutes ago   Exited (0) 31 seconds ago             focused_bose
97988d387bed   hello-world   "/hello"      2 hours ago     Exited (0) 2 hours ago                objective_carver
```

**退出容器**

```shell
exit 	# 直接停止容器并退出
Ctrl + P + Q	# 不停止容器并退出

[root@Mir /]# docker run -it centos /bin/bash
{Ctrl + P + Q}
[root@Mir /]# docker ps
CONTAINER ID   IMAGE     COMMAND       CREATED          STATUS          PORTS     NAMES
bd6bf8cc4c20   centos    "/bin/bash"   45 seconds ago   Up 44 seconds             lucid_archimedes
```

**删除容器**

```shell
docker rm 容器id			# 删除指定容器，不能删除正在运行的容器，强制删除用rm -f
docker rm -f $(docker ps -aq)	# 删除所有的容器
docker ps -a -q|xargs docker rm		# 删除所有的容器
```

**启动、停止容器**

```shell
docker start 容器id		# 启动容器
docker restart 容器id		# 重启容器
docker stop 容器id		# 停止当前正在运行的容器
docker kill 容器id		# 强制停止当前容器
```

### 常用其他命令

**后台启动容器**

```shell
# docker run -d 镜像名
[root@Mir /]# docker run -d centos
# 出现问题docker ps:发现centos停止了
# 这是常见的坑:docker容器使用后台运行，就必须要有一个前台进程，docker发现没有应用，就会自动停止
# 如Nginx容器启动后，发现自己没有提供服务，就会立刻停止，就是没有程序了
```

**查看日志**

```shell
docker logs -f -t --tail n 容器id   # n是输出日志条数,f是format，t显示时间戳 
# 写一段shell脚本
docker run -d centos /bin/sh -c "while true;do echo kuangshen;sleep 1;done"
```

**查看容器中的进程信息**

```shell
# docker top 容器id
[root@Mir /]# docker top 991ab0d4e795
UID                 PID                 PPID                C                   STIME     
root                27592               27572               0                   15:27     
```

**查看镜像元数据**

```shell
# docker inspect 容器id

# 测试
[root@Mir /]# docker inspect 991ab0d4e795
[
    {
        "Id": "991ab0d4e795f0f806783a4f28a39a7e8ff8b096ee25508fe06ef2e199985aea",
        "Created": "2022-03-18T07:27:47.934060985Z",
        "Path": "/bin/bash",
        "Args": [],
        "State": {
            "Status": "running",
            "Running": true,
            "Paused": false,
            "Restarting": false,
            "OOMKilled": false,
            "Dead": false,
            "Pid": 27592,
            "ExitCode": 0,
            "Error": "",
            "StartedAt": "2022-03-18T07:27:48.331337114Z",
            "FinishedAt": "0001-01-01T00:00:00Z"
        },
        "Image": "sha256:5d0da3dc976460b72c77d94c8a1ad043720b0416bfc16c52c45d4847e53fadb6",
        "ResolvConfPath": "/var/lib/docker/containers/991ab0d4e795f0f806783a4f28a39a7e8ff8b096ee25508fe06ef2e199985aea/resolv.conf",
        "HostnamePath": "/var/lib/docker/containers/991ab0d4e795f0f806783a4f28a39a7e8ff8b096ee25508fe06ef2e199985aea/hostname",
        "HostsPath": "/var/lib/docker/containers/991ab0d4e795f0f806783a4f28a39a7e8ff8b096ee25508fe06ef2e199985aea/hosts",
        "LogPath": "/var/lib/docker/containers/991ab0d4e795f0f806783a4f28a39a7e8ff8b096ee25508fe06ef2e199985aea/991ab0d4e795f0f806783a4f28a39a7e8ff8b096ee25508fe06ef2e199985aea-json.log",
        "Name": "/objective_newton",
        "RestartCount": 0,
        "Driver": "overlay2",
        "Platform": "linux",
        "MountLabel": "",
        "ProcessLabel": "",
        "AppArmorProfile": "",
        "ExecIDs": null,
        "HostConfig": {
            "Binds": null,
            "ContainerIDFile": "",
            "LogConfig": {
                "Type": "json-file",
                "Config": {}
            },
            "NetworkMode": "default",
            "PortBindings": {},
            "RestartPolicy": {
                "Name": "no",
                "MaximumRetryCount": 0
            },
            "AutoRemove": false,
            "VolumeDriver": "",
            "VolumesFrom": null,
            "CapAdd": null,
            "CapDrop": null,
            "CgroupnsMode": "host",
            "Dns": [],
            "DnsOptions": [],
            "DnsSearch": [],
            "ExtraHosts": null,
            "GroupAdd": null,
            "IpcMode": "private",
            "Cgroup": "",
            "Links": null,
            "OomScoreAdj": 0,
            "PidMode": "",
            "Privileged": false,
            "PublishAllPorts": false,
            "ReadonlyRootfs": false,
            "SecurityOpt": null,
            "UTSMode": "",
            "UsernsMode": "",
            "ShmSize": 67108864,
            "Runtime": "runc",
            "ConsoleSize": [
                0,
                0
            ],
            "Isolation": "",
            "CpuShares": 0,
            "Memory": 0,
            "NanoCpus": 0,
            "CgroupParent": "",
            "BlkioWeight": 0,
            "BlkioWeightDevice": [],
            "BlkioDeviceReadBps": null,
            "BlkioDeviceWriteBps": null,
            "BlkioDeviceReadIOps": null,
            "BlkioDeviceWriteIOps": null,
            "CpuPeriod": 0,
            "CpuQuota": 0,
            "CpuRealtimePeriod": 0,
            "CpuRealtimeRuntime": 0,
            "CpusetCpus": "",
            "CpusetMems": "",
            "Devices": [],
            "DeviceCgroupRules": null,
            "DeviceRequests": null,
            "KernelMemory": 0,
            "KernelMemoryTCP": 0,
            "MemoryReservation": 0,
            "MemorySwap": 0,
            "MemorySwappiness": null,
            "OomKillDisable": false,
            "PidsLimit": null,
            "Ulimits": null,
            "CpuCount": 0,
            "CpuPercent": 0,
            "IOMaximumIOps": 0,
            "IOMaximumBandwidth": 0,
            "MaskedPaths": [
                "/proc/asound",
                "/proc/acpi",
                "/proc/kcore",
                "/proc/keys",
                "/proc/latency_stats",
                "/proc/timer_list",
                "/proc/timer_stats",
                "/proc/sched_debug",
                "/proc/scsi",
                "/sys/firmware"
            ],
            "ReadonlyPaths": [
                "/proc/bus",
                "/proc/fs",
                "/proc/irq",
                "/proc/sys",
                "/proc/sysrq-trigger"
            ]
        },
        "GraphDriver": {
            "Data": {
                "LowerDir": "/var/lib/docker/overlay2/cad0d46ed7090be8999df59f077a6f200076b392eea38ed9116ae61e8415179e-init/diff:/var/lib/docker/overlay2/eed1e8983755cd5e93cc031f6f5e99e6ceffef7633ec12843746a3ce5659ef81/diff",
                "MergedDir": "/var/lib/docker/overlay2/cad0d46ed7090be8999df59f077a6f200076b392eea38ed9116ae61e8415179e/merged",
                "UpperDir": "/var/lib/docker/overlay2/cad0d46ed7090be8999df59f077a6f200076b392eea38ed9116ae61e8415179e/diff",
                "WorkDir": "/var/lib/docker/overlay2/cad0d46ed7090be8999df59f077a6f200076b392eea38ed9116ae61e8415179e/work"
            },
            "Name": "overlay2"
        },
        "Mounts": [],
        "Config": {
            "Hostname": "991ab0d4e795",
            "Domainname": "",
            "User": "",
            "AttachStdin": true,
            "AttachStdout": true,
            "AttachStderr": true,
            "Tty": true,
            "OpenStdin": true,
            "StdinOnce": true,
            "Env": [
                "PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin"
            ],
            "Cmd": [
                "/bin/bash"
            ],
            "Image": "centos",
            "Volumes": null,
            "WorkingDir": "",
            "Entrypoint": null,
            "OnBuild": null,
            "Labels": {
                "org.label-schema.build-date": "20210915",
                "org.label-schema.license": "GPLv2",
                "org.label-schema.name": "CentOS Base Image",
                "org.label-schema.schema-version": "1.0",
                "org.label-schema.vendor": "CentOS"
            }
        },
        "NetworkSettings": {
            "Bridge": "",
            "SandboxID": "43bb1f25f964fce89a5ddb96ec138b332e04ad3b17735c3cab23b5155934430b",
            "HairpinMode": false,
            "LinkLocalIPv6Address": "",
            "LinkLocalIPv6PrefixLen": 0,
            "Ports": {},
            "SandboxKey": "/var/run/docker/netns/43bb1f25f964",
            "SecondaryIPAddresses": null,
            "SecondaryIPv6Addresses": null,
            "EndpointID": "f2440671838091a3411598d0836cb22353835c6289aac1f51e46c4b092f4bb52",
            "Gateway": "172.17.0.1",
            "GlobalIPv6Address": "",
            "GlobalIPv6PrefixLen": 0,
            "IPAddress": "172.17.0.2",
            "IPPrefixLen": 16,
            "IPv6Gateway": "",
            "MacAddress": "02:42:ac:11:00:02",
            "Networks": {
                "bridge": {
                    "IPAMConfig": null,
                    "Links": null,
                    "Aliases": null,
                    "NetworkID": "a43851655b4a108b037207aa6027242fd42d48debf90ee2a7eb6a61ab4913fe1",
                    "EndpointID": "f2440671838091a3411598d0836cb22353835c6289aac1f51e46c4b092f4bb52",
                    "Gateway": "172.17.0.1",
                    "IPAddress": "172.17.0.2",
                    "IPPrefixLen": 16,
                    "IPv6Gateway": "",
                    "GlobalIPv6Address": "",
                    "GlobalIPv6PrefixLen": 0,
                    "MacAddress": "02:42:ac:11:00:02",
                    "DriverOpts": null
                }
            }
        }
    }
]

```

**进入当前正在运行的容器**

```shell
# 我们通常都是以后台方式运行容器，需要进入容器修改配置

# docker exec -it 容器id bashshell 
[root@Mir /]# docker exec -it 991ab0d4e795 /bin/bash
[root@991ab0d4e795 /]# ps -ef
UID        PID  PPID  C STIME TTY          TIME CMD
root         1     0  0 07:27 pts/0    00:00:00 /bin/bash
root        16     0  0 07:51 pts/1    00:00:00 /bin/bash
root        36    16  0 07:53 pts/1    00:00:00 ps -ef

# 方式二
[root@Mir /]# docker attach 991ab0d4e795
正在执行的代码...

# docker exec    进入容器后开启一个新的终端，可以在里面操作(常用)
# docker attach  进入容器正在执行的终端，不会启动新的进程
```

**从容器内拷贝文件到主机上**

```shell
# docker cp 容器id:容器内路径 目的主机路径
# 拷贝是一个手动过程，未来我们使用 -v 卷的技术也可以实现
```

### 小结

![image-20220318163915470](.\images\image-20220318163915470.png)

```shell
attach    	# 当前shell下attach连接指定运行镜像
build     	# 通过Dockerfile定制镜像
commit    	# 提交当前容器为新的镜像
cp        	# 从容器中拷贝指定文件或目录到宿主机中
create    	# 创建一个新的容器，同 run 但不启动容器
diff      	# 查看 docker 容器变化
events    	# 从docker服务器获取容器实时事件
exec      	# 在已存在的容器上运行命令
export    	# 导出容器的内容流作为一个 tar 归档文件【对应 import】
history   	# 展示一个镜像形成历史
images    	# 列出系统当前镜像
import    	# 从tar包中的内容创建一个新的文件系统映像【对应 export】
info      	# 显示系统相关信息
inspect   	# 查看容器详细信息
kill      	# kill 指定的容器
load      	# 从一个 tar 包中加载一个镜像【对应 save】
login     	# 注册或者登录一个 docker 源服务器
logout    	# 从当前 Docekr registry 退出
logs      	# 输出当前容器日志信息
port 		# 查看映射端口对应的容器内部源端
pause		# 暂停容器
ps			# 列出容器列表
pu11		# 从docker镜像源服务器拉取指定镜像或者库镜像
push		# 推送指定镜像或者库镜像至docker源服务器
restart		# 重启运行的容器
rm			# 移除一个或者多个容器
rmi			# 移除一个或多个镜像[无容器使用该镜像才可删除，否则需删除相关容器才能继续-f强制删除
run			# 创建一个新的容器并运行一个命令
save		# 保存一个镜像为一个 tar 包[对应 1oad]
search		# 在 docker hub 中搜索镜像
start		# 启动容器
stop		# 停止容器
tag			# 给源中镜像打标签
top			# 查看容器中运行的进程信息
unpause		# 取消暂停容器
version		# 查看 docker 版本号
wait 		# 截取容器停止时的退出状态值
```

## 练习

> Docker安装Nginx

```shell
# 1、搜索镜像 search  建议去dockerhub搜索，可以查看帮助文档
[root@Mir /]# docker search nginx
# 2、下载镜像 pull
[root@Mir /]# docker pull nginx
# 3、运行测试
[root@Mir /]# docker run -d --name nginx01 -p 3344:80 nginx
[root@Mir /]# curl localhost:3344
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
<style>
html { color-scheme: light dark; }
body { width: 35em; margin: 0 auto;
font-family: Tahoma, Verdana, Arial, sans-serif; }
</style>
</head>
<body>
<h1>Welcome to nginx!</h1>
<p>If you see this page, the nginx web server is successfully installed and
working. Further configuration is required.</p>

<p>For online documentation and support please refer to
<a href="http://nginx.org/">nginx.org</a>.<br/>
Commercial support is available at
<a href="http://nginx.com/">nginx.com</a>.</p>

<p><em>Thank you for using nginx.</em></p>
</body>
</html>
# 4、进入容器
[root@Mir /]# docker exec -it 5a9bb2449cbe /bin/bash 
root@5a9bb2449cbe:/# ls
bin   dev		   docker-entrypoint.sh  home  lib64  mnt  proc  run   srv  tmp  var
boot  docker-entrypoint.d  etc			 lib   media  opt  root  sbin  sys  usr

root@5a9bb2449cbe:/# whereis nginx
nginx: /usr/sbin/nginx /usr/lib/nginx /etc/nginx /usr/share/nginx
```

![image-20220318165438298](.\images\image-20220318165438298.png)

**思考**：

- 我们每次改动nginx配置文件，都需要进入容器内部，十分的麻烦，如果能在容器外部提供一个映射路径，达到在容器外部修改文件名，容器内部就可以自动修改？      ==》-v 数据卷技术

> Tomcat部署

```shell
# 官方使用命令
docker run --rm tomcat:9.0
# 之前-d是后台启动，停止了容器之后，容器不会被删除   docker run --rm,一般用来测试，用完关闭即自动删除
# 这里使用正常下载并启动
[root@Mir /]# docker pull tomcat
[root@Mir /]# docker run -d --name tomcat01 -p 3355:8080 tomcat

# 测试访问没有问题

# 进入容器
[root@Mir /]# docker exec -it tomcat01 /bin/bash
# 发现问题：1.Linux命令少了  2.没有webapps，阿里云镜像的原因，默认是最小的镜像，所有不必要的都剔除掉
# 仅保证最小可运行环境
```

**思考：**

- 以后要部署项目，如果每次都要进入容器十分麻烦，希望在容器外部提供一个映射路径，webapps，我们在外部部署项目，然后自动同步到内部就好了

## 可视化

- portainer(暂时先使用)

```shell

```

- Rancher(CI/CD再用)

**什么是portainer？**

- Docker图形化管理工具，提供一个后台面板供我们操作

```shell
# 安装并运行
[root@Mir /]# docker run -d -p 8088:9000 --restart=always -v /var/run/docker.sock:/var/run/docker.sock --privileged=true portainer/portainer
Unable to find image 'portainer/portainer:latest' locally
latest: Pulling from portainer/portainer
94cfa856b2b1: Pull complete 
49d59ee0881a: Pull complete 
a2300fd28637: Pull complete 
Digest: sha256:fb45b43738646048a0a0cc74fcee2865b69efde857e710126084ee5de9be0f3f
Status: Downloaded newer image for portainer/portainer:latest
084ecedcd13e4a2132110794e44f37a07c25303545d5dc8a443c7d5a9cbcf04c

```

- 访问测试:http://47.99.154.107:8088/

![image-20220318225405341](.\images\image-20220318225405341.png)

- **选择本地**

![image-20220318225442487](.\images\image-20220318225442487.png)

- **进入之后的面板**

![image-20220318225626995](.\images\image-20220318225626995.png)

**平时一般不使用可视化面板，可测试玩玩**

## Docker镜像讲解

### 镜像是什么？

- 镜像是一种轻量级、可执行的独立软件包，用来打包软件运行环境和基于运行环境开发的软件，它包含运行某个软件所需的所有内容，包括代码、运行时库、环境变量和配置文件。
- 所有的应用，直接打包docker镜像，就可以直接跑起来！
- **如何得到镜像：**
  - 从远程仓库下载
  - 朋友拷贝给你
  - 自己制作一个镜像 DockerFile

### Docker镜像加载原理

> UnionFS (联合文件系统)

**我们下载镜像的时候看到的就是这个，类似于Git多次提交修改叠加**

- **UnionFS（联合文件系统）**：Union文件系统（UnionFS）是一种分层、轻量级并且高性能的文件系统，它支持对文件系统的修改作为一次提交来一层层的叠加，同时可以将不同目录挂载到同一个虚拟文件系统下(unite several directories into a single virtualfilesystem)。Union 文件系统是 Docker 镜像的基础。镜像可以通过分层来进行继承，基于基础镜像（没有父镜像）,可以制作各种具体的应用镜像。

- **特性**：一次同时加载多个文件系统，但从外面看起来，只能看到一个文件系统，联合加载会把各层文件系统聋加起来，这样最终的文件系统会包含所有底层的文件和目录

> Docker镜像加载原理

- docker的镜像实际上由一层一层的文件系统组成，这种层级的文件系统UnionFS。


- **bootfs(boot file system)**主要包含bootloader和kernel，bootloader主要是引导加载kernel，Linux刚启动时会加载bootfs文件系统，在Docker镜像的最底层是bootfs。这一层与我们典型的Linux/Unix系统是一样的, 包含boot加载器和内核。当boot加载完成之后整个内核就都在内存中了，此时内存的使用权已由bootfs转交给内核，此时系统也会卸载bootfs。

- **rootfs(root file system)**, 在bootfs之上。包含的就是典型Linux系统中的/dev, /proc, /bin, /etc等标准目录和文件。rootfs就是各种不同的操作系统发行版，比如Ubuntu，Centos等等。
  ![image-20220318231319979](.\images\image-20220318231319979.png)

**平时我们安装进虚拟机的centos都是好几个G，为什么docker这里才200M？**

![image-20220318231644581](.\images\image-20220318231644581.png)

- 对于一个精简的OS，rootfs 可以很小，只需要包含最基本的命令，工具和程序库就可以了，因为底层直接用Host的kernel，自己只需要提供rootfs就可以了。由此可见对于不同的linux发行版, bootfs基本是一致的, rootfs会有差别,因此不同的发行版可以公用bootfs。

### 分层理解

> 分层的镜像

- 我们可以去下载一个镜像，注意观察下载的日志输出，可以看到是一层一层在下载

![image-20220318232056267](images/image-20220318232056267.png)

- **思考：为什么Docker镜像要采用这种分层的结构呢？**
  - 最大的好处，我觉得莫过于是资源共享了！比如有多个镜像都从相同的Base镜像构建而来，那么宿主机只需在磁盘上保留一份base镜像，同时内存中也只需要加载一份base镜像，这样就可以为所有的容器服务了，而且镜像的每一层都可以被共享。
  - 查看镜像分层的方式可以通过 docker image inspect 命令！

```shell
[root@Mir /]# docker image inspect redis:latest
[
    {
        "Id": "sha256:7614ae9453d1d87e740a2056257a6de7135c84037c367e1fffa92ae922784631",
        "RepoTags": [
            "redis:latest"
        ],
        "RepoDigests": [
            "redis@sha256:db485f2e245b5b3329fdc7eff4eb00f913e09d8feb9ca720788059fdc2ed8339"
        ],
        "Parent": "",
        "Comment": "",
        "Created": "2021-12-21T12:42:49.755107412Z",
        "Container": "13d25f53410417c5220c8dfe8bd49f06abdbcd69faa62a9b877de02464bb04a3",
        "ContainerConfig": {
            "Hostname": "13d25f534104",
            "Domainname": "",
            "User": "",
            "AttachStdin": false,
            "AttachStdout": false,
            "AttachStderr": false,
            "ExposedPorts": {
                "6379/tcp": {}
            },
            "Tty": false,
            "OpenStdin": false,
            "StdinOnce": false,
            "Env": [
                "PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin",
                "GOSU_VERSION=1.12",
                "REDIS_VERSION=6.2.6",
                "REDIS_DOWNLOAD_URL=http://download.redis.io/releases/redis-6.2.6.tar.gz",
                "REDIS_DOWNLOAD_SHA=5b2b8b7a50111ef395bf1c1d5be11e6e167ac018125055daa8b5c2317ae131ab"
            ],
            "Cmd": [
                "/bin/sh",
                "-c",
                "#(nop) ",
                "CMD [\"redis-server\"]"
            ],
            "Image": "sha256:e093f59d716c95cfce82c676f099b960cc700432ab531388fcedf79932fc81ec",
            "Volumes": {
                "/data": {}
            },
            "WorkingDir": "/data",
            "Entrypoint": [
                "docker-entrypoint.sh"
            ],
            "OnBuild": null,
            "Labels": {}
        },
        "DockerVersion": "20.10.7",
        "Author": "",
        "Config": {
            "Hostname": "",
            "Domainname": "",
            "User": "",
            "AttachStdin": false,
            "AttachStdout": false,
            "AttachStderr": false,
            "ExposedPorts": {
                "6379/tcp": {}
            },
            "Tty": false,
            "OpenStdin": false,
            "StdinOnce": false,
            "Env": [
                "PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin",
                "GOSU_VERSION=1.12",
                "REDIS_VERSION=6.2.6",
                "REDIS_DOWNLOAD_URL=http://download.redis.io/releases/redis-6.2.6.tar.gz",
                "REDIS_DOWNLOAD_SHA=5b2b8b7a50111ef395bf1c1d5be11e6e167ac018125055daa8b5c2317ae131ab"
            ],
            "Cmd": [
                "redis-server"
            ],
            "Image": "sha256:e093f59d716c95cfce82c676f099b960cc700432ab531388fcedf79932fc81ec",
            "Volumes": {
                "/data": {}
            },
            "WorkingDir": "/data",
            "Entrypoint": [
                "docker-entrypoint.sh"
            ],
            "OnBuild": null,
            "Labels": null
        },
        "Architecture": "amd64",
        "Os": "linux",
        "Size": 112691373,
        "VirtualSize": 112691373,
        "GraphDriver": {
            "Data": {
                "LowerDir": "/var/lib/docker/overlay2/c95a095ab39e35a4837e2a625227eef89ef057982a22bf40d8934bf151a7bc43/diff:/var/lib/docker/overlay2/a3f0b0dc2b2706224d8e041bf8e31ebc91ba10de042c48ccd57c41f4917c62a3/diff:/var/lib/docker/overlay2/0c1b85389227627a6c7c6a2c74b8ff98c8ca6965db91e91cf502917201c9ae45/diff:/var/lib/docker/overlay2/f0ecefb9dba28e06368057b36ff93de24ad1ce589967e0b807fe0e36d62145bc/diff:/var/lib/docker/overlay2/39f95bbd9ff98228453939b1b11c068ccaf7d7a4729da1dbb7aaa5848f1235cc/diff",
                "MergedDir": "/var/lib/docker/overlay2/734558b15eb12ff3e826efd65acc62c388776d822917d4ee275e99b26fe044ef/merged",
                "UpperDir": "/var/lib/docker/overlay2/734558b15eb12ff3e826efd65acc62c388776d822917d4ee275e99b26fe044ef/diff",
                "WorkDir": "/var/lib/docker/overlay2/734558b15eb12ff3e826efd65acc62c388776d822917d4ee275e99b26fe044ef/work"
            },
            "Name": "overlay2"
        },
        "RootFS": {
            "Type": "layers",
            "Layers": [
                "sha256:2edcec3590a4ec7f40cf0743c15d78fb39d8326bc029073b41ef9727da6c851f",
                "sha256:9b24afeb7c2f21e50a686ead025823cd2c6e9730c013ca77ad5f115c079b57cb",
                "sha256:4b8e2801e0f956a4220c32e2c8b0a590e6f9bd2420ec65453685246b82766ea1",
                "sha256:529cdb636f61e95ab91a62a51526a84fd7314d6aab0d414040796150b4522372",
                "sha256:9975392591f2777d6bf4d9919ad1b2c9afa12f9a9b4d260f45025ec3cc9b18ed",
                "sha256:8e5669d8329116b8444b9bbb1663dda568ede12d3dbcce950199b582f6e94952"
            ]
        },
        "Metadata": {
            "LastTagTime": "0001-01-01T00:00:00Z"
        }
    }
]
```

**理解**

- 所有的 Docker 镜像都起始于一个基础镜像层，当进行修改或增加新的内容时，就会在当前镜像层之上，创建新的镜像层。
- 举一个简单的例子，假如基于 Ubuntu Linux 16.04 创建一个新的镜像，这就是新镜像的第一层；如果在该镜像中添加 Python包，就会在基础镜像层之上创建第二个镜像层；如果继续添加一个安全补丁，就会创建第三个镜像层。
- 该镜像当前已经包含 3 个镜像层，如下图所示（这只是一个用于演示的很简单的例子）。

![image-20220318232632278](images\image-20220318232632278.png)

- 在添加额外的镜像层的同时，镜像始终保持是当前所有镜像的组合，这一点很重要。下图中举了一个简单的例子，每个镜像层包含 3个文件，而镜像包含了来自两个镜像层的 6 个文件。

![image-20220318232835807](images\image-20220318232835807.png)

- 上图中的镜像层跟之前图中的略有区别，主要目的是便于展示文件。
- 下图中展示了一个稍微复杂的三层镜像，在外部看来整个镜像只有6个文件，这是因为最上层中的文件7是文件5的一个更新版本。

![image-20220318233031758](images\image-20220318233031758.png)

- 这种情况下，上层镜像层中的文件覆盖了底层镜像层中的文件。这样就使得文件的更新版本作为一个新镜像层添加到镜像当中。
- Docker 通过存储引擎（新版本采用快照机制）的方式来实现镜像层堆栈，并保证多镜像层对外展示为统一的文件系统。
- Linux 上可用的存储引擎有AUFS、Overlay2、Device Mapper、Btrfs以及ZFS。顾名思义，每种存储引擎都基于 Linux 中对应的文件系统或者块设备技术，并且每种存储引擎都有其独有的性能特点。
- Docker在Windows 上仅支持windowsfilter一种存储引擎，该引擎基于NTFS文件系统之上实现了分层和CoW。
- 下图展示了与系统显示相同的三层镜像。所有镜像层堆叠并合并，对外提供统一的视图。

![image-20220318233248263](images\image-20220318233248263.png)

> 特点

- Docker镜像都是只读的，当容器启动时，一个新的可写层被加载到镜像顶部
- 这一层就是我们通常说的容器层，容器之下的都叫镜像层

![image-20220318233806301](images\image-20220318233806301.png)

### Commit镜像

