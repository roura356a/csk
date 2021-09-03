# Topology-aware GPU scheduling

Kubernetes does not support topology-aware GPU scheduling. In this case, GPUs are selected at random. The performance of GPU acceleration for training jobs can vary based on different combinations of GPUs. To resolve this issue, Container Service for Kubernetes \(ACK\) supports topology-aware GPU scheduling based on the scheduling framework. This feature selects a combination of GPUs from GPU-accelerated nodes to achieve optimal GPU acceleration for training jobs.

For more information about how to use topology-aware GPU scheduling, see the following topics:

-   [Overview of topology-aware GPU scheduling](/intl.en-US/User Guide for Kubernetes Clusters/GPU/NPU/GPU scheduling/GPU topology-aware scheduling/Overview of topology-aware GPU scheduling.md)
-   [Install ack-ai-installer](/intl.en-US/User Guide for Kubernetes Clusters/GPU/NPU/GPU scheduling/GPU topology-aware scheduling/Install ack-ai-installer.md)
-   [Use topology-aware GPU scheduling to achieve optimal GPU acceleration for TensorFlow distributed jobs](/intl.en-US/User Guide for Kubernetes Clusters/GPU/NPU/GPU scheduling/GPU topology-aware scheduling/Use topology-aware GPU scheduling to achieve optimal GPU acceleration for TensorFlow distributed jobs.md)
-   [Use topology-aware GPU scheduling to achieve optimal GPU acceleration for PyTorch distributed jobs](/intl.en-US/User Guide for Kubernetes Clusters/GPU/NPU/GPU scheduling/GPU topology-aware scheduling/Use topology-aware GPU scheduling to achieve optimal GPU acceleration for PyTorch distributed jobs.md)

