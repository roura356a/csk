---
keyword: [Sandboxed-Container, sandboxed containers, container runtimes, Docker]
---

# Sandboxed-Container overview

Sandboxed-Container provides an alternative to the Docker runtime. It allows you to run applications in a sandboxed and lightweight virtual machine that has a dedicated kernel. This enhances resource isolation and improves security.

Sandboxed-Container is suitable in scenarios such as untrusted application isolation, fault isolation, performance isolation, and load isolation among multiple users. Sandboxed-Container provides higher security. Sandboxed-Container has minor impacts on application performance and offers the same user experience as Docker in terms of logging, monitoring, and elastic scaling.

![Architecture](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/0565359951/p65828.png)

## Architecture

![architecture](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/9781800061/p166374.png)

## Features

Sandboxed-Container is container-securing runtime that is developed by Alibaba Cloud based on sandboxed and lightweight virtual machines. Compared with Sandboxed-Container V1, Sandboxed-Container V2 maintains the same isolation performance and reduces the pod overhead by 90%. It also allows you to start sandboxed containers 3 times faster and increases the maximum number of pods that can be deployed on a host by 10 times. Sandboxed-Container V2 provides the following key features:

-   Strong isolation based on sandboxed and lightweight virtual machines.
-   Good compatibility with runC in terms of application management.
-   High performance that corresponds to 90% the performance of applications based on runC.
-   Network Attached Storage \(NAS\) file systems, Alibaba Cloud disks, and OSS buckets can be mounted both directly and through virtio-fs.
-   The same user experience as that provided by containers in runC in terms of monitoring, logging, and storage.
-   Support for RuntimeClass \(runC and runV\). For more information, see [RuntimeClass](https://kubernetes.io/docs/concepts/containers/runtime-class/).
-   Less requirements on technical expertise and skills of using virtual machines.
-   Higher stability than that provided by Kata Containers. For more information about Kata Containers, see [Kata Containers](https://katacontainers.io/).

**Related topics**  


[How do I select between Docker and Sandboxed-Container?](/intl.en-US/User Guide for Kubernetes Clusters/Sandboxed-Container management/How do I select between Docker and Sandboxed-Container?.md)

[Differences between runC and runV](/intl.en-US/User Guide for Kubernetes Clusters/Sandboxed-Container management/Differences between runC and runV.md)

[Benefits of Sandboxed-Container](/intl.en-US/User Guide for Kubernetes Clusters/Sandboxed-Container management/Benefits of Sandboxed-Container.md)

[t1830927.md\#](/intl.en-US/User Guide for Kubernetes Clusters/Sandboxed-Container management/Create a managed ACK cluster that supports sandboxed containers.md)

