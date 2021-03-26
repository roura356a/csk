---
keyword: [部署Knative, 开启Knative]
---

# 开启Knative

Knative是一款基于Kubernetes的Serverless框架，其目标是制定云原生、跨平台的Serverless编排标准。本文介绍如何在ASK开启Knative。

Knative当前支持在Kubernetes专有版集群、Kubernetes托管版集群以及Serverless Kubernetes （ASK）集群部署。目前在ASK集群中部署的Knative暂只支持Knative Serving。

## 在创建ASK集群时部署Knative

1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。

2.  在控制台左侧导航栏中，单击**集群**。

3.  在集群列表页面中，单击页面右上角的**创建集群**。

4.  单击**ASK集群**页签，以下为重点关注参数，详细参数配置请参见[创建Serverless Kubernetes集群](/cn.zh-CN/Serverless Kubernetes集群用户指南/快速入门/创建Serverless Kubernetes集群.md)。

    |参数|配置|
    |--|--|
    |Kubernetes版本|选择1.15及以上版本。|
    |NAT网关|设置是否为专有网络创建NAT网关并配置SNAT规则。仅当**专有网络**选择为**自动创建**时，需要设置该选项。    -   若您选择自动创建VPC，可选择是否自动配置SNAT网关。
    -   若选择不自动配置SNAT，您可自行配置NAT网关实现VPC安全访问公网环境，并且手动配置SNAT，否则VPC内实例将不能正常访问公网。 |
    |公网访问|设置是否开放**使用EIP暴露API Server**。    -   如果选择开放，将创建一个EIP，同时会暴露Master节点的6443端口（对应API Server），您可以在外网通过kubeconfig连接或操作集群。
    -   若选择不开放，不会创建EIP，您只能在VPC内部用kubeconfig连接和操作集群。 |
    |Knative|选中开启**Knative**，将在创建ASK集群时部署Knative。![2](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/2050821061/p170979.png) |

5.  在页面右侧，单击**创建集群**，在弹出的当前配置确认页面，单击**确定**，启动部署。


## 在ASK集群管理中部署Knative

1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。

2.  在控制台左侧导航栏中，单击**集群**。

3.  在集群列表页面中，单击目标集群名称或者目标集群右侧**操作**列下的**详情**。

4.  在集群管理页左侧导航栏中，选择**应用** \> **Knative**。

5.  在Knative的组件管理页签单击**一键部署Knative**。

6.  选择需要安装的Knative组件后，单击**部署**。

    Serving组件用于管理Serverless工作负载，可以和事件结合并且提供了基于请求驱动的自动扩缩的能力，而且在没有服务需要处理的时候可以缩容到零个实例。


## 结果验证

Knative部署完成后，在Knative组件管理页面可以看到Knative组件显示**已安装**。

