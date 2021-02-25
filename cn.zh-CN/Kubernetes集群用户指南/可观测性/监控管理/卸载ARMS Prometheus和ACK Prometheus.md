# 卸载ARMS Prometheus和ACK Prometheus

本文介绍如何卸载ARMS Prometheus和ACK Prometheus。

## 操作步骤

您可以通过控制台卸载ARMS Prometheus和ACK Prometheus，卸载后不会有数据残留。卸载ARMS Prometheus和ACK Prometheus的操作类似，以下以卸载ARMS Prometheus为例。

1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。

2.  在控制台左侧导航栏中，单击**集群**。

3.  在集群列表页面中，单击目标集群名称或者目标集群右侧**操作**列下的**详情**。

4.  在集群管理页左侧导航栏中，选择**应用** \> **Helm**。

5.  在Helm页面找到**发布名称**为arms-prom的组件，然后单击arms-prom操作列的**删除**。

    **说明：** 如果您卸载的是ACK Prometheus，您需要在Helm页面找到**发布名称**为ack-prometheus-operator的组件，单击ack-prometheus-operator组件**操作**列的**删除**。

6.  在**删除应用**对话框中选中**清除发布记录**，然后单击**确定**。


