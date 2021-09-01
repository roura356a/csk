---
keyword: [Flexvolume组件, Disk Controller组件, NAS Controller组件]
---

# 安装与升级Flexvolume组件

创建阿里云Kubernetes 1.16之前版本的集群时，若存储插件选择为Flexvolume，则控制台默认安装Flexvolume与Disk-Controller组件，但不会默认安装NAS Controller组件。本文介绍如何对Flexvolume组件升级管理及如何安装NAS Controller组件。

-   已创建ACK集群。具体操作，请参见[创建Kubernetes托管版集群](/cn.zh-CN/Kubernetes集群用户指南/集群/创建集群/创建Kubernetes托管版集群.md)。
-   阿里云Kubernetes集群存储插件为Flexvolume。
-   已通过kubectl连接Kubernetes集群。具体操作，请参见[步骤二：选择集群凭证类型](/cn.zh-CN/Kubernetes集群用户指南/集群/连接集群/通过kubectl工具连接集群.md)。

## 使用限制

目前支持CentOS 7、Alibaba Cloud Linux 2操作系统。

## 安装组件

**安装Flexvolume组件**

-   对于阿里云Kubernetes 1.16及之后版本的集群，不再支持Flexvolume组件的安装，请使用CSI-Plugin组件。更多信息，请参见[CSI和Flexvolume存储插件的区别](/cn.zh-CN/Kubernetes集群用户指南/存储-CSI/存储CSI概述.md)。
-   对于阿里云Kubernetes 1.16之前版本的集群，在创建集群时，若存储插件选择为Flexvolume，则控制台会默认安装Flexvolume组件。具体操作，请参见[组件配置](/cn.zh-CN/Kubernetes集群用户指南/ACK Pro集群/创建ACK Pro版集群.md)。

**安装Disk-Controller组件**

-   对于阿里云Kubernetes 1.16及之后版本的集群，不再支持Disk-Controller组件的安装，请使用CSI-Provisioner组件。更多信息，请参见[CSI和Flexvolume存储插件的区别](/cn.zh-CN/Kubernetes集群用户指南/存储-CSI/存储CSI概述.md)。
-   对于阿里云Kubernetes 1.16之前版本的集群，在创建集群时，若存储插件选择为Flexvolume，则控制台会默认安装Disk-Controller组件。具体操作，请参见[组件配置](/cn.zh-CN/Kubernetes集群用户指南/ACK Pro集群/创建ACK Pro版集群.md)。

**安装NAS Controller组件**

若您的集群存储插件选择为Flexvolume，可以通过手动部署NAS Controller方式实现动态NAS数据卷。

通过以下YAML模板手动安装。

```
kind: Deployment
apiVersion: apps/v1
metadata:
  name: alicloud-nas-controller
  namespace: kube-system
spec:
  selector:
    matchLabels:
      app: alicloud-nas-controller
  strategy:
    type: Recreate
  template:
    metadata:
      labels:
        app: alicloud-nas-controller
    spec:
      tolerations:
      - operator: Exists
      affinity:
        nodeAffinity:
          preferredDuringSchedulingIgnoredDuringExecution:
          - weight: 1
            preference:
              matchExpressions:
              - key: node-role.kubernetes.io/master
                operator: Exists
      priorityClassName: system-node-critical
      serviceAccount: admin
      hostNetwork: true
      containers:
        - name: nfs-provisioner
          image: registry.cn-hangzhou.aliyuncs.com/acs/alicloud-nas-controller:v1.14.8.17-7b898e5-aliyun
          env:
          - name: PROVISIONER_NAME
            value: alicloud/nas
          securityContext:
            privileged: true
          volumeMounts:
          - mountPath: /var/log
            name: log
      volumes:
      - hostPath:
          path: /var/log
        name: log
```

**验证安装**

验证Flexvolume、Disk-Controller与NAS Controller组件是否成功部署。

-   执行以下命令，查看Flexvolume组件是否成功部署。

    ```
    kubectl get pod -nkube-system | grep flexvolume
    ```

-   执行以下命令，查看Disk-Controller组件是否成功部署。

    ```
    kubectl get pod -nkube-system | grep alicloud-disk-controller
    ```

-   执行以下命令，查看NAS Controller组件是否成功部署。

    ```
    kubectl get pod -nkube-system | grep alicloud-nas-controller
    ```


## 升级组件

Flexvolume与Disk Controller组件支持在控制台升级，NAS Controller组件不支持在控制台升级。

若阿里云Kubernetes 1.16之前版本的集群升级到1.16及之后版本，升级后的集群仍支持Flexvolume存储插件，且可通过控制台升级。

1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。

2.  在控制台左侧导航栏中，单击**集群**。

3.  在集群列表页面中，单击目标集群名称或者目标集群右侧**操作**列下的**详情**。

4.  在集群管理详情页左侧导航栏，选择**运维管理** \> **组件管理**。

5.  单击存储页签，在**flexvolume**或**alicloud-disk-controller**组件区域单击**升级**。

6.  在提示对话框中确认版本信息后单击**确定**。

    升级成功后，对应组件区域会提示升级成功，且可查看组件当前版本。


-   Flexvolume组件升级时，以下场景请[联系我们](#section_rup_63p_rhj)申请手动升级保障。
    -   在线升级失败。
    -   Flexvolume组件版本不大于v1.12，且使用了云盘或OSS数据卷。
    -   集群业务敏感，且使用大量数据卷，希望提供升级保障。
-   Disk Controller组件升级失败时，请[联系我们](#section_rup_63p_rhj)申请手动升级保障。

## 联系我们

若您需要申请手动升级保障，请使用钉钉扫描下面的二维码或搜索钉钉群号加入钉钉群咨询。

群名：容器存储组件升级支持群

群号：35532895

![容器存储组件升级支持群](https://help-static-aliyun-doc.aliyuncs.com/assets/img/zh-CN/4967509161/p266528.jpg)

