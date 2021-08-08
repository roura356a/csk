---
keyword: [绑定EIP, 创建集群未绑定EIP, 外网访问集群API Server]
---

# 控制集群API Server的公网访问能力

您可以使用弹性公网IP EIP（Elastic IP Address）暴露Kubernetes集群的API Server。使用EIP暴露集群API Server后，集群API Server将具有公网访问能力。您可以在创建集群时和创建集群后绑定EIP。本文介绍如何通过绑定和解绑EIP来控制集群API Server的公网访问能力。

## 创建集群时绑定EIP

您可以在创建集群的时候通过选中**使用EIP暴露API Server**获得从公网访问集群API Server的能力。创建集群的详细步骤请参见[创建Kubernetes托管版集群](/intl.zh-CN/Kubernetes集群用户指南/集群/创建集群/创建Kubernetes托管版集群.md)。

![EIP](https://help-static-aliyun-doc.aliyuncs.com/assets/img/zh-CN/3475659951/p103507.png)

## 创建集群后绑定EIP

如果您创建集群时未选择为API Server开放公网，您可以通过以下步骤为集群绑定EIP。

**说明：** 目前只有ACK标准版、ACK Pro版和ASK集群支持在创建集群后绑定EIP。

1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。

2.  在控制台左侧导航栏中，单击**集群**。

3.  在集群列表页面中，单击目标集群名称或者目标集群右侧**操作**列下的**详情**。

4.  在集群信息页面，单击**基本信息**页签，然后在**集群信息**区域，单击**绑定EIP**。

    ![绑定EIP](https://help-static-aliyun-doc.aliyuncs.com/assets/img/zh-CN/3475659951/p103524.png)

5.  选择已有EIP，单击**确定**。

    绑定完成后，API Server公网连接端点将会显示公网IP地址。


## 解绑或更换EIP

绑定EIP后，您还可以更换EIP或者解绑EIP。解绑EIP后，集群的API Server将失去公网访问能力。

![更换EIP](https://help-static-aliyun-doc.aliyuncs.com/assets/img/zh-CN/3475659951/p103535.png)

**说明：** 目前只有ACK标准版、ACK Pro版和ASK集群支持**更换EIP**和**解绑EIP**功能。

