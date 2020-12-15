---
keyword: [GPU拓扑感知调度, Kubernetes调度, 深度学习]
---

# GPU拓扑感知调度背景概述

本文简要描述了GPU拓扑分布情况，以及GPU拓扑感知调度的优势。

## GPU拓扑分布

下图为NVLink连接8个Tesla V100的混合立体网络拓扑。每块V100 GPU有6个NVLink通道，8块GPU间无法做到全连接，2块GPU间最多只能有2条NVLink连接。其中GPU0和GPU3，GPU0和GPU4之间有2条NVLink连接，GPU0和GPU1之间有一条NVLink连接，GPU0和6之间没有NVLink连接，故GPU0与GPU6之间仍然需要通过PCIe进行通信。

![GPU3](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/0968316061/p184644.png)

## GPU拓扑感知调度优势

NVlink连接的单向通信带宽为25 GB/s，双向通信带宽为50 GB/s，PCle连接的通信带宽为16 GB/s。在训练过程中，选择不同的GPU组合，会得到不同的训练速度，所以在GPU的调度过程中，选择最优的GPU组合，可得到最优的训练速度。

Kubernetes对节点的GPU拓扑信息不感知，调度过程中对GPU的选择比较随机，选择不同的GPU组合训练速度会存在较大的差异。基于以上问题，ACK基于[Scheduling Framework](https://developer.aliyun.com/article/766273)机制，实现GPU拓扑感知调度，在节点的GPU组合中选择具有最优训练速度的组合。

[安装ack-ai-installer组件](/cn.zh-CN/Kubernetes集群用户指南/ACK Pro集群/GPU拓扑感知调度/安装ack-ai-installer组件.md)

[分布式训练场景下激活GPU拓扑感知调度](/cn.zh-CN/Kubernetes集群用户指南/ACK Pro集群/GPU拓扑感知调度/分布式训练场景下激活GPU拓扑感知调度.md)

