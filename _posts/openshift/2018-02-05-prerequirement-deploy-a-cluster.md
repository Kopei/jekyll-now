---
layout: post
title: Openshift Origin cluster部署配置要求
---

> https://github.com/openshift/openshift-ansible

### 杂言
官方提供的github ansible部署代码还是有一些坑的，首先不能用master分支的代码，master分支是他们的开发分支。所以需要选择某个release， 以下选择release-3.8作为说明。先说说配置要求和操作系统、网络等环境等要求。

### 系统配置需求
- master，nodes, 外部etcd都有最小推荐配置. 大致上，master每1000pods需要额外1CPU和1.5GB内存。node的配置根据业务负载配置。
![prerequirement](http://p0iombi30.bkt.clouddn.com/%E5%B1%8F%E5%B9%95%E5%BF%AB%E7%85%A7%202018-02-05%20%E4%B8%8B%E5%8D%882.07.52.png)

### CPU核数配置
master和node默认使用系统所有的CPU, 可以设置环境变量`GOMAXPROCS`限制核数。

### 开启SELinux
SELinux必须开启，配置文件`/etc/selinux/config`
```bash
SELINUX=enforcing
SELINUXTYPE=targeted
```

### 使用OverlayFS文件系统

### 节点和master必须同步时间
ansible的环境变量设置为`openshift_clock_enabled=true`

### 有些容器是previlige, 可以访问docker守护进程。
这就等于这个容器对所有容器和镜像有root权限，openshift使用[security context constraints](https://docs.openshift.org/latest/architecture/additional_concepts/authorization.html#security-context-constraints) 来控制容器的权限。

### 容器的DNS
由于docker容器不会认宿主机的/etc/hosts, 所以所有节点都要安装dnsmasq, 容器访问宿主机的dns，宿主机再查询dns nameserver. ansible剧本需要
```bash
NM_CONTROLLED=yes  ##使用network manager
openshift_use_dnsmasq=true
```
- 默认容器内的域名解析按如下顺序进行：
1 使用宿主机的/etc/resolv.conf解析
2 容器内`/etc/origin/node/node-config.yaml`有一条宿主机的IP作为dnsIP
3 如果没有设置dnsIP， 默认值是kubernetes service IP。也就是pod中`/etc/resolv.conf`的第一个nameserver.
- 宿主机使用DNS
宿主机的域名解析配置取决于是否开启DHCP动态主机地址分配。如果没有开启使用静态ip地址，需要把DNS nameserver加入到NetworkManager;如果启用DHCP, NetworkManager会根据DHCP的配置自动分配DNS;或者在`node-config.yaml`手动加入dnsIP. 使用dig测试是否正确配置dns
```bash
$ dig <node_hostname> @<IP_address> +short
$ dig master.example.com @10.64.33.1 +short
10.64.33.100
```

### 联通的网络
- NetworkManager必须启用`NM_CONTROLLED=yes`，否则DNS配置会有问题。
- m-m, m-n节点需要开通一些端口，单节点master和多节点master的开放端口也不同。 OpenShift会自动配置一些iptables规则。
![port](http://p0iombi30.bkt.clouddn.com/%E5%B1%8F%E5%B9%95%E5%BF%AB%E7%85%A7%202018-02-05%20%E4%B8%8B%E5%8D%882.07.52.png))

### 存储
Openshift使用Kubernetes的持久卷[Persistent Volume](https://docs.openshift.org/latest/architecture/additional_concepts/storage.html#architecture-additional-concepts-storage)提供持久化存储。在安装Openshift完成后，可以根据具体需求，要求更多的存储资源。安装脚本里有提供相应的代码，支持的存储方式包括：`NFS, GlusterFS, Ceph RBD, OpenStack Cinder, AWS Elastic Block Store (EBS), GCE Persistent Disks, and iSCSI`.

### 在云上安装openshift origin需要考虑：
```
1. 配置安全组，开通部分端口
2. 需要覆盖一些参数。通过下面指令得到正确的值。
ansible-playbook  [-i /path/to/inventory] \
    ~/openshift-ansible/roles/openshift_facts/library/openshift_facts.py
```
![http://p0iombi30.bkt.clouddn.com/%E5%B1%8F%E5%B9%95%E5%BF%AB%E7%85%A7%202018-02-05%20%E4%B8%8B%E5%8D%884.07.50.png](http://p0iombi30.bkt.clouddn.com/%E5%B1%8F%E5%B9%95%E5%BF%AB%E7%85%A7%202018-02-05%20%E4%B8%8B%E5%8D%884.07.50.png)

### 如果安装容器化的GlusterFS,需要考虑额外的一些配置
[https://docs.openshift.org/latest/install_config/install/prerequisites.html#prereq-containerized-glusterfs-considerations](https://docs.openshift.org/latest/install_config/install/prerequisites.html#prereq-containerized-glusterfs-considerations)
