# SLB灰度引流概述 {#concept_371484 .concept}

本文主要为您介绍灰度部分生产流量到Kubernetes 集群，验证Kubernetes 集群的业务功能；避免回归测试未能覆盖所有业务功能。

## 前提条件 {#section_nmh_9p7_hf1 .section}

您已经完成业务功能回归测试，请参见[应用回归测试](intl.zh-CN/最佳实践/Swarm迁移Kubernetes/应用回归测试.md#)。

## NodePort引流 {#section_505_f3g_frd .section}

灰度引流主要有两种方式，基于Kubernetes 集群SLB引流 或 基于Swarm集群SLB引流；具体两种引流方式的情况如下：

-   基于Kubernetes SLB引流：更新客户端或DNS解析，将Kubernetes 集群SLB地址追加到客户端或DNS中，实现流量引入。
    -   引流成本高

        更新客户端或DNS解析配置

    -   回滚风险高
        -   万一Kubernetes 集群有问题，只能再次更新客户端或DNS配置。
        -   由于客户端发布时间较长或DNS运营商缓存问题，一般无法实时生效，导致线上业务有损。
-   基于Swarm SLB引流：将Kubernetes 集群服务挂到Swarm SLB下面并设置权重，实现流量引入。
    -   引流成本较低
        -   通过SLB 控制台，更新Swarm SLB配置即可。
        -   可以在SLB控制台权重配置，实现更精细的流量引入。
    -   回滚风险低
        -   通过SLB 控制台，更新Swarm SLB配置即可。
        -   实时生效。

**操作步骤** 

1.  [创建Kubernetes NodePort服务](intl.zh-CN/最佳实践/Swarm迁移Kubernetes/SLB灰度引流/创建Kubernetes NodePort服务.md#)。
2.  [验证Kubernetes NodePort服务](intl.zh-CN/最佳实践/Swarm迁移Kubernetes/SLB灰度引流/验证Kubernetes NodePort服务.md#)。
3.  [修改Swarm SLB配置](intl.zh-CN/最佳实践/Swarm迁移Kubernetes/SLB灰度引流/修改Swarm SLB配置.md#)。

## 引流快速回滚 {#section_s0p_t22_65s .section}

如果引入生产流量之后，发现Kubernetes 集群业务服务有问题；则调整虚拟服务器组，将Kubernetes 集群机器流量权重设置成0或将其剔除即可。

**操作步骤** 

1.  登录[负载均衡管理控制台](https://slb.console.aliyun.com/slb/cn-hangzhou)，在实例管理页面单击目标实例。
2.  在实例详情页面，单击**虚拟服务器组**，在目标分组右侧单击**编辑**。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/301854/155954562248028_zh-CN.png)

3.  从右侧滑出编辑虚拟服务器组页面，在目标虚拟服务右侧单击**删除**，并单击**确定**。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/301854/155954562248029_zh-CN.png)


## 灰度引流总结 {#section_1eu_og1_du8 .section}

主要介绍如何通过Swarm集群SLB **虚拟服务器组**机制，将生产流量部分引流到Kubernetes 的NodePort Service，进一步验证Kubernetes 集群业务服务是否正常。

通过Swarm SLB + Kubernetes NodePort 引流方式，相较于传统客户端或DNS切流方式，成本更低（只需在SLB控制台操作），风险更可控（切流/回滚实时生效，流量控制更精细）。

