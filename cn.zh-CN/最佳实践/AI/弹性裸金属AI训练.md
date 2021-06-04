---
keyword: [弹性裸金属AI训练, 弹性裸金属, AI训练]
---

# 弹性裸金属AI训练

本文介绍弹性裸金属AI训练的场景描述、解决问题、架构图及操作参考链接。

## 场景描述

本方案适用于AI图片训练场景，尤其是对性能要求苛刻，业务交付紧迫的场景。例如自动驾驶的模型训练（图片）等AI模型训练的场景。 本方案使用了SCC超级计算集群，采用弹性裸金属GPU服务器、并行文件系统CPFS、RDMA网络、阿里云容器服务Kubernetes版和飞天AI加速训练工具，提供性能稳定的训练环境，保障业务能力。

## 解决问题

-   搭建AI图片训练基础环境。
-   使用CPFS存储训练数据。
-   使用飞天AI加速训练工具加速训练。
-   使用Arena一键提交作业。

## 架构图

![bp7](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/0633542261/p279025.png)

## 参考链接

有关弹性裸金属AI训练的详情，请参见[弹性裸金属AI训练](https://bp.aliyun.com/detail/53)。

