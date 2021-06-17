# ACK集群实现GPU成本优化

本文介绍ACK实现GPU成本优化的场景描述、解决问题、架构图及操作参考链接。

## 场景描述

利用阿里云容器服务ACK部署GPU集群之后，出于成本优化的考虑，对于集群中GPU利用率不高的应用， 例如推理的应用，建议利用阿里云cGPU技术将一定数量的应用运行至一 块GPU卡上，以提高利用率。对于GPU利用率比较高的应用，不做改动。这样可以实现灵活管理的同时， 降低整体成本。

## 解决问题

-   在ACK服务中实现共享GPU，灵活拆分。
-   提高GPU利用率，降低TCO。
-   应用无需修改，保障多个容器共享同一个GPU时，彼此互相隔离。

## 架构图

![GPU](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/7340102261/p277365.png)

## 参考链接

有关ACK实现GPU成本优化的详情，请参见[ACK实现GPU成本优化](https://bp.aliyun.com/detail/163)。

