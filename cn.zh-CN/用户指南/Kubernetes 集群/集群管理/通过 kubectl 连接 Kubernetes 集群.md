# 通过 kubectl 连接 Kubernetes 集群 {#task_ubf_lhg_vdb .task}

如果您需要从客户端计算机连接到 Kubernetes 集群，请使用 Kubernetes 命令行客户端 [kubectl](https://kubernetes.io/docs/user-guide/kubectl/)。

1.  从[Kubernetes 版本页面](https://github.com/kubernetes/kubernetes/blob/master/CHANGELOG.md) 下载最新的 kubectl 客户端。 
2.  安装和设置 kubectl 客户端。 

    有关详细信息，参见 [安装和设置 kubectl](https://kubernetes.io/docs/tasks/kubectl/install/)。

3.  配置集群凭据。 

    您可以使用scp命令安全地将主节点的配置从 Kubernetes 集群主 VM 中的 `/etc/kubernetes/kube.conf` 复制到本地计算机的 `$HOME/.kube/config`（`kubectl` 预期凭据所在的位置）。

    -   如果您在创建集群时选择密码方式登录，请用以下方式拷贝 kubectl 配置文件。

        ```
        mkdir $HOME/.kube
        scp root@<master-public-ip>:/etc/kubernetes/kube.conf $HOME/.kube/config
        ```

    -   如果您在创建集群时选择密钥对方式登录，请用以下方式拷贝 kubectl 配置文件。

        ```
        mkdir $HOME/.kube
        scp -i [.pem 私钥文件在本地机器上的存储路径] root@:/etc/kubernetes/kube.conf $HOME/.kube/config
        ```

    您可以在集群信息页面查看集群的 `master-public-ip`。

    1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。 
    2.  单击**Kubernetes**进入 Kubernetes 集群列表页面。 
    3.  选择所需的集群并单击右侧的**管理** 

        您可以在**连接信息**处查看集群的连接地址。

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16645/15382910609791_zh-CN.png)


