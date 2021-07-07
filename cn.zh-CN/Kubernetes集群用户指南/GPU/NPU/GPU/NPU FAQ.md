# GPU/NPU FAQ

-   [GPU问题诊断](https://help.aliyun.com/knowledge_detail/144735.html)
-   [如何在GPU节点中升级Kernel？](https://help.aliyun.com/knowledge_detail/123756.html)
-   [如何修复GPU节点的容器启动异常？](https://help.aliyun.com/knowledge_detail/123771.html)
-   [创建或扩容支持GPU计算的Kubernetes集群时如何安装NVIDIA驱动？](https://help.aliyun.com/knowledge_detail/147961.html)
-   [排查GPU监控常见问题](/cn.zh-CN/Kubernetes集群用户指南/GPU/NPU/GPU运维管理/排查GPU监控常见问题.md)
-   [可用的GPU数目少于实际GPU数目](~~144735~~)
-   [GPU节点中重启Kubelet和Docker服务异常](~~123771~~)
-   [修复GPU实例重启或被置换后设备ID变更问题](/cn.zh-CN/Kubernetes集群用户指南/GPU/NPU/GPU运维管理/修复GPU实例重启或被置换后设备ID变更问题.md)
-   [阿里云容器服务是否支持GPU虚拟化型（vGPU）实例？](#section_ug2_7io_hhw)

## 阿里云容器服务是否支持GPU虚拟化型（vGPU）实例？

vGPU实例需要购买NVIDIA官方提供的GRID License才能正常工作，而阿里云并不提供License服务器。因此即使您创建了GPU虚拟化集群，vGPU实例也无法使用。因此，阿里云容器服务已不再支持在控制台选择vGPU实例作为集群节点。

如果您的业务对vGPU实例有强依赖，您可以向NVIDIA购买GRID License，自建License服务器。

**说明：**

-   更新ACK集群中vGPU实例的NVIDIA驱动License时，需要使用License服务器。
-   购买ECS实例并参考NVIDIA官网教程搭建License服务器。更多信息，请参见[NVIDIA](https://www.nvidia.com/object/nvidia-enterprise-account.html?spm=a2c4g.11186623.2.10.3fac7d8fHWP47f)。

如果您的License服务器已经搭建完成，请参考以下步骤将vGPU实例加入ACK集群。

**将vGPU实例加入ACK集群**

1.  [提交工单](https://selfservice.console.aliyun.com/ticket/createIndex)，申请开放自定义系统镜像功能。
2.  基于CentOS 7.X和Alibaba Cloud Linux 2制作自定义系统镜像，镜像中需要安装NVIDIA GRID驱动并且正确配置GRID License。具体操作，请参见[使用实例创建自定义镜像](/cn.zh-CN/镜像/自定义镜像/创建自定义镜像/使用实例创建自定义镜像.md)和[在GPU实例中安装GRID驱动（Linux）](/cn.zh-CN/实例/选择实例规格/GPU计算型/在GPU实例中安装GRID驱动（Linux）.md)。
3.  创建节点池。具体操作，请参见[管理节点池](/cn.zh-CN/Kubernetes集群用户指南/节点与节点池/节点池/管理节点池.md)。
4.  将vGPU实例加入到步骤3创建的节点池中，具体操作，请参见[添加已有节点](/cn.zh-CN/Kubernetes集群用户指南/节点与节点池/节点/添加已有节点.md)。

**后续相关步骤：更新ACK集群中vGPU实例的NVIDIA驱动License**

更新ACK集群中vGPU实例的NVIDIA驱动License，具体操作，请参见[更新ACK集群中GPU虚拟化型（vGPU）实例的NVIDIA驱动License](/cn.zh-CN/Kubernetes集群用户指南/GPU/NPU/GPU运维管理/更新vGPU实例的NVIDIA驱动License.md)。

