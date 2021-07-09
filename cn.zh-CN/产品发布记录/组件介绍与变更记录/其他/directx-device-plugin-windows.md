---
keyword: [directx-device-plugin-windows, GPU加速]
---

# directx-device-plugin-windows

本文介绍directx-device-plugin-windows组件信息、使用说明和变更记录。

## 组件介绍

directx-device-plugin-windows是ACK集群上使用的DirectX设备插件。在阿里云上使用虚拟化GPU云服务器时，directx-device-plugin-windows可以为Windows容器提供基于DirectX构建的GPU加速功能。关于虚拟化GPU云服务器的详细介绍，请参见[GPU虚拟化型]()。

directx-device-plugin-windows仅支持v1.20.4及其以上版本的ACK集群。

## 使用说明

当使用DirectX设备插件时，directx-device-plugin-windows默认会以DaemonSet工作负载的形式安装。directx-device-plugin-windows依赖Windows工作节点上的特权托管进程，仅适用于创建集群内节点池时就含有`windows.alibabacloud.com/deployment-topology=2.0`和`windows.alibabacloud.com/directx-supported=true`标签的Windows工作节点。如果您创建完Windows工作节点后，再在Windows工作节点上添加`windows.alibabacloud.com/deployment-topology=2.0`和`windows.alibabacloud.com/directx-supported=true`标签，则该Windows工作节点不支持directx-device-plugin-windows。

directx-device-plugin-windows目前仍然需要手动安装。具体操作，请参见[在Windows容器中使用基于DirectX的GPU加速](/cn.zh-CN/Kubernetes集群用户指南/Windows容器/在Windows容器中使用基于DirectX的GPU加速.md)。

## 变更记录

**2021年07月**

|版本号|镜像地址|变更时间|变更内容|变更影响|
|---|----|----|----|----|
|v1.0.0|registry-vpc.$\{region\}.aliyuncs.com/acs/directx-device-plugin-windows:v1.0.0|2021年07月05日|支持Multiple Windows Server Release 1809（10.0.17763.1999）、1909（10.0.18363.1556）、2004（10.0.19041.1052）。|此次升级不会对业务造成影响。|

