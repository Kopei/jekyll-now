---
layout: post
title: Openshift Template
---

### Openshift的模板
定义：模板是一组可以被参数化的对象，这组对象被处理后可以被用于创建服务，构建配置，部署配置。

### 模板代码例子
```yaml
apiVersion: v1
kind: Template
metadata:
  name: redis-template #1
  annotations:
    description: "Description"  #2
    iconClass: "icon-redis"  #3
    tags: "database,nosql"  #4
objects:   #5
- apiVersion: v1
  kind: Pod
  metadata:
    name: redis-master
  spec:
    containers:
    - env:
      - name: REDIS_PASSWORD
        value: ${REDIS_PASSWORD}   #6
      image: dockerfile/redis
      name: master
      ports:
      - containerPort: 6379
        protocol: TCP
parameters:  #7
- description: Password used for Redis authentication
  from: '[A-Z0-9]{8}'   
  generate: expression
  name: REDIS_PASSWORD
labels:      
  redis: master
```
1. 模板名
2. 可选描述
3. 展示的icon（搜索"openshift-logos-icon"）
4. 这个模板的tag
5. 模板将会创建的对象
6. 模板处理的时候输入参数
7. 模板参数
8. 任意产生表达式
9. 所以对象将被打上标签