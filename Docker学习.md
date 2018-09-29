# Docker学习

## 虚拟机的安装（vagrant+visualBox）

1. 安装VisualBox，[VisualBox下载](https://www.virtualbox.org/)

2. 安装vagrant，[vagrant下载](https://www.vagrantup.com/)

3. 注意，安装需要先安装visualBox ，在安装vagrant

4. 通过vagrant init  box名称，初始化一个Vagrantfile   

   > 例如：vagrant init centos/7     会在当前目录下生成一个Vagrantfile文件如下所示：

```
Vagrant.configure("2") do |config|
  config.vm.box = "centos/7"
end
注意文件中#号开头的都是注释，直接删除即可
```

1. 使用vagrant up命令下载box和启动容器，如果速度很慢请自行离线下载
2. 通过vagrant ssh可以进入到虚拟机中

```
vagrant status 查看当前主机的虚拟机状态  
vagrant box list 查看当前主机上已经安装的box信息
vagrant halt  停止默认虚拟主机   跟上虚拟主机名字，停止对应的虚拟主机
vagrant destroy  删除虚拟主机
vagrant init  生成一个默认的Vagrantfile
vagrant up 启动当前目录的一个Vagrantfile对应的虚拟主机配置
```



## docker安装

> 这里之记录在centos下安装dockers ce版本，具体需要安装其他版本请自行翻阅官方文档，本教程也是参考官方文档
>
> [docker安装教程](https://docs.docker.com/install/linux/docker-ce/centos/)

1.  删除系统老的docker版本，如果是新系统此步可以不执行

   ```
   sudo yum remove docker \
                     docker-client \
                     docker-client-latest \
                     docker-common \
                     docker-latest \
                     docker-latest-logrotate \
                     docker-logrotate \
                     docker-selinux \
                     docker-engine-selinux \
                     docker-engine
   ```

2. 设置对应的docker仓库信息

3. ```
   sudo yum install -y yum-utils \
     device-mapper-persistent-data \
     lvm2
    
   sudo yum-config-manager \
       --add-repo \
       https://download.docker.com/linux/centos/docker-ce.repo
   ```

   开始安装Docker ce版本

   ```
   sudo yum install -y docker-ce
   ```

4. 安装成功可以在控制台输入命令 

5. ```
   sudo docker version
   ```

   启动docker服务

   ```
   sudo systemctl start docker
   ```

6. 试运行sudo docker run hello-world命令

7. ```wiki
   [vagrant@localhost /]$ sudo docker run hello-world
   Unable to find image 'hello-world:latest' locally
   latest: Pulling from library/hello-world
   latest: Pulling from library/hello-world
   d1725b59e92d: Pulling fs layer
   d1725b59e92d: Pull complete
   Digest: sha256:0add3ace90ecb4adbf7777e9aacf18357296e799f81cabc9fde470971e499788
   Status: Downloaded newer image for hello-world:latest
   
   Hello from Docker!
   This message shows that your installation appears to be working correctly.
   
   To generate this message, Docker took the following steps:
    1. The Docker client contacted the Docker daemon.
    2. The Docker daemon pulled the "hello-world" image from the Docker Hub.
       (amd64)
    3. The Docker daemon created a new container from that image which runs the
       executable that produces the output you are currently reading.
    4. The Docker daemon streamed that output to the Docker client, which sent it
       to your terminal.
   
   To try something more ambitious, you can run an Ubuntu container with:
    $ docker run -it ubuntu bash
   
   Share images, automate workflows, and more with a free Docker ID:
    https://hub.docker.com/
   
   For more examples and ideas, visit:
    https://docs.docker.com/get-started/
   ```

通过如下VagrantFile文件可以创建一个安装好docker容器的虚拟机

> Vagrant.configure("2") do |config|
> ​     config.vm.box = "centos/7"
> ​     config.vm.provision "shell", inline: <<-SHELL
> ​           sudo yum remove docker docker-client docker-client-latest docker-common  docker-latest  docker-latest-logrotate  docker-logrotate   docker-selinux  docker-engine-selinux   docker-engine	
> ​           sudo yum install -y yum-utils  device-mapper-persistent-data   lvm2
> ​           sudo yum-config-manager   --add-repo   https://download.docker.com/linux/centos/docker-ce.repo
> ​           sudo yum install -y docker-ce
> ​           sudo systemctl start docker
> ​      SHELL
> end



## docker-machine 

docker-machine安装，参见[官方文档](https://docs.docker.com/machine/overview/)

docker-machine 可以直接创建带docker的虚拟机   

docker-machine create demo 创建一个docker版本的visualbox虚拟主机

docker-machine ls  查看docker-machine 当前的虚拟机列表

docker-machine ssh demo 进入到demo虚拟主机

 docker-machine stop demo1  停止一台虚拟机

decker-machine  env demo  demo虚拟主机的环境变量信息 

根据提示运行eval $(docker-machine env demo)

eval $(docker-machine env --unset)  取消远程连接



#### 通过docker-machine 在阿里云上创建docker虚拟主机

> 需要先安装对应的驱动信息  [阿里云对应的驱动信息](https://github.com/AliyunContainerService/docker-machine-driver-aliyunecs)

```
docker-machine create -d aliyunecs --aliyunecs-io-optimized=optimized --aliyunecs-instance-type=ecs.c5.large --aliyunecs-access-key-id=LTAIPoRNN2dtRazR --aliyunecs-access-key-secret=Wvdknasdf34dkasdf23asdfq3erqwerfdd --aliyunecs-region=CN-qingdao demo

-d aliyunecs 驱动类型为aliyunecs 
--aliyunecs-io-optimized=optimized  创建阿里云IO优化的参数，必须指定
--aliyunecs-instance-type  #创建的虚拟主机的类型 (实例的规格)
--aliyunecs-access-key-id  #当前账号对应的access-key
--aliyunecs-access-key-secret #当前账号对应的密钥
demo就是对应的实例名称

```



###### Docker 架构和底层技术

- Docker提供了一个开发，打包，运行app的平台

- 把app和底层infrastructure（physical/virtual）隔离开来


###### Docker Engine 核心组件

- 后台进程（dockerd）
- Rest API Server
- CLI接口 docker

###### docker 底层技术支持

- Namespaces  ：做隔离Pid，net， ipc ，mnt，uts
- control groups： 做资源的限制
- Union File Systems ： container 和image的分层



## Docker Image概述

- 文件和matedata的集合（root filesystem）
- 分成的，并且每一层都可以添加改变，删除文件，成为一个新的image
- 不同的image可以共享相同的layer
- Image本身是read-only的

##### Image的获取

- build from Dockerfile    

  ```
  FROM ubuntu:14.04
  LABEL maintainer="liu hao <237594169@qq.com>"
  RUN apt-get update && apt-get install -y redis-server
  EXPOSE 6379
  ENTRYPOINT [ "/usr/bin/redis-server"]
  
  $ docker build -t xiaopeng163/redis:latest .
  ```

- pull from Registry  从[hub.docker.com](https://hub.docker.com/)

  ```
  sudo docker pull ubuntu:14.04
  ```


> ### 小技巧  去掉sudo前缀
>
> 1. 创建docker用户组   sudo groupadd docker  
> 2. 将vagrant用户添加到docker用户组中 sudo gpasswd -a vagrant docker
> 3. 重启docker服务和重新登陆虚拟主机



#### 制作一个base Image

```
docker pull hello-world
docker image ls   #查看image的详细信息
docker run helloworld   #执行helloworld这个container
```

1. 创建一个目录，目录中放置一个可执行的文件 

   history |  grep yum   #查询yum的运行历史

2. 创建Dockerfile文件,内容如下 

   ```
   FROM scratch
   ADD hello /
   CMD ["/hello"]
   
   $docker build -t xiaopeng163/hello-world .
   ```

docker history imageID   查看Image的分层信息

docker run xiaopeng163/hello-world   执行image信息



## 什么是Container

- 通过Image创建（copy）
- 在image layer之上建立一个container layer（可读写）
- 类比面向对象：类(Image)和实例(Container)
- Image负责app的存储和分发，Container负责运行app



docker container ls    #查看当前正在运行的容器

docker container ls -a   #查看所有的容器信息   <=> docker ps -a

more hello-world/Dockerfile  #查看对应的image对应的dockerFile文件

docker run centos   创建并运行centos容器

docker run -it  centos   创建并以交互方式运行centos容器

#### docker的命令分类

Management Commands   对docker进行资源管理

Commands  操作命令



docker container rm   <=> docker rm   

docker images = docker image ls

docker image rm  =  docker rmi 

docker container ls -aq 查询所有container的id

docker rm $(docker container ls -aq)  清楚所有的容器

docker rm $(docker container ls -f "status=exited" -q)   清楚所有已经推出的congtainer



##### 自己动手构建一个新的Image（构建自己的docker镜像）

- docker container commit    建一个container构建成一个新的Image
- docker image build         

>  例 docker commit   容器名称   dockerid/新的容器名称

一般情况想通过一个Dockerfile文件build一个docker image ,如下Dockerfile文件内容

```
FROM centos
RUN yum install -y vim 
```

> 运行docker build -t  liuhao/centos-vim .  #最后的点表示从当前目录下的Dockerfile构建





#### DockerFile

> [官方的Dockerlibrary库](https://github.com/docker-library)     可以学习更多的Dockerfile的编写



##### FROM

```
FROM scratch   #制作base image
FROM centos   #使用官方的centos image   
```

##### LABEL

```
LABEL maintainer=“liuhao@qq.com”
LABEL version="1.0"
LABEL description="This is description"
```

>  Matadata 不可少！
>
> label 同写代码的注释



##### RUN

```
RUN yum update && yum install -y vim \
	python-dev    #反斜线换行
RUN age-get update && age-get install -y perl \
	pwgen --no-install-recommends && rm -rf \
	/var/lib/opt/lists/*   #注意清理cache
RUN /bin/bash -c 'source $HOME/.bashrc; echo $HOME' 
```

> 每运行一次RUN 都会生成一个新的层，避免无用分层，合并多条命令成一行！
>
> 为了美观，复杂的RUN请用反斜线换行！



##### WORKDIR

```
WORKDIR /root
WORKDIR /test #如果没有会自动创建test目录
RUM pwd  #输出结果是：/test
```

> 用WORKDIR，不要用RUN cd！尽量使用绝对目录



##### ADD and COPY 

```
ADD hello /
ADD test.tar.gz /  #添加到根目录并解压
```

> 大部分情况，COPY 由于ADD ！ADD除了COPY还有额外功能（解压）！
>
> 添加远程文件/目录请使用curl或者wget！



##### ENV

```
ENV MYSQL_VERSION 5.6  # 设置常量
RUN apt-get install -y mysql-server="${MYSQL_VERSION}" \
	&& rm -rf /var/lib/apt/lists/*  #引用常量
```

> 尽量使用ENV，增加可维护性！



##### VOLUME and EXPOSE

> 存储和网络



##### CMD and ENTRYPOINT

> RUN 执行命令并创建新的Image Layer
>
> CMD 设置容器启动后默认执行的命令和参数
>
> ENTRYPOINT 设置容器启动时运行的命令

- Shell格式

  ```
  RUN apt-get install -y vim
  CMD echo "hello docker"
  ENTRYPOINT echo "hello docker"
  ```

- Exec格式

  ```
  RUN [ "apt-get","install","-y","vim"]
  CMD ["/bin/echo","hello docker"]
  ENTRYPOINT ["/bin/echo","hello docker"]
  ```


##### CMD

> - 容器启动时默认执行的命令
> - 如果docker run 指定了其他命令，CMD命令被忽略
> - 如果定义了多个CMD 只有最后一个会执行



##### ENTRYPOINT

- 让容器以应用程序或者服务的形式运行
- 不会被忽略，一定会执行
- 最佳实践：写一个shell脚本作为entrypoint

```
COPY docker-entrypoint.sh /usr/local/bin/
ENTRYPOINT ["docker-entrypoint.sh"]

EXPOSE 27017
CMD ["mongod"]
```



#### Image的分发

1. 创建[dockerhub](https://hub.docker.com/)账号

2. 通过docker login登陆,输入用户名和密码

   ```
   docker login
   ```

3. 通过docker push 提交

   ```
   docker push 237594169/helloword:latest
   ```

4. 可以通过docker pull 237594169/helloworld

   > 推荐分发的方式使用dockerfile的方式分发





#### 搭建自己的registry

- 使用官方的[registry](https://hub.docker.com/_/registry/) 

- docker run -d -p 5000:5000 --restart always  --name registry registry:2

- 提交registry到刚才启动的docker容器

  ```
  重新构建一个镜像
  docker build -t registry主机IP：端口/hello-world .  #当前目录的Dockerfile
  在当前服务器的/etc/docker目录下创建 daemon.json key.json
  daemon.json文件内容为：
  { "insecure-registries":["registry主机IP：端口"] }
  编辑/lib/systemd/system/docker.service
  增加一行 EnviromentFile=-/etc/docker/daemon.json
  重启docker服务
  docker push registry主机IP：端口/hello-world
  ```




#### Dockerfile实战演示

> 演示Stress项目的使用

```
docker run -it ubuntu 

apt-get update && apt-get install -y stress  

which stress     stress安装位置

stress --help   帮助信息

stress --vm 1  --verbose 
```

DEMO Dockerfile如下：

```
FROM ubuntu
RUN apt-get update && apt-get install -y stress
ENTRYPOINT ["/usr/bin/stress"]
CMD []    #这个地方的参数是通过docker run 运行是跟在后面的参数
例：docker run -it liuhao/ubuntu-stress --vm 1  --verbose
```



#### 如果对一个容器进行资源限制

--memory         设置内存的大小  

--memory-swap    如果未设置swap-memeory大小，默认是memeory的大小

docker run --memory=200M liuhao/ubuntu-stress --vm 1 --verbose --vm-byte 500M

--cpu-shares   设置CPU的占比权重  可以设置容器占用CPU的优先级



#### Docker Network

##### 单机

- Bridge Network
- Host Network
- None Network  

##### 多机

- Overlay Network

演示环境：  通过vagrant启动两台visualbox虚拟主机

```
Vagrant.require_version ">=1.6.0"

boxes = [
    {
        :name => "docker-node1",
        :eth1 => "192.168.205.10",
        :mem => "1024",
        :cpu => "1"
    },
    {
        :name => "docker-node2",
        :eth1 => "192.168.205.11",
        :mem => "1024",
        :cpu => "1"
    }
]

Vagrant.configure(2) do |config|

	conofig.vm.box = "centos/7"
	
	boxes.each do |opts|
		config.vm.define opts[:name] do |config|
			config.vm.hostname = opts[:name]
			config.vm.provider "vmware_fusion" do |v|
				v.vmx["memSize"] = opts[:mem]
				v.vmx["numvcpus"] = opts[:cpu]
			end
			
			config.vm.privider "virtualbox" do |v|
				v.customize ["modifyvm", :id, "--memory", opts[:mem]]
				v.customize ["modifyvm", :id, "--cpus", opts[:cpu]]
			end 
			config.vm.network :private_network, ip: opts[:eth1]
		end
	end

	config.vm.synced_folder "./labs", "/home/vagrant/labs"
	config.vm.provision "shell", privileged: true, path: "./setup.sh"

end
```



> 注意：安装vagrant+centos7时，映射目录的时候报错
>
> ```
> Vagrant was unable to mount VirtualBox shared folders. This is usually
> because the filesystem "vboxsf" is not available. This filesystem is
> made available via the VirtualBox Guest Additions and kernel module.
> Please verify that these guest additions are properly installed in the
> guest. This is not a bug in Vagrant and is usually caused by a faulty
> Vagrant box. For context, the command attempted was:
> 
> mount -t vboxsf -o uid=1000,gid=1000 vagrant /vagrant
> The error output from the command was:
> mount: unknown filesystem type 'vboxsf'
> ```
>
>   解决：
> vagrant plugin install vagrant-vbguest
>
> vagrant reload --provision  



#### 网络的基础

##### Ping 和telnet 

- Ping （ICMP协议）：验证IP的可达性
- telnet（ICMP） ：验证服务的可用性

wireshark   抓包工具



网络的命名空间

```
sudo docker run -d --name test1 busybox /bin/sh -c "while true; do sleep 3600; done"
sudo docker ps
sudo docker exec -it 容器的ID   /bin/sh   #进入到容易内部
ip a   #查看当前虚拟主机的ip配置信息
sudo docker exec 容器id ip a
```



sudo ip netns list    查看当前主机的网络namespace 

sudo ip netns delete test1    删除test1的network namespace

sudo ip netns add test1   添加test1的network namespace

sudo ip netns exec test1  ip a    看到的是

ip link   

sudo ip netns exec test1 ip link set dev lo up    让lo端口的up起来

 

实例演示，让两台虚拟机可以连接起来

```
ip link
sudo ip link add veth-test1 type veth peer name veth-test2
ip link 
sudo ip link set veth-test1 netns test1
sudo ip link set veth-test2 netns test2
sudo ip netns exec test1 ip link
sudo ip netns exec test2 ip link
ip link

sudo ip netns exec test1 ip addr add 192.168.1.1/24 dev veth-test1
sudo ip netns exec test1 ip addr add 192.168.1.2/24 dev veth-test2
sudo ip netns exec test1 ip link
sudo ip netns exec test2 ip link

sudo ip netns exec test1 ip link set dev veth-test1 up   #启动Veth-test1端口
sudo ip netns exec test2 ip link set dev veth-test2 up   #启动Veth-test1端口
sudo ip netns exec test1 ip a
sudo ip netns exec test2 ip a


sudo ip netns exec test1 ping 192.168.1.2
sudo ip netns exec test2 ping 192.168.1.1
```



#### 默认网络bridge

```
sudo docker network ls 
sudo docker network inspect 容器ID   查看容易网路配置信息
sudo docker exec test1 ip a    
brctl   show   查看虚拟机与docker容器网路的映射 
sudo yum install -y bridge-utils     #如果上面的命令无法运行，请先安装bridge-utils工具
sudo docker run -d --name test1 busybox /bin/sh -c "while true; do sleep 3600; done"
sudo docker network inspect bridge 查看bridge网络类型
```



#### 容器之间的link 

1、容器之间可以通过指定--link参数，直接连接到其他容器中

```
例：
 sudo docker  run -d --name test1  busybox  /bin/sh -c "while true; do sleep 3600; done"
 sudo docker  run -d --name test2 --link test1 busybox  /bin/sh -c "while true; do sleep 3600; done"
 
 docker exec -it test2 /bin/sh 
 ping test1  这里应该是可以ping通的
```

> 注意：--link 具有单向性 



2、通过创建自己的bridge进行网络的连接

```
docker network create -d bridge my-bridge
docker network ls
sudo docker run -d --name test3 --network my-bridge busybox /bin/sh -c "while true; do sleep 3600; done"
sudo docker ps 
brctl show 
sudo docker network inspect my-bridge   查看my-bridge的网络配置信息
docker network connect  my-bridge test2  将test2连接到my-bridge上
```

> 注意：优先使用自定义bridge的方式，因为使用自定义bridge的话，默认连接到自定义bridge的网络上都是可以通过使用容器的name进行网络访问，不存在--link的单向性 



> 使用多机网络的时候需要注意DNS文件的解析问题，/etc/resolv.conf 这个中，需要配置正确的DNS解析,去掉对一个你的search 选项



#### 容器端口的映射

```
docker run --name web -d nginx
docker run --name web -d -p 80:80 nginx  
```

> 通过-p参数指定端口的映射  格式是：内部端口：外部端口



####   容器网络之HOST和NONE

- NONE是孤立的，独立的docker容器  一般情况下很少使用

- Host ，host网络连接没有自己的网络连接，和主机共享网络连接，会存在端口冲突问题。


#### 多容器复杂引用的部署

启动容器的时候可以给容器设置一个环境变量 ，通过-e 指定参数



#### 使用etcd工具配置多机网络连接

##### 安装etcd集群

在docker-node1上

```
vagrant@docker-node1:~$ wget https://github.com/coreos/etcd/releases/download/v3.0.12/etcd-v3.0.12-linux-amd64.tar.gz
vagrant@docker-node1:~$ tar zxvf etcd-v3.0.12-linux-amd64.tar.gz
vagrant@docker-node1:~$ cd etcd-v3.0.12-linux-amd64
vagrant@docker-node1:~$ nohup ./etcd --name docker-node1 --initial-advertise-peer-urls http://192.168.205.10:2380 \
--listen-peer-urls http://192.168.205.10:2380 \
--listen-client-urls http://192.168.205.10:2379,http://127.0.0.1:2379 \
--advertise-client-urls http://192.168.205.10:2379 \
--initial-cluster-token etcd-cluster \
--initial-cluster docker-node1=http://192.168.205.10:2380,docker-node2=http://192.168.205.11:2380 \
--initial-cluster-state new&
```


在docker-node2上

```
vagrant@docker-node2:~$ wget https://github.com/coreos/etcd/releases/download/v3.0.12/etcd-v3.0.12-linux-amd64.tar.gz
vagrant@docker-node2:~$ tar zxvf etcd-v3.0.12-linux-amd64.tar.gz
vagrant@docker-node2:~$ cd etcd-v3.0.12-linux-amd64/
vagrant@docker-node2:~$ nohup ./etcd --name docker-node2 --initial-advertise-peer-urls http://192.168.205.11:2380 \
--listen-peer-urls http://192.168.205.11:2380 \
--listen-client-urls http://192.168.205.11:2379,http://127.0.0.1:2379 \
--advertise-client-urls http://192.168.205.11:2379 \
--initial-cluster-token etcd-cluster \
--initial-cluster docker-node1=http://192.168.205.10:2380,docker-node2=http://192.168.205.11:2380 \
--initial-cluster-state new&
```

检查cluster状态

```
vagrant@docker-node2:~/etcd-v3.0.12-linux-amd64$ ./etcdctl cluster-health
member 21eca106efe4caee is healthy: got healthy result from http://192.168.205.10:2379
member 8614974c83d1cc6d is healthy: got healthy result from http://192.168.205.11:2379
cluster is healthy
```

##### 重启docker服务


在docker-node1上

```
$ sudo service docker stop
$ sudo /usr/bin/dockerd -H tcp://0.0.0.0:2375 -H unix:///var/run/docker.sock --cluster-store=etcd://192.168.205.10:2379 --cluster-advertise=192.168.205.10:2375&
```

在docker-node2上

```
$ sudo service docker stop
$ sudo /usr/bin/dockerd -H tcp://0.0.0.0:2375 -H unix:///var/run/docker.sock --cluster-store=etcd://192.168.205.11:2379 --cluster-advertise=192.168.205.11:2375&
```

##### 创建overlay network

在docker-node1上创建一个demo的overlay network

```
vagrant@docker-node1:~$ sudo docker network ls
NETWORK ID          NAME                DRIVER              SCOPE
0e7bef3f143a        bridge              bridge              local
a5c7daf62325        host                host                local
3198cae88ab4        none                null                local
vagrant@docker-node1:~$ sudo docker network create -d overlay demo
3d430f3338a2c3496e9edeccc880f0a7affa06522b4249497ef6c4cd6571eaa9
vagrant@docker-node1:~$ sudo docker network ls
NETWORK ID          NAME                DRIVER              SCOPE
0e7bef3f143a        bridge              bridge              local
3d430f3338a2        demo                overlay             global
a5c7daf62325        host                host                local
3198cae88ab4        none                null                local
vagrant@docker-node1:~$ sudo docker network inspect demo
[
    {
        "Name": "demo",
        "Id": "3d430f3338a2c3496e9edeccc880f0a7affa06522b4249497ef6c4cd6571eaa9",
        "Scope": "global",
        "Driver": "overlay",
        "EnableIPv6": false,
        "IPAM": {
            "Driver": "default",
            "Options": {},
            "Config": [
                {
                    "Subnet": "10.0.0.0/24",
                    "Gateway": "10.0.0.1/24"
                }
            ]
        },
        "Internal": false,
        "Containers": {},
        "Options": {},
        "Labels": {}
    }
]
```

我们会看到在node2上，这个demo的overlay network会被同步创建

```
vagrant@docker-node2:~$ sudo docker network ls
NETWORK ID          NAME                DRIVER              SCOPE
c9947d4c3669        bridge              bridge              local
3d430f3338a2        demo                overlay             global
fa5168034de1        host                host                local
c2ca34abec2a        none                null                local
```

通过查看etcd的key-value, 我们获取到，这个demo的network是通过etcd从node1同步到node2的

```
vagrant@docker-node2:~/etcd-v3.0.12-linux-amd64$ ./etcdctl ls /docker
/docker/network
/docker/nodes
vagrant@docker-node2:~/etcd-v3.0.12-linux-amd64$ ./etcdctl ls /docker/nodes
/docker/nodes/192.168.205.11:2375
/docker/nodes/192.168.205.10:2375
vagrant@docker-node2:~/etcd-v3.0.12-linux-amd64$ ./etcdctl ls /docker/network/v1.0/network
/docker/network/v1.0/network/3d430f3338a2c3496e9edeccc880f0a7affa06522b4249497ef6c4cd6571eaa9
vagrant@docker-node2:~/etcd-v3.0.12-linux-amd64$ ./etcdctl get /docker/network/v1.0/network/3d430f3338a2c3496e9edeccc880f0a7affa06522b4249497ef6c4cd6571eaa9 | jq .
{
  "addrSpace": "GlobalDefault",
  "enableIPv6": false,
  "generic": {
    "com.docker.network.enable_ipv6": false,
    "com.docker.network.generic": {}
  },
  "id": "3d430f3338a2c3496e9edeccc880f0a7affa06522b4249497ef6c4cd6571eaa9",
  "inDelete": false,
  "ingress": false,
  "internal": false,
  "ipamOptions": {},
  "ipamType": "default",
  "ipamV4Config": "[{\"PreferredPool\":\"\",\"SubPool\":\"\",\"Gateway\":\"\",\"AuxAddresses\":null}]",
  "ipamV4Info": "[{\"IPAMData\":\"{\\\"AddressSpace\\\":\\\"GlobalDefault\\\",\\\"Gateway\\\":\\\"10.0.0.1/24\\\",\\\"Pool\\\":\\\"10.0.0.0/24\\\"}\",\"PoolID\":\"GlobalDefault/10.0.0.0/24\"}]",
  "labels": {},
  "name": "demo",
  "networkType": "overlay",
  "persist": true,
  "postIPv6": false,
  "scope": "global"
}
```

##### 创建连接demo网络的容器

在docker-node1上

```
vagrant@docker-node1:~$ sudo docker run -d --name test1 --net demo busybox sh -c "while true; do sleep 3600; done"
Unable to find image 'busybox:latest' locally
latest: Pulling from library/busybox
56bec22e3559: Pull complete
Digest: sha256:29f5d56d12684887bdfa50dcd29fc31eea4aaf4ad3bec43daf19026a7ce69912
Status: Downloaded newer image for busybox:latest
a95a9466331dd9305f9f3c30e7330b5a41aae64afda78f038fc9e04900fcac54
vagrant@docker-node1:~$ sudo docker ps
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS               NAMES
a95a9466331d        busybox             "sh -c 'while true; d"   4 seconds ago       Up 3 seconds                            test1
vagrant@docker-node1:~$ sudo docker exec test1 ifconfig
eth0      Link encap:Ethernet  HWaddr 02:42:0A:00:00:02
          inet addr:10.0.0.2  Bcast:0.0.0.0  Mask:255.255.255.0
          inet6 addr: fe80::42:aff:fe00:2/64 Scope:Link
          UP BROADCAST RUNNING MULTICAST  MTU:1450  Metric:1
          RX packets:15 errors:0 dropped:0 overruns:0 frame:0
          TX packets:8 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:0
          RX bytes:1206 (1.1 KiB)  TX bytes:648 (648.0 B)

eth1      Link encap:Ethernet  HWaddr 02:42:AC:12:00:02
          inet addr:172.18.0.2  Bcast:0.0.0.0  Mask:255.255.0.0
          inet6 addr: fe80::42:acff:fe12:2/64 Scope:Link
          UP BROADCAST RUNNING MULTICAST  MTU:1500  Metric:1
          RX packets:8 errors:0 dropped:0 overruns:0 frame:0
          TX packets:8 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:0
          RX bytes:648 (648.0 B)  TX bytes:648 (648.0 B)

lo        Link encap:Local Loopback
          inet addr:127.0.0.1  Mask:255.0.0.0
          inet6 addr: ::1/128 Scope:Host
          UP LOOPBACK RUNNING  MTU:65536  Metric:1
          RX packets:0 errors:0 dropped:0 overruns:0 frame:0
          TX packets:0 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:1
          RX bytes:0 (0.0 B)  TX bytes:0 (0.0 B)

vagrant@docker-node1:~$
```

在docker-node2上

```
vagrant@docker-node2:~$ sudo docker run -d --name test1 --net demo busybox sh -c "while true; do sleep 3600; done"
Unable to find image 'busybox:latest' locally
latest: Pulling from library/busybox
56bec22e3559: Pull complete
Digest: sha256:29f5d56d12684887bdfa50dcd29fc31eea4aaf4ad3bec43daf19026a7ce69912
Status: Downloaded newer image for busybox:latest
fad6dc6538a85d3dcc958e8ed7b1ec3810feee3e454c1d3f4e53ba25429b290b
docker: Error response from daemon: service endpoint with name test1 already exists.
vagrant@docker-node2:~$ sudo docker run -d --name test2 --net demo busybox sh -c "while true; do sleep 3600; done"
9d494a2f66a69e6b861961d0c6af2446265bec9b1d273d7e70d0e46eb2e98d20
```

##### 验证连通性。

```
vagrant@docker-node2:~$ sudo docker exec -it test2 ifconfig
eth0      Link encap:Ethernet  HWaddr 02:42:0A:00:00:03
          inet addr:10.0.0.3  Bcast:0.0.0.0  Mask:255.255.255.0
          inet6 addr: fe80::42:aff:fe00:3/64 Scope:Link
          UP BROADCAST RUNNING MULTICAST  MTU:1450  Metric:1
          RX packets:208 errors:0 dropped:0 overruns:0 frame:0
          TX packets:201 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:0
          RX bytes:20008 (19.5 KiB)  TX bytes:19450 (18.9 KiB)

eth1      Link encap:Ethernet  HWaddr 02:42:AC:12:00:02
          inet addr:172.18.0.2  Bcast:0.0.0.0  Mask:255.255.0.0
          inet6 addr: fe80::42:acff:fe12:2/64 Scope:Link
          UP BROADCAST RUNNING MULTICAST  MTU:1500  Metric:1
          RX packets:8 errors:0 dropped:0 overruns:0 frame:0
          TX packets:8 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:0
          RX bytes:648 (648.0 B)  TX bytes:648 (648.0 B)

lo        Link encap:Local Loopback
          inet addr:127.0.0.1  Mask:255.0.0.0
          inet6 addr: ::1/128 Scope:Host
          UP LOOPBACK RUNNING  MTU:65536  Metric:1
          RX packets:0 errors:0 dropped:0 overruns:0 frame:0
          TX packets:0 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:1
          RX bytes:0 (0.0 B)  TX bytes:0 (0.0 B)

  vagrant@docker-node1:~$ sudo docker exec test1 sh -c "ping 10.0.0.3"
  PING 10.0.0.3 (10.0.0.3): 56 data bytes
  64 bytes from 10.0.0.3: seq=0 ttl=64 time=0.579 ms
  64 bytes from 10.0.0.3: seq=1 ttl=64 time=0.411 ms
  64 bytes from 10.0.0.3: seq=2 ttl=64 time=0.483 ms
  ^C
  vagrant@docker-node1:~$
```



#### Docker数据的存储Volume

##### volume的类型

- 受管理的data Volume，由docker后台自动创建

- 绑定挂载的Volume，具体挂载位置可以由用户指定



#### 实验环境搭建

vagrant plugin list    查看vagrant插件列表

vagrant plugin install vagrant-scp   安装vagrant-scp插件

vagrant scp 将本地目录拷贝到远程虚拟主机上

vagrant scp ../chapter5/labs/ docker-node1: /home/vagrant/labs/ 将chapter5/labs目录拷贝到docker-node1中的/home/vagrant/labs内



#### 数据持久化：Data Volume 

docker run -d --name mysql1 mysql -e MYSQL_ALLOW_EMPTY_PASSWORD=true mysql

sudo docker logs mysql1  查看当前容器的日志

dacker volume ls   查看volume信息

可以通过 VOLUME ["/var/lib/mysql"]  在Dockerfile中指定此命令

通过-v  name：映射到本地的路径   

docker run -d -v mysql：/var/lib/mysql --name mysql1 mysql -e MYSQL_ALLOW_EMPTY_PASSWORD=true mysql



#### 数据持久化：Bind Mouting 



docker run -v  /home/aaa:/root/aaa   会做主机与docker容器对应的文件映射，会自动进行同步

more dockerfile 查看容易的详细信息

docker run  -d -v $(pwd):/usr/share/nginx/html -p 80:80 --name web nginx 

docker rm -f web 强制删除容器



 

#### docker-compose

> 案例：wordpress+mysql 两个容器
>
> docker run -d --name mysql -v mysql-data:/var/lib/mysql -e MYSQL_ROOT_PASSWORD=root -e MYSQL_DATABASE=wordpress mysql
>
> docker run -d -e WORDPRESS_DB_HOST=mysql:3306 --link mysql -p 8080:80 wordpress



Docker Compose 是一个docker cli工具

可以通过一个yml文件定义多容器的docker应用

通过一条命令就可以根据yml文件的定义去创建或者管理这多个容器

docker-compose.yml

Services  volume  network



Services 

一个service代表一个container，这个container可以从dockerhub的image来创建，或者从本地的dockerfilebuild出来的image来创建

service的启动类似docker run ，我们可以给其指定network和volume，所以可以给service指定network和volume的引用



services：

   db:

​        image: postgres: 9.4

  		volumes:

  			- "db-data: /var/lib/postgresql/data"

​		networks:

​			- back-tier

安装docker-compose 

[docker-compose](https://docs.docker.com/compose/install/#install-compose)



docker-compose ps 查看docker-compose的状态

docker-compose stop 停止/   down 停止同时删除

docker-compose start 启动  

docker-compose up -d   后台启动   调试不用加-d参数，可以看到日志 

docker-compose iamges   docker-compose 中的images

docker-compose exec   docker-compose定义的service 

例1：

```yml
version: "3"
services: 
	redis：
		image：redis
	web：
		build：
			context：.
			dockerfile: Dockerfile
		ports:
			- 8080:5000
		enviroment:
			REDIS_HOST: redis
```

例2：

```yml
	version:"3"
	services:
		wordpress:
			image: wordpress
			ports:
				- 8080:80
			environment:
				WORDPRESS_DB_HOST: mysql
				WORDPRESS_DB_PASSWORD: root
			network:
				- my-bridge
				
		mysql:
			image: mysql
			environment:
				MYSQL_ROOT_PASSWORD: root
				MYSQL_DATABASE: wordpress
			volumes:
				- mysql-data:/var/lib/mysql
			network:
				- my-bridge
	volumes:
		mysql-data:
		
	networks:
		my-bridge:
			driver: bridge
	
```



容器的水平扩展和负载均衡

docker-compose up --scale web=3 -d  注意docker-compose.yml中的port占用问题

例：

```yml
version: "3"
services: 
	redis：
		image：redis
	web：
		build：
			context：.
			dockerfile: Dockerfile
		enviroment:
			REDIS_HOST: redis
	
	lb:
		image: dockercloud/haproxy
		links: 
			- web
		ports: 
			- 8080: 80
		volumes:
		 	- /var/run/docker.sock:/var/run/docker.sock
```

 



#### Swarm Mode 容器编排

docker swarm init --advertise-addr=192.168.205.10   定义一个clust节点  swarm-manager节点

根据提示信息中的命令，创建对应的work节点

```
docker swarm join --token SWMTKN-1-25wu6w86o2zsudxatj231crbqilehsgmbvqshfy2op1tghyx0r-4uybv9feqwdekp7qx1j04u5ti 192.168.0.17:2377
```

docker swarm  --help 

docker node ls   显示swarm节点列表 查看对应的clust节点的状态，只能在manager上进行操作

docker service create   --help

swarm模式下使用dockerservice create 创建容器

docker service create  --name demo busybox sh -c "while true; do sleep 3600; done"

docker service ls 查看service 列表信息 

docker service ps demo 查看demo容器的详细信息

docker service scale   对service进行横向的扩展   ，对应的service可以自动启动达到指定的扩展数量

例  docker service scale   demo=5

 

docker service rm demo   删除name为demo的service名称



### 案例，在多机上部署mysql及wordpress项目

- 创建overlay网络，并设置名称为demo

```
docker network create -d overlay demo
```

- 创建mysql service，指定对应的数据库密码是root 数据库名称是wordpress，指定到demo网络中，同时挂载指定对应的挂载名称为mysql-data，制定到对应的/var/lib/mysql 目录中  

```
docker service create --name mysql --env MYSQL_ROOT_PASSWORD=root --env MYSQL_DATABASE=wordpress  --network demo --mount type=volume,source=mysql-data,destination=/var/lib/mysql mysql
```

在service中，通过--mount指定对应的数据存储映射

- 创建wordpress服务，指定服务名称为wordpress，同时配置对应的端口映射到80端口，配置环境变量，数据库的密码为root ，对应的数据库主机为上个步骤中创建的mysql服务

```
docker service create --name wordpress -p 80:80  --network demo --env WORDPRESS_DB_PASSWORD=root  --env WORDPRESS_DB_HOST=mysql wordpress
```

- 查看服务的启动状态  

```
docker service ls
```



## docker stack deploy   

docker stack deploy wordpress --compose-file=docker-compose.yml

 

```yml
version: "3"
services:
  web:
    image: wordpress
    ports:
      - 8080:80
    environment:
      WORDPRESS_DB_HOST: mysql
      WORDPRESS_DB_PASSWORD: root
    networks:
      - my-network
    depends_on:
      - mysql
    deploy:
      mode: replicated
      replicas: 3
      restart_policy:
        condition: on-failure
        delay: 5s
        max_attempts: 3
      update_config:
        parallelism: 1
        delay: 5s
        order: stop-first   #这个只支持3.4以上版本 ，这里不支持
        
   mysql:
     image: mysql
     environment:
       MYSQL_ROOT_PASSWORD: root
       MYSQL_DATABASE: wordpress
     volumes:
       - mysql-data:/var/lib/mysql
     networks:
       - my-network
     deploy: 
       mode: global
       placement:
         constraints: 
           - node.role == manager
         
volumes:
  mysql-data:
  
networks：
  my-bridge：
    driver: overlay
        
```





endpoint_mode:

vip  虚拟IP

dnsrr   DNS round-robin   使用DNS的负载均衡

global ：不能做横向扩展

replicated   默认，可以横向扩展  

​        

placement ：

​    constranints：  指定部署到指定的node



## Docker Secret Management

- 存在Swarm Manager节点Raft database里
- Secret可以assign给一个service，这个service就能看到这个secret
- 在container内部Secret看起来像文件，但是实际是在内存中



docker  secret create  

```
vim password 
docker secret create my-pw password  #通过文件创建密码
rm -f password

echo "adminadmin" | docker secret create my-pw2 -  通过管道创建secret
docker secret ls 查看secret  

docker service create --secret 
docker service create --name client --secret my-pw busybox sh -c "while true; do sleep 3600; done"
docker service ls 
docker exec -it 容器ID sh 
cd /run/secrets   这个文件可以看到对应的密码信息


docker service create --name db --secret my-pw -e MYSQL_ROOT_PASSWORD_FILE=/run/secrets/my-pw mysql  指定mysqlrootpassword来源于指定的secret文件
```



docker stack deply wordpress -c=docker-compose.yml



## Service更新

确认主机上有overlay网络

docker service create --name web -p 8080:5000  --network demo xiaopeng163/python-flask-demo:1.0

确认service有多个，这样在更新的时候才不会停掉

docker service scale web=3

docker service ps web 

curl 127.0.0.1:8080

sh -c "while true ; do curl 127.0.0.1:8080&&sleep 1; done"  监听这个服务

```
docker service update --image xiaopeng163/python-flask-demo:2.0   image的更新
docker service update --publish-rm 8080:5000  --pubish-add 8088:5000 web  端口的更新
```

docker stack 直接就是在docker-compose.yml中更改版本信息，

通过docker stack deploy -c=docker-compose.yml更新



Docker Cloud

CaaS    Container-as-a-Service

Docker Cloud是提供容器的管理，编排，部署的托管服务

关联云服务上 AWS ECS Azure

添加节点行为Docker host

创建服务Service

创建Stack

Image管理



两种模式

Standard模式，一个Node就是一个Docker Host

Swarm模式（beta） 多个Node组成的Swarm Cluster





## Docker 企业版

[docker EE](http://tore.docker.com ) 下载

docker ee on centos













