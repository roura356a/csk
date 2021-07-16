---
keyword: [一键部署, WordPress]
---

# 通过应用市场一键部署WordPress

本文主要为您介绍如何一键部署WordPress。

已创建一个ACK集群，详情请参见[创建Kubernetes托管版集群](/cn.zh-CN/Kubernetes集群用户指南/集群/创建集群/创建Kubernetes托管版集群.md)。

**说明：** 仅支持在ACK集群中一键部署WordPress，ASK集群暂不支持。

## 操作步骤

1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。

2.  在控制台左侧导航栏选择**市场** \> **应用目录**。

3.  在应用目录页面单击**APP Hub**页签，查找**wordpress**，然后单击**wordpress 5.3.0**。

    ![wordpress](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/5010346261/p142677.png)

4.  在wordpress页面，右侧**创建**区域设置**集群**、**命名空间**、**发布名称**。

5.  单击**参数**页签，将以下两处persistence参数设置为false后单击**创建**。

    -   第一处persistence参数修改：

        ![2](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/0466459951/p142060.png)

    -   第二处persistence参数修改：

        ![1](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/0466459951/p142059.png)

6.  在控制台左侧导航栏中，单击**集群**。

7.  在集群列表页面中，单击目标集群名称或者目标集群右侧**操作**列下的**详情**。

8.  在集群管理页左侧导航栏中，选择**网络** \> **服务**。

9.  在服务页面单击WordPress的外部端点，可以看到WordPress主页。

    ![主页](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/7571346261/p142679.png)

    **说明：** 如果WordPress参数设置错误，您可以在集群管理页左侧导航栏中单击**应用** \> **Helm**，删除WordPress，再重新部署WordPress。


