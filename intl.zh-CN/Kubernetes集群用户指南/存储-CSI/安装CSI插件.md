---
keyword: [CSI插件, Kubernetes, CSI-Plugin, CSI-Provisioner]
---

# 安装CSI插件

CSI插件包括CSI-Plugin和CSI-Provisioner两部分。您可以在容器服务Kubernetes版Container Service for Kubernetes（ACK）中，安装CSI-Plugin和CSI-Provisioner存储插件。

**说明：** CSI插件需要您的集群版本大于1.14，且初始化集群的时候选择使用CSI插件。或者自行配置节点kubelet参数，确保`enable-controller-attach-detach`为true。

## 注意事项

-   使用Flexvolume需要kubelet关闭`--enable-controller-attach-detach`选项。默认阿里云Kubernetes集群已经关闭此选项。
-   在kube-system命名空间部署CSI。

## 安装CSI-Plugin和CSI-Provisioner

ACK托管版和专有版集群默认安装CSI-Plugin和CSI-Provisioner。如果您的集群中没有安装该插件，您可以手动安装。具体操作，请参见[alibaba-cloud-csi-driver](https://github.com/kubernetes-sigs/alibaba-cloud-csi-driver/blob/master/README.md)。

## 验证安装

您可以连接ACK集群验证CSI插件的安装情况，请确保在节点中可以使用kubectl命令行正常连接集群。关于如何连接集群的步骤，请参见[通过kubectl连接Kubernetes集群](/intl.zh-CN/Kubernetes集群用户指南/集群/连接集群/通过kubectl连接Kubernetes集群.md)。

-   执行以下命令，输出若干（节点个数）Running状态的Pod列表。

    ```
    kubectl get pod -n kube-system | grep csi-plugin
    ```

-   执行以下命令，输出一个Running状态的Pod列表。

    ```
    kubectl get pod -n kube-system | grep csi-provisioner
    ```


