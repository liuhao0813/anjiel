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
sudo docker network bridge 查看bridge网络类型
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

> 注意：优先使用自定义bridge的方式，因为使用自定义bridge的话，默认连接到自定义bridge的网络上都是可以通过使用rongqi的name进行网络访问，不存在--link的单向性 



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





