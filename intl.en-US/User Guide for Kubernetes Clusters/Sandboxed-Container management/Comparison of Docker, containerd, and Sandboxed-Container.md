---
keyword: [Sandboxed-Container, containerd, core benefit, Docker container]
---

# Comparison of Docker, containerd, and Sandboxed-Container

Containers and images have become industry standards for software packaging and delivery. Kubernetes has become a standard platform for building, developing, and managing containerized cloud-native applications. An increasing number of enterprises and customers choose to deploy their applications in Container Service for Kubernetes \(ACK\). ACK supports the containerd, Docker, and Sandboxed-Container runtimes. This topic compares these runtimes in terms of implementations, limits, and deployment architectures. It also compares the commonly used commands provided by Docker Engine and containerd. This allows you to select a container runtime based on your requirements and scenarios.

## Comparison in terms of implementations and limits

|Item|containerd|Docker|Sandboxed-Container|Description|
|----|----------|------|-------------------|-----------|
|Cluster type|Managed Kubernetes clusters and dedicated Kubernetes clusters|All types|Managed Kubernetes clusters and dedicated Kubernetes clusters|None|
|Node type|Supports:-   ECS
-   EBM

|Supports:-   ECS
-   EBM

|Supports:-   EBM

|None|
|Node OS|Supports:-   CentOS
-   Alibaba Cloud Linux 2
-   ACK v1.20.4 version of Windows

|Supports:-   CentOS
-   Alibaba Cloud Linux 2
-   Windows

|Supports:-   Alibaba Cloud Linux 2 Customized Edition

|-   You cannot deploy both Docker and Sandboxed-Container on a node.
-   To deploy both Docker and Sandboxed-Container in a cluster, you can create node pools of different runtime types. |
|Container engine|containerd|Docker Engine|containerd|None|
|Monitoring|Supported|Supported|Supported|None|
|Container log collection|Supported|Supported|Supports log collection by using sidecar containers. Manual configuration is required.|For more information about sidecar configurations, see [Use CRDs to collect Kubernetes container logs in Sidecar mode](/intl.en-US/Data Collection/Logtail collection/Container log collection/Use CRDs to collect Kubernetes container logs in Sidecar mode.md).|
|Container stdout collection|Supported|Supported|Supported|None|
|RuntimeClass|Not supported|Not supported|Supported \(runV\)|None|
|Pod scheduling|No configuration is required.|No configuration is required.|You must add configurations based on the following rules:-   For Kubernetes 1.14.x, you must add the following configuration to the nodeSelector field.

    ```
alibabacloud.com/sandboxed-container: Sandboxed-Container.runv
    ```

-   For Kubernetes V1.16.x and later, no extra configuration is required.

|None|
|HostNetwork|Supported|Supported|Not supported|None|
|exec/logs|Supported|Supported|Supported|None|
|Node data disk|Optional|Optional|Required. The data disk must be at least 200 GiB.|None|
|Network plug-in|Supports:-   Flannel
-   Terway

|Supports:-   Flannel
-   Terway

|Supports:-   Flannel
-   Terway: supports only the inclusive ENI mode.

|None|
|kube-proxy mode|Supports:-   Iptables
-   IPVS

|Supports:-   Iptables
-   IPVS

|Supports:-   Iptables
-   IPVS

|None|
|Volume plug-in|CSI|CSI|CSI|None|
|Container root file system|OverlayFS|OverlayFS|DeviceMapper|None|

## Comparison in terms of deployment architectures

|Runtime|Deployment architecture|
|-------|-----------------------|
|Docker|```
kubelet -> dockerd -> containerd -> containerd-shim -> runC containers
``` |
|Containerd|```
kubelet -> containerd -> containerd-shim -> runC containers
``` |
|Sandboxed-Container V2|```
kubelet -> (CRI)containerd
                          \-> containerd-shim -> runC containers
                          \-> containerd-shim-rund-v2 -> runV sandboxed containers
``` |

## Comparison of the commonly used commands provided by Docker Engine and containerd

Docker uses Docker Engine for container lifecycle management. Sandboxed-Container uses containerd for container lifecycle management. These tools provide different commands that can be used to manage images and containers. The following table describes the commonly used commands provided by Docker Engine and containerd.

|Description|Docker|Containerd|
|docker|crictl \(recommended\)|ctr|
|-----------|------|----------|
|------|----------------------|---|
|Queries containers.|`docker ps`|`crictl ps`|`ctr -n k8s.io c ls`|
|Queries information about one or more containers.|`docker inspect`|`crictl inspect`|`ctr -n k8s.io c info`|
|Queries container logs.|`docker logs`|`crictl logs`|N/A|
|Runs a command in a container.|`docker exec`|`crictl exec`|N/A|
|Attaches to a container.|`docker attach`|`crictl attach`|N/A|
|Queries resource usage statistics.|`docker stats`|`crictl stats`|N/A|
|Creates a container.|`docker create`|`crictl create`|`ctr -n k8s.io c create`|
|Starts one or more containers.|`docker start`|`crictl start`|`ctr -n k8s.io run`|
|Stops one or more containers.|`docker stop`|`crictl stop`|N/A|
|Removes one or more containers.|`docker rm`|`crictl rm`|`ctr -n k8s.io c del`|
|Queries images.|`docker images`|`crictl images`|`ctr -n k8s.io i ls`|
|Queries information about one or more images.|`docker inspect`|`crictl inspecti`|N/A|
|Pulls an image.|`docker pull`|`crictl pull`|`ctr -n k8s.io i pull`|
|Pushes an image.|`docker push`|N/A|`ctr -n k8s.io i push`|
|Removes one or more images.|`docker rmi`|`crictl rmi`|`ctr -n k8s.io i rm`|
|Queries pods.|N/A|`crictl pods`|N/A|
|Queries information about one or more pods.|N/A|`crictl inspectp`|N/A|
|Starts a new pod.|N/A|`crictl runp`|N/A|
|Stops one or more pods|N/A|`crictl stopp`|N/A|

