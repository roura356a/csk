---
keyword: [自定义组件参数, Pro集群, 管控面, 托管组件]
---

# 自定义ACK Pro集群的管控面参数

为了满足生产环境调整管控面参数的需求，ACK Pro托管版集群为您提供了管控面参数自定义功能。您可以根据需要修改托管组件Kube API Server和Kube Controller Manager（KCM）的参数。本文介绍如何自定义ACK Pro托管版集群的管控面参数。

## 注意事项

为了确保顺利完成管控面的参数修改，请仔细阅读以下注意事项：

-   修改参数之后，管控面会进行重启，建议您选择业务低峰进行参数修改操作。
-   修改参数之后，您输入的参数会覆盖ACK Pro提供的默认参数，从而导致默认参数失效。
-   为了保证管控面的稳定性，当前ACK Pro仅支持自定义部分参数。
-   请确保您输入的参数正确且完整，错误参数可能会导致管控面启动失败。关于参数设置的详细信息，请参见[kube-apiserver](https://kubernetes.io/docs/reference/command-line-tools-reference/kube-apiserver/)和[kube-controller-manager](https://kubernetes.io/docs/reference/command-line-tools-reference/kube-controller-manager/)。

## 自定义Pro集群的管控面参数

1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。

2.  在控制台左侧导航栏中，单击**集群**。

3.  在集群列表页面中，单击目标集群名称或者目标集群右侧**操作**列下的**详情**。

4.  在集群管理页左侧导航栏中，选择**运维管理** \> **组件管理**。

5.  下文以修改Kube API Server组件为例，说明如何修改组件参数。
6.  在**核心组件**区域，单击目标组件右下方的**配置**。

7.  在**kube-apiserver参数配置**对话框中，输入您的自定义参数，然后单击**确定**。

    **说明：** 请确保您输入的参数完整性和正确性。目前ACK Pro集群只支持修改Kube API Server和KCM组件的参数。关于Kube API Server和KCM参数的具体格式和合法值，请参见[kube-apiserver](https://kubernetes.io/docs/reference/command-line-tools-reference/kube-apiserver/)和[kube-controller-manager](https://kubernetes.io/docs/reference/command-line-tools-reference/kube-controller-manager/)（需选择对应的Kubernetes版本）。


## 默认参数列表

当您进入组件的参数配置操作之后，组件原有的默认参数会被覆盖。您可以参考以下表格将参数恢复为默认值。

|Kubernetes版本|组件名|参数|默认值|
|------------|---|--|---|
|1.16|kube-apiserver|ServiceNodePortRange|30000-32767|
|EnableAdmissionPlugins|-   如果开启了PodSecurityPolicy：默认值为`NodeRestriction,PodSecurityPolicy`
-   如果关闭了PodSecurityPolicy：默认值为`NodeRestriction` |
|kube-controller-manager|HorizontalPodAutoscalerSyncPeriod|15s|
|1.18|kube-apiserver|ServiceNodePortRange|30000-32767|
|EnableAdmissionPlugins|-   如果开启了PodSecurityPolicy：默认值为`NodeRestriction,PodSecurityPolicy`
-   如果关闭了PodSecurityPolicy：默认值为`NodeRestriction` |
|kube-controller-manager|HorizontalPodAutoscalerSyncPeriod|15s|

