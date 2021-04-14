---
keyword: [AHAS-应用限流, 注册集群]
---

# 将AHAS-应用限流接入注册集群

注册的集群接入AHAS-应用限流能为分布在各处的Kubernetes集群提供统一的管理方式。本文介绍如何通过容器服务Kubernetes版中的应用将AHAS-应用限流接入至注册的Kubernetes集群。

您已通过容器服务Kubernetes版接入一个注册的Kubernetes集群。具体操作步骤，请参见[创建注册集群并接入本地数据中心集群](/cn.zh-CN/Kubernetes集群用户指南/多云混合云/注册集群管理/创建注册集群并接入本地数据中心集群.md)。

## 操作步骤

1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。

2.  在控制台左侧导航栏中，选择**市场** \> **应用目录**。

3.  在应用目录页面单击**阿里云应用**页签，选中**ack-ahas-sentinel-pilot**应用。

    **阿里云应用**包含较多应用，您可在页面上角搜索**ack-ahas-sentinel-pilot**，支持关键字搜索。

4.  在**应用目录 - ack-ahas-sentinel-pilot**页面右侧**创建**区域，选择目标集群，然后单击**创建**。

    ![cluster_ahas_sentinel_pilot_02](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/2306659951/p86637.png)

    |参数|描述|备注|
    |--|--|--|
    |region\_id|    -   如果集群和VPC之间有专线，该参数为专线连接的region。
    -   如果集群和VPC之间没有专线，该参数填入**cn-public**
|根据所选集群自动生成。|
    |cluster\_id|您的集群ID。|
    |cluster\_name|您的集群名称。|


## 结果验证

验证安装是否成功，请参见[步骤二：为Java应用开启AHAS应用防护](/cn.zh-CN/应用防护/接入应用/接入JAVA应用/接入容器服务Kubernetes版应用.md)。

如果集群和VPC之间没有专线，即**region\_id**参数填写的是**cn-public**，需要在应用的`annotations`里加入**ahasLicenseKey**。有关**ahasLicenseKey**获取方法，请参见[查看License](/cn.zh-CN/应用防护/参考信息/查看License.md)。

**相关文档**  


[注册集群概述](/cn.zh-CN/Kubernetes集群用户指南/多云混合云/注册集群管理/注册集群概述.md)

