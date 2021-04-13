---
keyword: [AHAS-架构感知, 注册集群]
---

# 将AHAS-架构感知接入注册集群

注册的集群接入AHAS-架构感知能为分布在各处的Kubernetes集群提供统一的管理方式。本文介绍如何通过容器服务Kubernetes版中的应用将AHAS-架构感知接入至注册的Kubernetes集群。

您已通过容器服务Kubernetes版接入一个注册的Kubernetes集群。具体操作步骤，请参见[创建注册集群并接入本地数据中心集群](/cn.zh-CN/Kubernetes集群用户指南/多云混合云/混合集群/创建注册集群并接入本地数据中心集群.md)。

## 操作步骤

1.  查看AHAS产品的License，备用。

    查看AHAS产品License的具体步骤，请参见[查看License](/cn.zh-CN/应用防护/参考信息/查看License.md)。

2.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。

3.  在控制台左侧导航栏中，选择**市场** \> **应用目录**。

4.  在**应用目录**页面单击**阿里云应用**页签，选中**ack-ahas-pilot**应用。

    **阿里云应用**包含较多应用，您可在页面右上角搜索**ack-ahas-pilot**，支持关键字搜索。

5.  在**应用目录 - ack-ahas-pilot**页面右侧**创建**区域，选择目标集群。

6.  在**应用目录 - ack-ahas-pilot**页面单击**参数**页签，设置相应的参数，然后单击右侧**创建**区域的**创建**。

    ![cluster_ahas_pilot_02](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/1306659951/p86606.png)

    |参数|描述|备注|
    |--|--|--|
    |region\_id|    -   如果集群和VPC之间有专线，该参数为专线连接的地域。
    -   如果集群和VPC之间没有专线，该参数填入**cn-public**
|根据选择的集群自动生成。|
    |cluster\_id|您的集群ID。|
    |cluster\_name|您的集群名称。|
    |license|填写您在AHAS控制台中查看到的License。|无|


部署完成后您可以在[AHAS控制台](https://ahas.console.aliyun.com)中查看集群的拓扑图。

如果集群和VPC之间没有专线，即**region\_id**参数填写的是**cn-pubulic**

拓扑图示例如下：

![cluster_ahas_pilot_06](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/1306659951/p86632.png)

**相关文档**  


[注册集群概述](/cn.zh-CN/Kubernetes集群用户指南/多云混合云/混合集群/注册集群概述.md)

