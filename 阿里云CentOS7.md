### 阿里云负债均衡配置SLB

1、计费模式   按流量，按带宽    

2、可用区选择  多可用区 ，单可用区



### 第一章 ECS云服务

##### 什么是云服务ECS 

云服务器（Elastic Compute Service， 简称ECS）是一种简单高效，处理能力可弹性伸缩的计算服务



云主机与与服务概念

 

自动快照策略

1. 定位清楚业务类型和应用场景
2. 估算成本

类似与模板，包含了操作系统和预装的软件

通过镜像的一键部署，执行配置环境，安装软件，繁琐且耗时的操作





### Centos7特性

##### 系统基础变化

文件系统的变化 xfs   

修改主机名  /etc/hostname 

修改时区  timedatectl set timezone  命令修改

查看IP信息  ip 

修改DNS 地址 

##### 核心服务

防火墙  firewalld

服务管理  systemd

查看端口的状态   ss   

##### 其他变化

Centos7默认支持docker 

   内核支持overlayFS 

   Repo源支持

不再支持32的操作系统

支持40G以太网络卡

GNOME 3.X



### CentOS7系统配置

##### 操作系统主机名的修改

```
hostname   
hostname  新的主机名   #临时修改主机名  
vim /etc/hostname  #永久修改主机名
```



##### 操作系统的时区

```
timedatectl   通过这个命令设置
```



##### Centos7常见的网络接口

eth0      这是新的接口命名  ens223

```
vim /etc/sysconfig/grub 
在GRUB_CMDLINE_LINUX中添加 。net.ifnames=0 biosdevname=0  
```

在centos7中使用的是的ip 查看

 如果使用ifconfig  需要安装 net-tools

##### centos7中网络IP地址的修改

查看网络接口地址信息

```
ip a   addr的缩写
配置多个网络IP接口地址
ip a add   192.168.8.148/24 dev eth0:2
在阿里云上需要通过弹性网络来配置
```

操作控制网络接口

ip link set dev eth0  down  将我的接口设备进行关闭  eth0关闭

ip link set dev eth0 up   启动接口

配置路由

 ip route

策略路由



#### Centos7自动补全   

bash-completion  安装这个软件包 进行命令和参数的自动补全

```
yum install -y bash-completion
```



##### Centos7系统内存

内存的查看方式

应用程序

buffers    缓存  存取速度不同步的设备或优先级不同的设备之间传输数据的缓存区域

cached    缓存  CPU读取数据文件缓存的一个存储缓存区域

未使用  

shell的 共享内存

当程序需要时，可以回收cacha和Buffers来提供给应用程序使用



操作系统内存耗尽，将触发OOM机制，导致系统自动重启

不要期待swap能解决内存使用问题

使用到60%-80%，需要重视



##### 内存的使用情况查看

top    m    p   shift M   shift p

free -m



#### centos7中端口ss命令的使用

netstat    ss

 ss命令比netstat执行快

netstat -luntp  查看系统中正在监听的服务  

 netstat 提取的是/proc/目录下的信息

而ss 提取的是/proc/net目录，范围更小



端口的连接状态  

TCP三次握手

1 syn（j）

2 syn（K） ack(j+1)      sysn_recv   

3 ack(k)

TCP 四次挥手

1. fin(m)
2. ack(m+1)
3. fin(n)
4. ack(n+1)



-ltnup

l  监听的状态 

t  TCP 

n   显示端口

u   UDP   

p  进程

a  所有的状态信息 

ss -lntp state established  只查看当前状态的端口信息

ss  -s     全局的角度查看对应的端口信息

ss -na sport eq :22  只查看22端口的信息



systemd单元   

服务类型   

无扩展名，systemctl默认吧扩展名当作.service

挂载点，设备名称会完成自动转化为对应的单元

一个软件包可能会提供多个不同的单元



| 文件扩展名 | 作用                     | 文件扩展名 | 作用               |
| ---------- | ------------------------ | ---------- | ------------------ |
| .service   | 用于定义系统服务         | .snapshot  | 管理系统快照       |
| .target    | 模拟实现“运行级别”       | .swap      | Swap设备           |
| .device    | 定义内核识别设备         | .automount | 自动挂载点         |
| .mount     | 文件系统挂载点           | .path      | 监视文件或是目录   |
| .socket    | 进程间通信用的socket文件 | .scope     | 外部线程           |
| .timer     | 定时器                   | .slice     | 分层次管理系统进程 |



