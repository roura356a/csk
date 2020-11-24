# 集群管理FAQ

本文主要为您介绍集群管理的一般问题。

关于故障处理类问题，参见[常见问题](https://help.aliyun.com/knowledge_detail/148453.html)。

## 已有集群是否可以添加支持Intel SGX的节点？

集群满足以下三个条件后可以添加支持Intel SGX的节点：

-   Kubernetes版本大于等于1.14.0。
-   集群的网络插件为**Flannel**。
-   创建集群时操作系统为AliyunLinux 2.xxxx，并且在节点接入时不要选择自定义镜像。

您可以[容器服务管理控制台](https://cs.console.aliyun.com/)，找到符合以上三个条件的集群，并在该集群右侧的**操作**列选择**更多** \> **系统组件升级**，安装aesm和sgx-device-plugin。

