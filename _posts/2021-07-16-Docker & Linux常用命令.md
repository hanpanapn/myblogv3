---
tags : Linux 
stickie: true
---

### Linux

打包：将 /etc/ 内的所有档案备份下来，并且保存其权限！

```bash
tar -czvpf /tmp/etc.tar.gz /etc
```

解压：将/etc/etc.tar.gz 里面的所有文件解压到 /home/test文件夹里面

```bash
cd /home/test && tar -xzvf /etc/etc.tar.gz

```

#### Docker

Docker中网络配置 ：Bridge模式,

Bridge模式是Docker默认的网络模式，当Docker进程启动时，会在主机上创建一个名为docker0的虚拟网桥，用来连接宿主机和容器，此主机上的Docker容器都会连接到这个虚拟网桥上，虚拟网桥的工作方式和物理交换机类似，这样所有容器就通过交换机连在了一个二层网络中。

参考：[Docker的bridge和macvlan两种网络模式](https://www.cnblogs.com/yanh0606/p/9013255.html)

Docker中网络配置 ：macvlan模式


```bash
#因为多个MAC地址的网络数据包都是从同一块网卡上传输，所以需要打开网卡的混杂模式 
sudo ip link set eth0 promisc on  
```

创建网段：

```bash
docker network create -d macvlan --subnet=192.168.123.0/24 --gateway=192.168.123.1 -o parent=eth0 macnet
```
```macnet``` 我理解是网络名称


查看网络内部信息:
```bash
docker network inspect macnet
```
网卡列表:
```bash
docker network  ls
```

移除指定的网络：

```bash
docker network rm macnet
```

拉取Docker镜像:

```bash
docker pull ubuntu
```

已端口形式启动容器：

```bash
docker run --restart always --name ubuntu3 -itd  -p 8000:80 --privileged ubuntu:latest bash
```

新建并启动名称为```ubuntu1```的容器：

```bash
docker run --restart always --name ubuntu3 -itd  --network macnet --privileged ubuntu:latest bash
```
 
```--restart always``` 参数表示容器退出时始终重启，使服务尽量保持始终可用；

```--name ubuntu1``` 参数定义了容器的名称；

```-d``` 参数定义使容器运行在 Daemon 模式（后台运行）；  -itd 注意这个

```--network macnet```参数定义将容器加入 maxnet网络；

```--privileged``` 参数定义容器运行在特权模式下；

```ubuntu:latest``` 为 Docker 镜像名 

```/sbin/init``` 定义容器启动后执行的命令。
 
 
 启动已终止容器：
 
 ```bash
 d
 ```
 
 进入容器
 ```bash
 docker exec -it 容器ID bash
 ```
 
 更多参数说明请使用 docker exec --help 查看
 
 Linux容器内修改并重启网络：
 
 ```bash
 vim /etc/config/network
 /etc/init.d/network restart

 ```
 
 停止名称为```ubuntu1```的容器：
 
 ```bash
 
 ```
 
 删除名称为```ubuntu1```的容器：

 ```bash
 docker container rm 容器ID
 ```
 
 如果要删除一个运行中的容器，可以添加 -f 参数。Docker 会发送 SIGKILL 信号给容器。
 

查看docker挂载目录：

```bash
#grep 查询，-A20 ：应该是前20行
docker inspect 容器ID | grep Mounts -A  20 

```

删除一个停止的Docker容器

```base
docker container rm 容器ID
```

如果要删除一个运行中的容器，可以添加 -f 参数。Docker 会发送 SIGKILL 信号给容器。


---

#### 参考文章

[Docker之四种网络模式 、容器的互通与隔离](https://blog.csdn.net/lilygg/article/details/88616218)

[Docker —— 从入门到实践](https://yeasy.gitbook.io/docker_practice/)

[在Docker 中运行 OpenWrt 旁路网关](https://mlapp.cn/376.html)

[Docker的bridge和macvlan两种网络模式](https://www.cnblogs.com/yanh0606/p/9013255.html)

