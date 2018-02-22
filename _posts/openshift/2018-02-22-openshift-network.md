---
layout: post
title: Openshift的网路
comment: true
---

> https://docs.openshift.org/latest/architecture/networking/networking.html

### 杂言
openshift的网路架构是建立在kubernetes的service上的。K8S的[Service](https://kubernetes.io/docs/concepts/services-networking/service/)
是一组pods和访问这些pods的逻辑抽象， 通过[Label Selector](https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/#label-selectors)可以找到它们。