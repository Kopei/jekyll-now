---
layout: post
title: Openshift Commandline Shortname
---

#### 切换project
`oc project myproject`

#### 创建pod
`oc create -f Filename.json(yml)`

#### 创建app
```bash
oc new-app (IMAGE | IMAGESTREAM | TEMPLATE | PATH | URL ...) [options]
oc new-app --name=dbinit --strategy=docker https://github.com/devops-with-openshift/liquibase-example.git  ##将会从这个仓库拉代码，build on Dockerfile
```

#### 简写对应
![http://p0iombi30.bkt.clouddn.com/%E5%B1%8F%E5%B9%95%E5%BF%AB%E7%85%A7%202018-01-03%20%E4%B8%8B%E5%8D%883.07.32.png](http://p0iombi30.bkt.clouddn.com/%E5%B1%8F%E5%B9%95%E5%BF%AB%E7%85%A7%202018-01-03%20%E4%B8%8B%E5%8D%883.07.32.png)

#### 开启一个容器的shell
```bash
   oc get pods
   oc rsh mypod
```