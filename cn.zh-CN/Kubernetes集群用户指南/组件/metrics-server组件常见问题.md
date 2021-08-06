---
keyword: metrics-server组件常见问题
---

# metrics-server组件常见问题

metrics-server是离线监控数据组件，提供查看集群离线监控数据功能。本文介绍使用metrics-server组件时遇到的常见问题。

## kubectl top pod/node全部无数据

请按照以下方式进行预检查。

1.  执行以下命令，检查metrics-server的API Service是否正常。

    ```
    kubectl get apiservices
    ```

    ![metris](https://help-static-aliyun-doc.aliyuncs.com/assets/img/zh-CN/7232428261/p302016.png)

    返回结果中`v1beta1.metrics.k8s.io`显示`True`，说明metrics-server的API Service是正常的。

2.  如果metrics-server的API Service不正常，在metrics-server所在的Node节点上执行以下命令，检查metrics-server的443端口与8082端口是否可以在集群中正常访问。

    ```
    curl -v 127.0.0.1:8082/apis/metrics/v1alpha1/nodes
    ```

    执行以上命令，能正常返回数据，说明metrics-server的443端口与8082端口可以在集群中正常访问。

3.  如果metrics-server的443端口与8082端口无法在集群中正常访问，重启metrics-server。

    您可以通过删除metrics-server的Pod的方式重启metrics-server。

    1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。

    2.  在控制台左侧导航栏中，单击**集群**。

    3.  在集群列表页面中，单击目标集群名称或者目标集群右侧**操作**列下的**详情**。

    4.  在集群管理页左侧导航栏中，选择**工作负载** \> **无状态**。

    5.  在**无状态**页面顶部设置**命名空间**为kube-system，单击metrics-server。

    6.  在**容器组**页签下选择metrics-server的Pod**操作**列下的**更多** \> **删除**。

    7.  在**提示**对话框单击**确定**。


按上述说明检查后，没有发现问题，请按照以下工单模板[提交工单](https://selfservice.console.aliyun.com/ticket/createIndex)。

工单模板

1.  API Service是否正常？

    是

2.  metrics-server 443与8082端口是否可达？

    是

3.  提供集群ID。

## kubectl top pod/node部分无数据

请按照以下方式进行预检查。

-   检查是特定的Node上所有Pod无数据，还是特定的Pod无数据。如果是特定的Node上所有Pod无数据，请检查节点是否存在时区漂移，可以通过NTP服务器的date命令进行时区校验。
-   检查metrics-server Pod到特定的Node的10255端口的网络连通性。

按上述说明检查后，没有发现问题。请按照以下工单模板[提交工单](https://selfservice.console.aliyun.com/ticket/createIndex)。

工单模板

1.  单个Node上的Pod是否全部无数据？

    是

2.  节点时区是否有漂移？

    无

3.  metrics-server到指定节点的连通性是否可达？

    是


## HPA无法获取metrics数据

请按照以下方式进行预检查。

检查对应的Pod执行`kubectl top pod pod-id`的结果。如果数据异常，请参考上述kubectl top pod/node部分无数据和kubectl top pod/node全部无数据的检查方法进行检查。

按上述说明检查后，没有发现问题。请按照以下工单模板[提交工单](https://selfservice.console.aliyun.com/ticket/createIndex)。

工单模板

1.  监控数据是否有异常？

    无

2.  执行`kubectl describe hpa hpa-name`，提交元数据信息。

## 滚动发布时HPA额外弹出多余的Pod

请按照以下方式进行预检查。

检查metrics-server是否升级到了最新的版本。如果版本没有问题，在kube-system命名空间下的metrics-server配置启动参数。

```
--metric-resolution=15s
--enable-hpa-rolling-update-skipped=true
```

按上述说明检查后，没有发现问题。请按照以下工单模板[提交工单](https://selfservice.console.aliyun.com/ticket/createIndex)。

工单模板

1.  检查metrics-server的版本是否为最新？

    是

2.  检查配置参数是否已经增加防误弹能力？

    是

3.  执行`kubectl describe hpa hpa-name`，提交HPA的描述。

