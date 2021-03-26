---
keyword: [部署Eventing, 事件驱动框架Eventing]
---

# 部署Eventing

ASK Knative中提供了事件驱动框架Eventing。Eventing组件针对Serverless事件驱动模式做了一套完整的设计，提供了事件的接入、触发等一整套事件管理的能力。本文介绍如何在ASK Knative中部署Eventing。

-   [创建Serverless Kubernetes集群](/intl.zh-CN/Serverless Kubernetes集群用户指南/快速入门/创建Serverless Kubernetes集群.md)
-   [开启Knative](/intl.zh-CN/Serverless Kubernetes集群用户指南/Knative/Knative组件管理/开启Knative.md)

## 操作步骤

1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。

2.  在控制台左侧导航栏中，单击**集群**。

3.  在集群列表页面中，单击目标集群名称或者目标集群右侧**操作**列下的**详情**。

4.  在集群管理页左侧导航栏中，选择**应用** \> **Knative**。

5.  在**组件管理**页签**核心组件**区域单击Eventing**操作**列的**部署**。

6.  在**部署Eventing**对话框中提示**当前集群未开启PrivateZone，请先开启PrivateZone**，请按以下步骤操作，开启PrivateZone。

    **说明：** 在**部署Eventing**对话框中未提示**当前集群未开启PrivateZone，请先开启PrivateZone**，可跳过此步骤，直接执行步骤[7](#step_msv_ok7_xll)。

    1.  执行以下命令，编辑eci-profile文件。

        ```
        kubectl -n kube-system edit configmap eci-profile
        ```

    2.  修改`privatezone`参数值为`true`，保存并退出eci-profile文件。

        ```
        apiVersion: v1
        data:
          ...
          privatezone: "true"
          ...
        kind: ConfigMap
        metadata:
          name: eci-profile
          namespace: kube-system
        ```

7.  在**部署Eventing**对话框中单击**确定**。


## 执行结果

部署Eventing完成后，在**组件管理**页签，可以看到Eventing组件状态为**已部署**。

![eventing](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/7020039061/p207749.png)

