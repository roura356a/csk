---
keyword: [GPU XID, Kubernetes事件中心, 监控告警, 异常]
---

# 监控GPU异常

本文介绍如何通过Kubernetes事件中心对GPU Xid错误进行监控告警，帮助您实时了解和诊断NVIDIA驱动程序的错误报告。

-   [创建托管GPU集群](/cn.zh-CN/Kubernetes集群用户指南/GPU/NPU/创建异构计算集群/创建托管GPU集群.md)或者[创建专有GPU集群](/cn.zh-CN/Kubernetes集群用户指南/GPU/NPU/创建异构计算集群/创建专有GPU集群.md)
-   [创建并使用Kubernetes事件中心](/cn.zh-CN/应用中心（App）/K8S事件中心/创建并使用Kubernetes事件中心.md)

Xid消息是来自NVIDIA驱动程序的错误报告，该报告会打印到操作系统的内核日志或事件日志中。Xid消息表明发生了一般的GPU错误，通常是由于驱动程序对GPU的编程不正确或发送给GPU的命令损坏所致。这些消息可能表示硬件问题、NVIDIA软件问题或用户应用程序问题。

GPU设备在使用中，容易发生一些Xid错误，可以配合Kubernetes事件中心，对这些Xid错误进行监控告警，及时发现并定位故障原因。

## 操作步骤

1.  进入日志服务K8s事件中心页面。

    具体操作步骤，请参见[创建并使用Kubernetes事件中心](/cn.zh-CN/应用中心（App）/K8S事件中心/创建并使用Kubernetes事件中心.md)。

2.  在K8s事件中心左侧导航栏中，单击目标集群，然后单击**事件总览**。

    在事件总览页面查看GPU Xid告警统计信息及Xid错误的详细内容。

    ![xid](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/8875659951/p129877.png)

3.  在目标集群下拉菜单中，单击**告警配置**。

4.  单击**添加通知方式**，在**添加通知方式**面板，配置通知方式，然后单击**确定**。

    可以根据需要选择短信、邮件、钉钉等接收通知，再自定义通知内容，下图以短信为例。

    ![短信](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/8875659951/p129869.png)

5.  通知方式配置完成后，在全部告警事件页面右上角单击**修改**，选择**K8s GPU Xid告警**，并在**K8s GPU Xid告警**下拉框中选择**短信**。

    ![配置 xid](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/8875659951/p129887.png)

6.  在全部告警事件页面，单击**保存**。

    触发告警后，您会收到阿里云发出的告警短信。


