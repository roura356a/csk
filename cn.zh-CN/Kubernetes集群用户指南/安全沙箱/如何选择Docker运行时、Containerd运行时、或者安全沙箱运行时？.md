---
keyword: [安全沙箱运行时, Containerd运行时, 核心优势, Docker容器]
---

# 如何选择Docker运行时、Containerd运行时、或者安全沙箱运行时？

镜像和容器已然成为应用的打包和交付标准。Kubernetes成为了整个容器云原生应用的标准OS，越来越多的企业和用户选择在ACK中部署自己的应用。容器服务Kubernetes版（ACK）支持Containerd、Docker、安全沙箱三种运行时。本文通过对比三种运行时的实现和使用限制、部署结构，并通过对比Docker和Containerd两种容器引擎常用命令，帮助您根据需求场景选择合适的容器运行时。

## 容器运行时实现和使用限制的对比

|特性|Containerd运行时|Docker运行时|安全沙箱运行时|说明|
|--|-------------|---------|-------|--|
|集群类型|支持托管版和专有版|支持所有类型|支持托管版和专有版|无|
|节点型号|支持：-   ECS
-   EBM

|支持：-   ECS
-   EBM

|支持：EBM

|无|
|节点OS|支持：-   CentOS
-   Alibaba Cloud Linux 2
-   ACK v1.20.4版本的Windows

|支持：-   CentOS
-   Alibaba Cloud Linux 2
-   Windows

|支持：Alibaba Cloud Linux 2定制版

|-   不支持同一节点同时部署Docker和安全沙箱两种运行时。
-   集群内可以通过创建不同节点池来实现Docker运行时节点和安全沙箱运行时节点混合部署。 |
|容器引擎|支持Containerd|支持Docker|支持Containerd|无|
|监控|支持|支持|支持|无|
|容器日志文件采集|支持|支持|手动支持（Sidecar）|有关Sidecar配置，请参见[通过Sidecar-CRD方式采集容器日志](/cn.zh-CN/数据采集/Logtail采集/采集容器日志/通过Sidecar-CRD方式采集容器日志.md)。|
|容器标准输出采集|支持|支持|支持|无|
|RuntimeClass|不支持|不支持|支持（runV）|无|
|Pod调度|无需配置|无需配置|配置如下：-   Kubernetes v1.14.x版本必须为nodeSelector增加以下配置。

    ```
alibabacloud.com/sandboxed-container: Sandboxed-Container.runv
    ```

-   Kubernetes v1.16.x以及更高版本不需要任何配置。

|无|
|HostNetwork|支持|支持|不支持|无|
|exec/logs|支持|支持|支持|无|
|节点数据盘|可选|可选|必选（不小于200 GiB）|无|
|网络插件|支持：-   Flannel
-   Terway

|支持：-   Flannel
-   Terway

|支持：-   Flannel
-   Terway：仅支持非独占弹性网卡模式。

|无|
|kube-proxy代理模式|支持：-   Iptables
-   IPVS

|支持：-   Iptables
-   IPVS

|支持：-   Iptables
-   IPVS

|无|
|存储插件|支持CSI Plugin|支持CSI Plugin|支持CSI Plugin|无|
|容器RootFS|支持OverlayFS|支持OverlayFS|支持配置磁盘Quota的OverlayFS|无|

## 运行时部署结构对比

|运行时|部署结构|
|---|----|
|Docker|```
kubelet -> dockerd -> containerd -> containerd-shim -> runC容器
``` |
|Containerd|```
kubelet -> containerd -> containerd-shim -> runC容器
``` |
|安全沙箱v2|```
kubelet -> (CRI)containerd
                          \-> containerd-shim -> runC容器
                          \-> containerd-shim-rund-v2 -> runV安全沙箱容器
``` |

## Docker和Containerd两种容器引擎常用命令对比

Docker运行时和安全沙箱运行时的容器引擎分别是Docker和Containerd。这两种容器引擎都有各自的命令工具来管理镜像和容器。两种容器引擎常用命令对比如下。

|命令|Docker|Containerd|
|docker|crictl（推荐）|ctr|
|--|------|----------|
|------|----------|---|
|查看容器列表|`docker ps`|`crictl ps`|`ctr -n k8s.io c ls`|
|查看容器详情|`docker inspect`|`crictl inspect`|`ctr -n k8s.io c info`|
|查看容器日志|`docker logs`|`crictl logs`|无|
|容器内执行命令|`docker exec`|`crictl exec`|无|
|挂载容器|`docker attach`|`crictl attach`|无|
|显示容器资源使用情况|`docker stats`|`crictl stats`|无|
|创建容器|`docker create`|`crictl create`|`ctr -n k8s.io c create`|
|启动容器|`docker start`|`crictl start`|`ctr -n k8s.io run`|
|停止容器|`docker stop`|`crictl stop`|无|
|删除容器|`docker rm`|`crictl rm`|`ctr -n k8s.io c del`|
|查看镜像列表|`docker images`|`crictl images`|`ctr -n k8s.io i ls`|
|查看镜像详情|`docker inspect`|`crictl inspecti`|无|
|拉取镜像|`docker pull`|`crictl pull`|`ctr -n k8s.io i pull`|
|推送镜像|`docker push`|无|`ctr -n k8s.io i push`|
|删除镜像|`docker rmi`|`crictl rmi`|`ctr -n k8s.io i rm`|
|查看Pod列表|无|`crictl pods`|无|
|查看Pod详情|无|`crictl inspectp`|无|
|启动Pod|无|`crictl runp`|无|
|停止Pod|无|`crictl stopp`|无|

