---
layout: post
title: Openshift Deployment
---

> _version v3.7_; _OS Mac_

### 安装Openshift AllinOne
- 安装oc cli. 地址：[https://github.com/openshift/origin/releases](https://github.com/openshift/origin/releases)
```bash
wget -c https://github.com//openshift/origin/releases/download/v3.7.0/openshift-origin-client-tools-v3.7.0-7ed6862-mac.zip
tar -xvf openshift-origin-client-tools-v3.7.0-7ed6862-mac.zip
mv openshift-origin-client-tools-v3.7.0-7ed6862-mac/oc /usr/local/bin
```
- 启动Openshift, 使用预置配置, 挂出数据
```bash
oc cluster up --host-data-dir='$REPLACE/oc/profiles/Devops/data' --host-config-dir='$REPLACE/oc/profiles/Devops/config' --use-existing-config
```
- 访问`https://127.0.0.1:8443/login`

### Openshift的部署叫replication controller.
- 多种部署策略，支持模板配置部署方式
  - Rolling openshift默认策略
    1. 执行pre lifecycle hook
    2. 通过surge配置开始部署
    3. 通过最大不可用配置缩减旧的部署
    4. 重复扩展操作，直到新的部署达到想要的复制数，旧的部署慢慢将为0
    5. 执行post lifecycle hook
  - 触发部署方式Trigger，支持配置改变，镜像改变，github, webhook
    ```bash
    oc describe dc myapp ##查看myapp配置
    oc set triggers dc myapp  ## 查看
    oc set triggers dc myapp --remove-all  ##去除所有triggers, 但是config trigger auto=false
    oc set triggers dc myapp --from-config  ##更新config自动部署
    oc set triggers dc myapp --from-config --remove ## 删除config
    ``` 
    ```bash
    oc import-image docker.io/busybox:latest --confirm ##把上游的镜像仓库镜像加入本地命名空间
    oc set triggers dc myapp --from-image=welcome/busybox:latest --containers=myapp ##image 更新部署
    ```
  - Recreate 策略
    1. 执行pre lifecycle hook
    2. 慢慢关闭上一个部署
    3. 执行中间lifecycle hook
    4. 部署新的应用
    5. 执行post lifecycle hook
    ```bash
    oc delete project welcome
    oc new-project welcome --display-name='Welcom' --description='Welcome'
    oc new-app devopswithopenshift/welcome:latest --name=myapp
    oc patch dc myapp -p '{"spec":{"strategy":{"type":"Recreate"}}}'
    oc set probe dc myapp --readiness --open-tcp=8080 --initial-delay-seconds=5 --timeout-seconds=5
    oc set probe dc myapp --liveness -- echo ok
    oc expose svc myapp --name welcome
    ```
  - Custom 订制
  - lifecycle hooks. Openshift会另外起一个容器用于运行pre, mid, post hook.
    - 当前支持pod-based类型hook, execNewPod字段定义
    - 
- 支持事件触发部署
- 自定义策略切换部署方式
- 回滚
- 可扩展

### 针对openshift的特性，需要考虑应用的架构
- 比如： session调整。是服务器session还是无状态，客户端缓存。
