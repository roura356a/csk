---
keyword: [监控集群侧存储资源, storage-operator组件安装]
---

# 使用storage-operator组件监控集群侧存储资源

在阿里云ACK集群中通过部署storage-operator组件可以将PV及PVC不同状态的分布、变化趋势、异常的PV和PVC的详细信息展示出来。本文介绍如何通过storage-operator组件监控集群侧存储资源。

您已创建一个ACK集群。具体操作，请参见[创建Kubernetes托管版集群](/cn.zh-CN/Kubernetes集群用户指南/集群/创建集群/创建Kubernetes托管版集群.md)。

## 步骤一：安装storage-operator组件

1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。

2.  在控制台左侧导航栏中，单击**集群**。

3.  在集群列表页面中，单击目标集群名称或者目标集群右侧**操作**列下的**详情**。

4.  在集群管理页左侧导航栏中，选择**运维管理** \> **组件管理**。

5.  单击存储页签，在storage-operator组件区域，单击**安装**。


## 步骤二：查看监控信息

目前该监控功能仅支持PV和PVC监控，您可以通过阿里云Prometheus服务监控当前PV和PVC的使用状况。

1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。

2.  在控制台左侧导航栏中，单击**集群**。

3.  在集群列表页面中，单击目标集群名称或者目标集群右侧**操作**列下的**详情**。

4.  在集群管理页左侧导航栏中，选择**运维管理** \> **Prometheus监控**。

    若提示组件未安装，请先单击**开始安装**完成组件安装。

5.  单击**k8s csi cluster**监控大盘页签，进入csi-plugin组件集群侧的监控大盘，查看监控信息。

    ![K8s csi cluster](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/8459795161/p251927.png)

    K8s CSI Cluster监控大盘指标说明如下所示：

    |指标名称|说明|显示维度|
    |----|--|----|
    |PV Status Statistics|PV不同状态分布|数量以及占比|
    |PVC Status Statistics|PVC不同状态分布|数量以及占比|
    |PV Total Num|PV不同状态变化趋势|数量|
    |PVC Total Num|PVC不同状态变化趋势|数量|
    |Unbound PV Details|非Bound状态的PV详细信息（支持根据关键字过滤）|详细信息（time-name-status-type）|
    |Unbound PVC Details|非Bound状态的PVC详细信息（支持根据关键字过滤）|详细信息（time-name-namespace-status）|


