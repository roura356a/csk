---
keyword: ack-arms-prometheus组件常见问题
---

# ack-arms-prometheus组件常见问题

ack-arms-prometheus组件是ARMS Prometheus监控在ACK集群中的监控组件。本文介绍安装ack-arms-prometheus组件时遇到的常见问题。

## 提示Component Not Installed后继续安装ack-arms-prometheus组件，安装失败

-   检查是否已经安装ack-arms-prometheus组件。
    1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。
    2.  在控制台左侧导航栏中，单击**集群**。
    3.  在集群列表页面中，单击目标集群名称或者目标集群右侧**操作**列下的**详情**。
    4.  在集群管理页面左侧导航栏选择**应用** \> **Helm**。

        在**Helm**页面检查是否已有ack-arms-prometheus。

        -   若**Helm**页面已有ack-arms-prometheus，您需要进行以下操作：

            您需要在**Helm**页面删除ack-arms-prometheus，并在**组件管理**页面重新安装ack-arms-prometheus。关于安装ack-arms-prometheus的具体操作，请参见[管理组件](/intl.zh-CN/Kubernetes集群用户指南/组件/管理组件.md)。

        -   若**Helm**页面没有ack-arms-prometheus，您需要进行以下操作：
            1.  若没有ack-arms-prometheus，说明删除ack-arms-prometheus helm有资源残留，需要手动清理。关于删除ack-arms-prometheus残留资源的具体操作，请参见[ARMS Prometheus监控常见问题](/intl.zh-CN/Kubernetes集群用户指南/可观测性/监控管理/阿里云Prometheus监控.md)。
            2.  在**组件管理**页面安装ack-arms-prometheus。关于安装ack-arms-prometheus的具体操作，请参见[管理组件](/intl.zh-CN/Kubernetes集群用户指南/组件/管理组件.md)。
            3.  执行以上步骤，仍无法安装ack-arms-prometheus，请[提交工单](https://workorder-intl.console.aliyun.com/console.htm)。
-   检查arms-prometheus-ack-arms-prometheus的日志是否有报错。
    1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。
    2.  在控制台左侧导航栏中，单击**集群**。
    3.  在集群列表页面中，单击目标集群名称或者目标集群右侧**操作**列下的**详情**。
    4.  在集群管理页面左侧导航栏选择**工作负载** \> **无状态**。
    5.  在**无状态**页面顶部设置**命名空间**为**arms-prom**，然后单击arms-prometheus-ack-arms-prometheus。
    6.  单击**日志**页签，查看日志中是否有报错。

        若日志中出现报错，请[提交工单](https://selfservice.console.aliyun.com/ticket/category/arms)。

-   检查Agent是否安装报错。
    1.  登录[ARMS控制台](https://arms-intl.console.aliyun.com/)。
    2.  在顶部菜单栏，选择地域。
    3.  在控制台左侧导航栏单击**Prometheus监控**。
    4.  在**Prometheus监控**页面，单击K8s集群名称。
    5.  在左侧导航栏单击**设置**，在右侧页面单击**设置**页签。

        在**设置**页签下可以查看健康检查结果。若有报错，请[提交工单](https://selfservice.console.aliyun.com/ticket/category/arms)。


## 安装ack-arms-prometheus组件时报错xxx in use

1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。
2.  在控制台左侧导航栏中，单击**集群**。
3.  在集群列表页面中，单击目标集群名称或者目标集群右侧**操作**列下的**详情**。
4.  在集群管理页面左侧导航栏选择**应用** \> **Helm**。

    在**Helm**页面检查是否已有ack-arms-prometheus。

    -   若**Helm**页面已有ack-arms-prometheus，您需要进行以下操作：

        您需要在**Helm**页面删除ack-arms-prometheus，并在**组件管理**页面重新安装ack-arms-prometheus。关于安装ack-arms-prometheus的具体操作，请参见[管理组件](/intl.zh-CN/Kubernetes集群用户指南/组件/管理组件.md)。

    -   若**Helm**页面没有ack-arms-prometheus，您需要进行以下操作：
        1.  若没有ack-arms-prometheus，说明删除ack-arms-prometheus helm有资源残留，需要手动清理。关于删除ack-arms-prometheus残留资源的具体操作，请参见[ARMS Prometheus监控常见问题](/intl.zh-CN/Kubernetes集群用户指南/可观测性/监控管理/阿里云Prometheus监控.md)。
        2.  在**组件管理**页面安装ack-arms-prometheus。关于安装ack-arms-prometheus的具体操作，请参见[管理组件](/intl.zh-CN/Kubernetes集群用户指南/组件/管理组件.md)。
        3.  执行以上步骤，仍无法安装ack-arms-prometheus，请[提交工单](https://workorder-intl.console.aliyun.com/console.htm)。

