---
layout: post
title: Kubernetes的PersistentVolume和StorageClass
---
> [https://v1-8.docs.kubernetes.io/docs/concepts/storage/persistent-volumes/](https://v1-8.docs.kubernetes.io/docs/concepts/storage/persistent-volumes/)


### 前言
K8S管理员使用`PersistentVolume`来抽象底层存储，然后用户使用`PersistentVolumeClaim`来消耗存储资源。`PersistentVolume`有和pod不同的生命周期，`PersistentVolumeClaim`声明对应存储资源的请求，可以在这里声明存储的用量和读写模式（将来可能还有性能要求），但是除了用量和模式，用户可能还有颗粒度更细的存储要求，这时候就需要`StorageClass`来分类存储资源了。

### 配置PV
PV可以被静态和动态的方式配置
- **静态方式** 就是管理员手动配置存储
- **动态方式** 管理员配置一个`StorageClass`，设置`DefaultStorageClass`, 然后PVC请求`StorageClass`就可以使用动态pv了。
- binding绑定。当用户对API做PVC后，master控制逻辑会找到匹配的PV, 然后将它们绑定。如果是静态供应的pv, master可能会找不到用量完全合适的pv, 这个时候会使用较大的pv。如果没有合适的匹配, 那么就不会绑定。
- 用法。 pvc的用法还是很简单，见下面代码
```yaml
kind: Pod
apiVersion: v1
metadata:
  name: mypod
spec:
  containers:
    - name: myfrontend
      image: dockerfile/nginx
      volumeMounts:
      - mountPath: "/var/www/html"
        name: mypd
  volumes:
    - name: mypd
      persistentVolumeClaim:
        claimName: myclaim
```
- PV回收机制
当用户删除PVC后，PV有三种PV回收机制，`retain`, `recycle`, `delete`
  - retain 保留。删除PVC不会删除PV, 管理员需要手动删除PV,然后删除对应在存储服务上数据。
  - recycle 回收。这个策略需要volume插件支持，基本上会对volume做`rm -rf /thevolume`操作，然后这个pv可以被新的pvc使用。
  - delete 删除。需要volume插件支持。同时删除pv和对应外部存储。如果pv是动态提供的，那么会继承`StorageClass`的回收策略，默认是delete。






