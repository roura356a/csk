---
keyword: 公网访问集群API Server
---

# 通过公网访问集群API Server

您可以使用弹性公网IP EIP（Elastic IP Address）暴露ASK集群的API Server。使用EIP暴露集群API Server后，您将获得从公网访问集群API Server的能力。本文介绍如何在创建集群时绑定EIP。

## 创建集群时绑定EIP

您可以在创建集群的时候通过选中**使用EIP暴露API Server**获得从公网访问集群API Server的能力。创建集群的详细步骤请参见[创建Serverless Kubernetes集群](/cn.zh-CN/Serverless Kubernetes集群用户指南/快速入门/创建Serverless Kubernetes集群.md)。

![EIP](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/3475659951/p103507.png)

## 后续绑定EIP

如果您创建集群时未选择为API Server开放公网，您可以通过以下步骤为集群关联EIP。

1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。

2.  在控制台左侧导航栏中，单击**集群**。

3.  在控制台左侧导航栏中，选择**集群** \> **集群**。

4.  在集群列表页面中，单击目标集群名称或者目标集群右侧**操作**列下的**详情**。

5.  在集群列表页面中，单击目标集群名称或者目标集群右侧**操作**列下的**管理**。

6.  在**基本信息**页签的**集群信息**区域，单击**绑定EIP**。

    ![绑定EIP](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/3475659951/p103524.png)


绑定EIP后，您还可以参见上述步骤更换EIP或者解绑EIP。

![更换EIP](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/3475659951/p103535.png)

