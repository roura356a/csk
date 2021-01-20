---
keyword: [GPU topology-aware scheduling, Kubernetes scheduling, deep learning]
---

# Overview

This topic describes the GPU topology. It also includes further details of the benefits of GPU topology-aware scheduling.

## GPU topology

The following figure shows the topology of eight Tesla V100 GPUs that communicate with each other through NVLinks. Each Tesla V100 GPU is assigned six NVLinks. However, NVLinks cannot be established between every two Tesla V100 GPUs. At most two NVLinks can be established between two Tesla V100 GPUs. In this example, two NVLinks are established between GPU 0 and GPU 3. Two NVLinks are established between GPU 0 and GPU 4. One NVLink is established between GPU 0 and GPU 1. GPU 0 and GPU 6 communicate with each other through Peripheral Component Interconnect Express \(PCIe\), instead of NVLinks.

![GPU3](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/9686528061/p184644.png)

## Benefits

The one-way communication bandwidth of an NVLink is 25 Gbit/s. The two-way communication bandwidth of an NVLink is 50 Gbit/s. The PCIe bandwidth is 16 Gbit/s. In a training job, the training speed depends on the different combinations of GPUs. Therefore, the optimal combination of GPUs can be selected during the GPU scheduling process. This ensures the optimal training speed.

Kubernetes does not support GPU topology-aware scheduling. In this case, GPUs are selected at random. The training speed can vary based on different combinations of GPUs. To fix this issue, Container Service for Kubernetes \(ACK\) supports GPU topology-aware scheduling based on the [scheduling framework](https://developer.aliyun.com/article/766273). You can use this feature to select a combination of GPUs on GPU nodes. This ensures the optimal training speed.

[Install the ack-ai-installer component](/intl.en-US/User Guide for Kubernetes Clusters/Introduction/GPU topology-aware scheduling/Install the ack-ai-installer component.md)

[Activate GPU topology-aware scheduling in distributed training scenarios](/intl.en-US/User Guide for Kubernetes Clusters/Introduction/GPU topology-aware scheduling/Activate GPU topology-aware scheduling in distributed training scenarios.md)

