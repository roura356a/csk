# 将arms-prometheus接入注册集群

注册的集群接入arms-prometheus能为分布在各处的Kubernetes集群提供统一的管理方式。本文介绍如何通过容器服务Kubernetes版中的应用将arms-prometheus接入至注册的Kubernetes集群。

您已通过容器服务Kubernetes版接入一个注册的Kubernetes集群。具体操作步骤，请参见[创建注册集群并接入本地数据中心集群](/cn.zh-CN/Kubernetes集群用户指南/多云混合云/混合集群/创建注册集群并接入本地数据中心集群.md)。

## 操作步骤

1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。

2.  在控制台左侧导航栏中，选择**市场** \> **应用目录**。

3.  在**应用目录**页面单击**阿里云应用**页签，选中**ack-arms-prometheus**应用。

    **阿里云应用**包含较多应用，您可在页面右上角搜索**ack-arms-prometheus**，支持关键字搜索。

4.  在应用目录 - ack-arms-prometheus页面右侧**创建**区域，选择目标集群，并单击**创建**。

    ![镜像](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/0306659951/p132528.png)

    |参数|描述|备注|
    |--|--|--|
    |cluster\_id|您的集群ID。|根据选择的集群自动生成。|
    |uid|您的阿里云账号用户名。**说明：** userID需要带引号。 |
    |region\_id|应用实时监控服务ARMS所在的地域。|

    **说明：**

    -   如果您的集群和专有网络VPC之间有专线，专线会被自动使用。
    -   如果您是通过公网注册的外部集群，需要删除镜像参数中的vpc。例如，删除vpc后的镜像参数为registry.cn-hangzhou.aliyuncs.com/arms-docker-repo/arms-prom-operator:v0.1。

安装完成后，可查看监控数据和定义告警规则，详情请参见[t855956.dita\#concept\_1062555/section\_hg4\_crq\_33b](t855956.dita#concept_1062555/section_hg4_crq_33b)和[创建Prometheus监控报警](/cn.zh-CN/大盘和报警/创建报警.md)。

**相关文档**  


[注册集群概述](/cn.zh-CN/Kubernetes集群用户指南/多云混合云/混合集群/注册集群概述.md)