| 基础命令                        | 作用                                         |
| ------------------------------- | :------------------------------------------- |
| systemctl ,systemctl list-units | 查看激活的单元                               |
| systemctl  --failed             | 查看运行失败的单元                           |
| systemctl list-unit-files       | 查看所有可用的单元                           |
| systemctl help <单元 >          | 显示单元的帮助手册                           |
| system daemon-reload            | 重新载入systemd，扫描新的或有变动的单元      |
| systemctl start <单元>          | 激活单元                                     |
| systemctl stop <单元>           | 停止单元                                     |
| systemctl restart <单元>        | 重启单元                                     |
| systemctl reload <单元>         | 重载单元                                     |
| systemctl status <单元>         | 输出单元运行状态                             |
| systemctl is-enabled <单元>     | 检查单元是否配置为自动启动                   |
| systemctl enabled <单元>        | 开机激活单元                                 |
| systemctl enabled --now <单元>  | 设置单元为自动启动并立即启动这个单元         |
| systemctl disable <单元>        | 取消开机自动激活单元                         |
| systemctl mask <单元>           | 禁用一个单元（禁用后，间接启动也是不可能的） |
| systemctl unmark <单元>         | 取消禁用的某个单元                           |

systemctl  --type  servcie list-unit-files



##### Systemd的优势

1. 支持并行启动，显著提供开机启动效率

2. Centos7 关机只关闭正在运行的服务

3. 对于服务的管理不需要基于init.d下的脚本

4. Systemd解决原有模式的缺陷


systemctl list-dependencies  服务单元   或是启动级别target

systemctl  --reverse list-dependencies  服务单元   或是启动级别target



服务单元文件配置

[Unit]:单元的说明及依赖相关设置
[Service]:定义单元的管理方式
[isntall]:定义加如到target单元中



 systemctl cat nginx  查看对应的服务单元文件的配置



Systemd日志管理Journal

journalctl   显示所有信息

notice 或warning以粗体显示

journalctl -n 20   显示最后20行

journalctl -f   实时更新日志

journalctl -p err..alert 查看指定级别的日志

journalctl -u   nginx.servcie  查看指定服务单元的日志

journalctl -b  查看系统启动时候的日志  

journalctl  --since    today   参看今天的日志

journalctl --until  "30 hours ago "  查看30分钟之前的日志

 journalctl  _PID= 2345   根据PID查看日志

journalctl _UID=2344 根据UID查看日志



### 防火墙

ls /user/lib/firewalld/zones/  查看防火墙的区域

firewall-cmd --list-all  查看当前区域

 firewall-cmd  --get-active-zones  查看当前活动的区域

firewall-cmd --set-default-zone =home  设置当前默认的网络区域



- 一个网卡接口只能属于一个zone ，不能同时属于多个zone 
- 一个zone可以对应多个网卡接口i
- 任何配置了一个网络接口的区域就是个以活动区域



 ip a  

lo  是本地回环接口 

查看网卡接口是那个区域

firewall-cmd --list-all

firewall-cmd --get-active-zones  当前活跃的区域一定有网卡接口

将网卡接口的区域进行转移

firewall-cmd --zone=home --change-interface=eth0 

firewall-cmd --list-all

firewall-cmd --zone=home --add-interface=eth0 

firewall-cmd --zone=home --remove-interface=eth0   移除eth0

firewall-cmd --zone=home --list-all



临时性添加

firewall-cmd --zone=public --add-port=21/tcp   在public区域添加21端口

firewall-cmd --zone=public --remove-port=21/tcp   在public区域添加21端口



firewall-cmd --complete-reload

firewall-cmd --reload

永久性添加

firewall-cmd --zone=public --add-port=21/tcp --permanent 

firewall-cmd --zone=public --remove-port=21/tcp --permanent 

Firewalld规则配置

从外访问服务器内部，如果没有添加规则默认是阻止

从服务器内部访问服务器外部默认是允许的

想让规则永久生效，需要加入--permanent选项

每次更改规则后需执行 firewall-cmd --reload（动态加载新规则）



服务规则的添加

firewall-cmd --zone=public --add-service=http

firewall-cmd --zone=public --list-services

firewall-cmd --zone=public --list-all

























































































