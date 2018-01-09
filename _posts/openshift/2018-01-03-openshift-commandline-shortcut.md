---
layout: post
title: Openshift Commandline Shortname
---

#### 创建新的project
`oc new-project postgres --display-name='postgres' --description='postgres'`
#### 切换project
`oc project myproject`

#### 创建资源. 从json(yml)生成一个OpenStack可以用模板。
```bash
    oc create -f Filename.json(.yml)
    oc process -f file.json|oc create -f -  ##处理模板生产构建配置，然后创建资源
```

#### 创建app， 就是部署
```bash
oc new-app (IMAGE | IMAGESTREAM | TEMPLATE | PATH | URL ...) [options]
oc new-app --name=dbinit --strategy=docker https://github.com/devops-with-openshift/liquibase-example.git  ##将会从这个仓库拉代码，build on Dockerfile
```

### 修改一个资源的配置
```bash
oc patch dc postgresql -p '{"spec":{"strategy":{"type":"Recreate"}}}'
```

### 设置应用配置
```bash
oc set env dc postgresql POSTGRESQL_ADMIN_PASSWORD=password
```

### 展示pod资源的信息
`oc get pods`

### 将模板转化为资源
`oc process -f template.json| oc create -f -`

### 
#### 简写对应
![http://p0iombi30.bkt.clouddn.com/%E5%B1%8F%E5%B9%95%E5%BF%AB%E7%85%A7%202018-01-03%20%E4%B8%8B%E5%8D%883.07.32.png](http://p0iombi30.bkt.clouddn.com/%E5%B1%8F%E5%B9%95%E5%BF%AB%E7%85%A7%202018-01-03%20%E4%B8%8B%E5%8D%883.07.32.png)

#### 开启一个容器的shell
```bash
   oc get pods
   oc rsh mypod
```