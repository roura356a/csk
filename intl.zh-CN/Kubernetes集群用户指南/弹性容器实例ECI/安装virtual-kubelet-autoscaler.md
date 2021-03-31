---
keyword: [virtual-kubelet-autoscaler, 安装virtual-kubelet-autoscaler]
---

# 安装virtual-kubelet-autoscaler

virtual-kubelet-autoscaler是阿里云提供的Kubernetes Autoscaler插件，在Pod进行调度时，如果没有匹配的node节点（例如：已有的ECS work节点资源不足），会由virtual-kubelet-autoscaler进行接管，把调度失败的Pod进行重新调度，调度到ECI（虚拟节点）上。

-   创建一个Kubernetes托管版集群。具体操作，请参见[创建Kubernetes托管版集群](/intl.zh-CN/Kubernetes集群用户指南/集群/创建集群/创建Kubernetes托管版集群.md)。
-   已在集群上部署了一个虚拟节点。具体操作，请参见[通过部署ACK虚拟节点组件创建ECI Pod](/intl.zh-CN/Kubernetes集群用户指南/弹性容器实例ECI/通过部署ACK虚拟节点组件创建ECI Pod.md)。

## 部署virtual-kubelet-autoscaler

1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。

2.  在控制台左侧导航栏中，选择**市场** \> **应用目录**。

3.  在**应用目录**页面**阿里云应用**页签，然后选中**ack-virtual-kubelet-autoscaler**应用。

    **阿里云应用**包含较多应用，您可在页面右上角搜索**ack-virtual-kubelet-autoscaler**，支持关键字搜索。

4.  在**应用目录 - ack-virtual-kubelet-autoscaler**页面右侧的**创建**区域，选择目标集群，单击**创建**。

    ![创建](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/8106659951/p132495.png)


1.  在集群列表页面中，单击目标集群名称或者目标集群右侧**操作**列下的**详情**。
2.  在集群管理页左侧导航栏中，选择**应用** \> **Helm**，可以找到ack-virtual-kubelet-autoscaler的应用。

