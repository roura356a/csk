---
keyword: [CSI组件, Kubernetes, CSI-Plugin, CSI-Provisioner]
---

# 安装与升级CSI组件

CSI组件包括CSI-Plugin和CSI-Provisioner两部分。本文介绍在容器服务Kubernetes版Container Service for Kubernetes（ACK）中，如何管理与升级CSI-Plugin和CSI-Provisioner存储组件。

## 前提条件

-   您已创建一个大于1.14版本的ACK集群，且存储插件选择为CSI。具体操作，请参见[创建Kubernetes托管版集群](/intl.zh-CN/Kubernetes集群用户指南/集群/创建集群/创建Kubernetes托管版集群.md)。
-   您已通过kubectl连接Kubernetes集群。具体操作，请参见[通过kubectl连接Kubernetes集群](/intl.zh-CN/Kubernetes集群用户指南/集群/连接集群/通过kubectl连接Kubernetes集群.md)。

## 安装CSI-Plugin和CSI-Provisioner

在创建ACK托管版和专有版集群时除非您选择安装flexvolume组件，否则控制台将默认安装CSI-Plugin和CSI-Provisioner组件。

**验证安装**

验证CSI-Plugin和CSI-Provisioner组件是否成功部署。

-   执行以下命令，查看CSI-Plugin组件是否成功部署。

    ```
    kubectl get pod -n kube-system | grep csi-plugin
    ```

-   执行以下命令，查看CSI-Provisioner组件是否成功部署。

    ```
    kubectl get pod -n kube-system | grep csi-provisioner
    ```


## 升级CSI-Plugin和CSI-Provisioner

CSI-Plugin和CSI-Provisioner组件支持在控制台升级。

1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。

2.  在控制台左侧导航栏中，单击**集群**。

3.  在集群列表页面中，单击目标集群名称或者目标集群右侧**操作**列下的**详情**。

4.  在集群管理详情页左侧导航栏，选择**运维管理** \> **组件管理**。

5.  单击存储页签，在**csi-plugin**及**CSI-Provisioner**组件区域单击**升级**。

6.  在提示对话框中确认版本信息后单击**确定**。

    升级成功后，对应组件区域会提示升级成功，且可查看组件当前版本。


若控制台升级失败或前置检查失败，可以按照以下场景操作。

-   CSI-Plugin组件前置检查失败。
    -   若您的集群没有使用云盘、NAS或OSS类型数据卷，请手动升级CSI-Plugin组件。具体操作，请参见[CSI-Plugin升级](https://github.com/kubernetes-sigs/alibaba-cloud-csi-driver/blob/master/deploy/ack/upgrade/upgrade.md)。
    -   若您的集群使用了云盘、NAS或OSS类型数据卷，且集群为测试环境，需要选择手动升级。具体操作，请参见[CSI-Plugin升级](https://github.com/kubernetes-sigs/alibaba-cloud-csi-driver/blob/master/deploy/ack/upgrade/upgrade.md)。
    -   若您的集群使用了云盘、NAS或OSS类型数据卷，且包含关键业务数据，请[提交工单](https://workorder-intl.console.aliyun.com/console.htm)申请手动升级保障。
-   CSI-Plugin组件前置检查通过，但是升级失败。

    检查集群中节点是否都是Ready状态，如果存储在NotReady节点，需要先解决节点问题。

    若无法找到具体原因，请[提交工单](https://workorder-intl.console.aliyun.com/console.htm)申请手动升级保障。

-   控制台可以看到CSI-Plugin组件，但看不到CSI-Provisioner组件。

    因为使用了早期的CSI-Provisioner的StatefulSet部署模式，请[提交工单](https://workorder-intl.console.aliyun.com/console.htm)申请手动升级保障。

-   CSI-Provisioner组件前置检查失败。
    -   若您的集群没有使用通过StorageClass创建的云盘或NAS类型的动态数据卷，请手动升级CSI-Provisioner组件。具体操作，请参见[CSI-Provisioner升级](https://github.com/kubernetes-sigs/alibaba-cloud-csi-driver/blob/master/deploy/ack/upgrade/upgrade.md)。
    -   若您的集群使用通过StorageClass创建的云盘或NAS类型的动态数据卷，且集群为测试环境，需要选择手动升级。具体操作，请参见[CSI-Provisioner升级](https://github.com/kubernetes-sigs/alibaba-cloud-csi-driver/blob/master/deploy/ack/upgrade/upgrade.md)。
    -   若您的集群使用通过StorageClass创建的云盘或NAS类型的动态数据卷，且包含关键业务数据，请[提交工单](https://workorder-intl.console.aliyun.com/console.htm)申请手动升级保障。
-   CSI-Provisioner组件前置检查通过，但是升级失败，请[提交工单](https://workorder-intl.console.aliyun.com/console.htm)申请手动升级保障。

