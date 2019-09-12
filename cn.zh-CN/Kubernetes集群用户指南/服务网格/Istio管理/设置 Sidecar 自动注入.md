# 设置 Sidecar 自动注入 {#task_1846836 .task}

本文为您介绍如何在某个命名空间中开启/关闭 Sidecar 自动注入功能。

-   您已成功创建一个 Kubernetes 集群，参见[创建Kubernetes 集群](cn.zh-CN/Kubernetes集群用户指南/集群管理/创建集群/创建Kubernetes 集群.md#)。
-   您已成功部署Istio，参见[部署Istio](cn.zh-CN/Kubernetes集群用户指南/服务网格/Istio管理/部署Istio.md#)。

1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。
2.  在 Kubernetes 菜单下， 单击左侧导航栏中的**服务网格** \> **Istio管理**，进入部署Istio页面。
3.  在**Sidecar 自动注入设置**区域勾选一个或者多个命名空间，单击**启用 Sidecar 自动注入**。 

    ![启用自动注入](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/1463888/156825257857372_zh-CN.png)

4.  在弹出的提示框中单击**确定**。即在目标命名空间下启动Sidecar 自动注入功能。
5.  如果您想要关闭**Sidecar 自动注入**功能，单击命名空间右侧的**关闭自动注入**，在弹出的对话框中单击**确定**即可。 

    ![关闭自动注入](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/1463888/156825257857373_zh-CN.png)


