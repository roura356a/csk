# ACK发布Kubernetes 1.12版本说明

容器服务所用 Kubernetes版本和社区完全兼容，在核心代码层面和社区版保持一致。本文介绍容器服务的Kubernetes版本在社区版本基础上所做的变更的说明。

## 1.12.6-aliyun.1

-   屏蔽 apiserver kubelet的TLS handshake error日志（SLB健康检查产生的TLS握手日志）。

    CommitID：4f1d96e153b050d8374bfbb66803d7b3d9181abe

-   kubeadm 支持部署18.09.2的Docker版本号校验 。

    CommitID：3b1ebfa1b857c44f5261a36f1420b10a08e01771

-   调整aggregationcontroller的Watch的日志级别 。

    CommitID：01a904eed3f9486caa482c8983698075d1cea2f1

-   Kubeadm
    -   更新集群资源时，Kubeadm增加Retry。
    -   kubeadm不再部署DNS。
    -   kubeadm不再部署kubeproxy。
    -   kubeadm生成的证书，证书时效调整为十年。

参考[社区版Kubernetes 1.12.6 Release Notes](https://github.com/kubernetes/kubernetes/blob/master/CHANGELOG-1.12.md#v1126)。

## 参考链接

-   [CHANGELOG-1.12.md](https://github.com/kubernetes/kubernetes/blob/master/CHANGELOG/CHANGELOG-1.12.md)
-   [CHANGELOG-1.11.md](https://github.com/kubernetes/kubernetes/blob/master/CHANGELOG/CHANGELOG-1.11.md)

