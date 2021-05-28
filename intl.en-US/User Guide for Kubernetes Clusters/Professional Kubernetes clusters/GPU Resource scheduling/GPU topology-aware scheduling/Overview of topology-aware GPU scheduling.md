---
keyword: [topology-aware GPU scheduling, Kubernetes scheduling, deep learning]
---

# Overview of topology-aware GPU scheduling

This topic describes the GPU topology and benefits of topology-aware GPU scheduling.

## GPU topology

The following figure shows the topology of eight Tesla V100 GPUs that communicate with each other through NVLinks. Each Tesla V100 GPU is assigned six NVLinks. However, NVLinks cannot be established between every two Tesla V100 GPUs. At most two NVLinks can be established between two Tesla V100 GPUs. In this example, two NVLinks are established between GPU 0 and GPU 3. Two NVLinks are established between GPU 0 and GPU 4. One NVLink is established between GPU 0 and GPU 1. GPU 0 and GPU 6 communicate with each other through Peripheral Component Interconnect Express \(PCIe\), instead of NVLinks.

![GPU3](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/9686528061/p184644.png)

## Benefits of topology-aware GPU scheduling

The one-way communication bandwidth of an NVLink is 25 Gbit/s. The two-way communication bandwidth of an NVLink is 50 Gbit/s. The PCIe bandwidth is 16 Gbit/s. In a training job, the training speed depends on the combination of GPUs. The optimal combination of GPUs can be selected during the GPU scheduling process to ensure the optimal training speed.

Kubernetes does not support topology-aware GPU scheduling. In this case, GPUs are selected at random. The training speed varies based on different combinations of GPUs. Container Service for Kubernetes \(ACK\) supports topology-aware GPU scheduling based on the [scheduling framework](https://developer.aliyun.com/article/766273). This feature selects a combination of GPUs from GPU-accelerated nodes to achieve optimal GPU acceleration for training jobs.

[Install the ack-ai-installer component](/intl.en-US/User Guide for Kubernetes Clusters/Professional Kubernetes clusters/GPU Resource scheduling/GPU topology-aware scheduling/Install the ack-ai-installer component.md)

[Use GPU topology-aware scheduling to achieve optimal GPU acceleration for TensorFlow distributed jobs](/intl.en-US/User Guide for Kubernetes Clusters/Professional Kubernetes clusters/GPU Resource scheduling/GPU topology-aware scheduling/Use GPU topology-aware scheduling to achieve optimal GPU acceleration for TensorFlow distributed jobs.md)

[Use GPU topology-aware scheduling to achieve optimal GPU acceleration for PyTorch distributed jobs](/intl.en-US/User Guide for Kubernetes Clusters/Professional Kubernetes clusters/GPU Resource scheduling/GPU topology-aware scheduling/Use GPU topology-aware scheduling to achieve optimal GPU acceleration for PyTorch distributed jobs.md)

