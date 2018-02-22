---
layout: post
title: Openshift的网路
comment: true
---

> https://docs.openshift.org/latest/architecture/networking/networking.html

### 前言
openshift的网路架构是建立在kubernetes的service上的。K8S的[Service](https://kubernetes.io/docs/concepts/services-networking/service/)
是一组pods和访问这些pods的逻辑抽象，service解耦了下游pods的网路变化。由于Service也会变化，所以openshift在master运行[skyDNS](https://github.com/skynetservices/skydns)来解决service网路的变化。

### SDN
Software Define Networking软件定义集群网络使openshift的pods相互能够通信，SDN使用Open vSwitch(OVS)来管理配置网路。有三种SDN插件可以配置pods网路：
- ovs-subnet, 提供所有的pods直接相互通信
- ovs-multitenant, 能够提供项目级别的pods和service隔离。每一个项目都有一个VNID, 这个网路内项目之间的pods不能相互通信。VNID 0是一个例外，可以和所有pods通信，可以提供负载均衡等服务。
- ovs-networkpolicy, 这里用户可以自己定义网路规则。
在master上，Openshift SDN分配网络给运行节点并且注册到etcd, 可以定义的网段有10.128.0.0/14, node节点定义在10.128.0.0/23。master节点是不能访问集群网络的，除非它也作为运行节点。

