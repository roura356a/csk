---
keyword: [Sandboxed-Container, release notes]
---

# Sandboxed-Container release notes

This topic lists the latest changes to the Sandboxed-Container runtime.

For more information about Sandboxed-Container, see [Overview](/intl.en-US/User Guide for Kubernetes Clusters/Sandboxed-Container management/Sandboxed-Container overview.md).

## January 2021

|Version|Release date|Description|Impact|
|-------|------------|-----------|------|
|2.1.1|January 7, 2021|Privileged containers are supported.|No impact on workloads.|

## December 2020

|Version|Release date|Description|Impact|
|-------|------------|-----------|------|
|2.1.0|November 26, 2020|New features are released to improve service stability and performance. The new features are:-   A project quota is supported for you to limit the number of bytes that can be written to the container rootfs directory.
-   A cloud disk can be mounted to a sandboxed container.
-   A NAS file system can be mounted to a sandboxed container.
-   Custom kernel parameters are supported for sandboxed pods.
-   Quality of service \(QoS\) policies and network traffic marking policies are supported.

|No impact on workloads.|

## August 2020

|Version|Release date|Description|Impact|
|-------|------------|-----------|------|
|2.0.0|August 28, 2020|The major version of Sandboxed-Container 2.0 is upgraded to achieve the following benefits:-   The lightweight virtual machine container runtime developed by Alibaba Cloud supports more lightweight and efficient deployment. It also simplifies the architecture and maintenance of Kubernetes clusters.
-   Sandboxed-Container V2.0 reduces the resource overheads by 90% and accelerates Sandboxed-Container startup by three times.
-   Increases the deployment density of standalone sandboxed containers by 10 times.
-   The virtio-fs file system is supported. The performance of this file system is higher than that of the 9pfs file system.

|During the upgrade, the pods on the nodes that use the Sandboxed-Container runtime are rebuilt. We recommend that you create pod replicas before you perform the upgrade.|

## July 2020

|Version|Release date|Description|Impact|
|-------|------------|-----------|------|
|1.1.1|July 27, 2020|The following issues that are related to the stability of Sandboxed-Container are fixed:-   The security risk that is related to the container-storaged component is eliminated.
-   The issue where the `kubectl cp` command is blocked after you run this command is fixed.
-   The issue where logs cannot be printed to stdout files after containerd is restarted is fixed.
-   The issue where the system time of sandboxed containers may not be synchronized at regular intervals is fixed.

|No impact on workloads.|

## March 2020

|Version|Release date|Description|Impact|
|-------|------------|-----------|------|
|1.1.0|March 5, 2020|New features of Sandboxed-Container 1.1.0 are released: -   Alibaba Cloud disks and Network Attached Storage \(NAS\) volumes can be mounted to sandboxed containers. This provides the same performance as the volumes that are mounted to the host. This avoids performance loss when storage devices are mounted over 9pfs.
-   RootFS block I/O throttling is supported.

The stability of Sandboxed-Container 1.1.0 is significantly improved.

|No impact on workloads.|

## September 2019

|Version|Release date|Description|Impact|
|-------|------------|-----------|------|
|1.0.0|September 5, 2019|The following features of Sandboxed-Container 1.0.0 are supported: -   Strong isolation based on sandboxed, lightweight virtual machines.
-   Good compatibility with runC in terms of application management.
-   High performance that is equivalent to 90% of the performance provided by applications based on runC.
-   The same user experience as runC in terms of logging, monitoring, and storage.
-   The RuntimeClass feature is released. This feature allows you to select the container runtime, such as runC and runV. For more information, see [RuntimeClass](https://kubernetes.io/docs/concepts/containers/runtime-class/).
-   Fewer requirements on technical expertise and skills of using virtual machines.
-   Higher stability compared to that provided by the Kata Containers runtime. For more information about Kata Containers, see [Kata Containers](https://katacontainers.io/).

|No impact on workloads.|

