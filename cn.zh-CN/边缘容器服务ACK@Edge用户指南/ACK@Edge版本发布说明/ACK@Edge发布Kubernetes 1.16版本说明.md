---
keyword: [ACK@Edge, 版本说明]
---

# ACK@Edge发布Kubernetes 1.16版本说明

阿里云边缘容器服务ACK@Edge是基于容器服务ACK针对边缘计算场景推出的云边一体化托管方案。本文介绍ACK@Edge发布Kubernetes 1.16版本所做的变更内容。

## Kubernetes Core

该版本在容器服务ACK 1.16版本之上，针对边缘计算场景做的变更主要为以下两点：

-   修复当节点cpuacct.stat文件中超出4条记录时**kubelet**启动失败问题。
-   Kube-Proxy支持IPVS（IP Virtual Server）模式。
-   **kubelet**支持通过指定网卡名来配置节点InternalIP。

更多关于ACK版本变更信息，请参见[ACK发布Kubernetes 1.16版本说明](/cn.zh-CN/新功能发布记录/Kubernetes版本发布说明/ACK发布Kubernetes 1.16版本说明.md)。

## 边缘节点自治

该版本边缘节点自治在稳定性方面做了加强，主要变更包括以下几个方面：

-   缓存数据丢失时，Client获取的返回数据从空字符串修改为404。
-   **edge-hub**证书存储目录从/etc/kubernetes/edge-hub调整为/var/lib/edge-hub。
-   **edge-hub**证书名称从**edge-hub.kubeconfig**调整为**edge-hub.conf, bootstrap-edge-hub-current.conf --\> bootstrap-hub.conf**。
-   增加**prometheus metrics**接口。
-   **iptables**性能优化：为127.0.0.1:10261和169.254.2.1:10261地址增加**iptables notrack**。

更多信息，请参见[边缘网络自治]()。

## 云边运维通道

该版本对云边运维通道的性能进行了优化，主要变更包括以下几个方面：

-   隧道底层通信库协议从普通TCP协议切换成**gRPC**，基于**gRPC**的压缩效率，云边通信数据量最大可减少40%。
-   为**edge-tunnel-agent**增加证书申请及自动更新功能，解耦了对节点证书依赖，同时**edge-tunnel-agent**证书存储目录修改为/var/lib/edge-tunnel-agent/pki。
-   增加**prometheus metrics**。
-   **edge-tunnel-agent** Pod部署依赖节点Label调整为`alibabacloud.com/is-edge-worker: "true"`。

更多信息，请参见[边缘运维通道]()。

## 运维监控组件

该版本对**metrics-server**进行了版本升级，同时安全性也进行增强，主要变更包括以下两点：

-   **metrics-server**版本从V0.2.1升级到V0.3.8。
-   云监控对接方式调整为更通用的Token对接。

## 边缘单元化管理

边缘单元化管理是该版本新增功能，增加新的组件**yurt-app-manager** ，主要特性包括以下几个方面：

-   通过NodePool实现节点的单元化管理。
-   通过UnitedDeployment实现应用的单元化部署。
-   通过Service拓扑配置来实现流量在节点池内或节点闭环。

更多关于边缘单元化管理的信息，请参见[边缘节点池概述]()。

## 增强型网络节点池

增强型网络节点池是该版本的新增功能，主要特性包括以下两个方面：

-   提供更稳定、更安全的云边通信通道。
-   支持私网环境的边缘应用通过容器网络与云端应用通信。

更多信息，请参见[创建增强型网络边缘节点池（公测）]()。

## 容器运行时

该版本容器运行时主要变更包括以下两点：

-   ARM和ARM64 runC版本升级到1.0.0-rc10。
-   Cgroup Driver由Cgroupfs变更为Systemd。

## CNI插件

该版本增强了CNI插件的稳定性，修复不同命名空间中相同Pod名导致Pod IP分配失败的问题。

## 边缘节点接入

该版本对边缘节点接入流程进行了优化并新增接入参数，主要包括以下几个方面：

-   优化边缘节点接入流程，新增CIDR（Classless Inter-Domain Routing）冲突校验。
-   支持给节点设置可分配的IP数量。
-   新增labels、nodeIface、annotations和taints等参数。
-   支持Ubuntu 5.4内核系统接入。

更多信息，请参见[添加边缘节点](/cn.zh-CN/边缘容器服务ACK@Edge用户指南/边缘节点管理/添加边缘节点.md)。

## OpenAPI变更

节点池API支持边缘节点池，更多信息，请参见[节点池](/cn.zh-CN/API参考/节点/节点池/创建节点池.md)。

