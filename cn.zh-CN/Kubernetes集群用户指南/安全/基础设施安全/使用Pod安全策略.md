---
keyword: [创建或更新Pod安全策略, 准入控制组件, psp]
---

# 使用Pod安全策略

Kubernetes的Pod安全策略（Pod Security Policy）准入控制组件会基于您定义的规则验证在集群上创建和更新Pod的请求。如果创建或更新Pod的请求不符合定义的规则，系统将拒绝该请求并返回错误。本文将介绍如何在容器服务Kubernetes版ACK（Container Service for Kubernetes）中使用Pod安全策略。

您已完成以下操作：

-   [创建一个Kubernetes集群](/cn.zh-CN/Kubernetes集群用户指南/集群/创建集群/创建Kubernetes托管版集群.md)。
-   [通过kubectl连接Kubernetes集群](/cn.zh-CN/Kubernetes集群用户指南/集群/连接集群/通过kubectl连接Kubernetes集群.md)。

## ACK默认的Pod安全策略

在ACK中，Kubernetes 1.16.6版本的标准专有集群和标准托管集群将默认启用Pod安全策略准入控制组件，并配置一个名为ack.privileged的Pod安全策略。这个安全策略将放行任意类型的Pod，效果等同于集群未开启Pod安全策略准入控制组件。





## 删除ACK默认Pod安全策略对应的集群角色绑定

**警告：** 在删除ACK默认的Pod安全策略对应的集群角色绑定前必须先配置好自定义的Pod安全策略及其相应的RBAC绑定，否则所有用户、控制器、服务账号都将无法创建或更新Pod。

在配置好自定义的Pod安全策略及其相应的RBAC绑定后，您可以通过删除ACK默认Pod安全策略ack.privileged的集群角色绑定的方式来启用您自定义的Pod安全策略。

**说明：** 请不要删除或修改名为ack.privileged的Pod安全策略以及名为ack:podsecuritypolicy:privileged的集群角色，ACK集群的正常运行需要依赖这两个资源。

## 配置或恢复ACK默认的Pod安全策略

**相关文档**  


[Pod Security Policies](https://kubernetes.io/docs/concepts/policy/pod-security-policy/)

