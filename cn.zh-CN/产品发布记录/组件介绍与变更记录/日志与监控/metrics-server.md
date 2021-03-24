---
keyword: [metrics-server, 变更版本]
---

# metrics-server

metrics-server是阿里云容器服务Kubernetes版基于社区开源监控组件进行改造和增强的监控采集和离线组件，并提供Metrics API进行数据消费，提供HPA的能力。

社区开源监控组件详细介绍请参见[社区开源监控组件](https://github.com/kubernetes-sigs/metrics-server)。

## 变更记录

v0.2.2-b4bf266-aliyun

-   支持采集Windows节点池与Linux节点池混部场景。
-   支持调整多档位的弹性灵敏度，目前支持15s、20s、30s、60s四个档位。
-   修复应用滚动发布时HPA误弹的问题。

## 问题诊断

**kubectl top pod/node全部无数据**

请按照以下方式进行预检查。

执行`kubectl get apiservice`，检查metrics-server的API Service是否正常。如果API Service不正常，则检查metrics-server的443端口与8082端口是否可以在集群中正常访问。如果无法访问，请重启metrics-server进行重试。

按上述说明检查后，没有发现问题，请按照以下工单模板[提交工单](https://selfservice.console.aliyun.com/ticket/createIndex)。

工单模板

1.  API Service是否正常。

    是

2.  metrics-server 443与8082端口是否可达。

    是

3.  提供集群ID。

**kubectl top pod/node部分无数据**

请按照以下方式进行预检查。

-   检查是特定的Node上所有Pod无数据，还是特定的Pod无数据。如果是特定的Node上所有Pod无数据，请检查节点是否存在时区漂移，可以通过ntpdate进行时区校验。
-   检查metrics-server Pod到特定的Node的10255端口的网络连通性。

按上述说明检查后，没有发现问题。请按照以下工单模板[提交工单](https://selfservice.console.aliyun.com/ticket/createIndex)。

工单模板

1.  单个Node上的Pod是否全部无数据。

    是

2.  节点时区是否有漂移。

    无

3.  metrics-server到指定节点的连通性是否可达。

    是


**HPA无法获取metrics数据**

请按照以下方式进行预检查。

检查对应的Pod执行`kubectl top pod pod-id`的结果。如果数据异常，请参考上述kubectl top pod/node部分无数据和kubectl top pod/node全部无数据的检查方法进行检查。

按上述说明检查后，没有发现问题。请按照以下工单模板[提交工单](https://selfservice.console.aliyun.com/ticket/createIndex)。

工单模板

1.  监控数据是否有异常。

    无

2.  执行`kubectl describe hpa hpa-name`，提交元数据信息。

**滚动发布时HPA额外弹出多余的Pod**

请按照以下方式进行预检查。

检查metrics-server是否升级到了最新的版本。如果版本没有问题，在kube-system下的metrics-server配置启动参数。

```
--metric-resolution=15s
--enable-hpa-rolling-update-skipped=true
```

按上述说明检查后，没有发现问题。请按照以下工单模板[提交工单](https://selfservice.console.aliyun.com/ticket/createIndex)。

工单模板

1.  检查metrics-server的版本是否为最新。

    是

2.  检查配置参数是否已经增加防误弹能力。

    是

3.  执行`kubectl describe hpa hpa-name`，提交HPA的描述。

