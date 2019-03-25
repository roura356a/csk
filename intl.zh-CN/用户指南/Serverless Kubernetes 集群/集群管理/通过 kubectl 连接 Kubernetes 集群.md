# 通过 kubectl 连接 Kubernetes 集群 {#task_msl_bc1_ydb .task}

如果您需要从客户端计算机连接到 Kubernetes 集群，请使用 Kubernetes 命令行客户端 kubectl。

1.  从[Kubernetes 版本页面](https://github.com/kubernetes/kubernetes/blob/master/CHANGELOG.md?spm=a2c4g.11186623.2.4.9ZrN6B&file=CHANGELOG.md)下载最新的 kubectl 客户端。 
2.  安装和设置 kubectl 客户端。 

    有关详细信息，参见 [安装和设置 kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/)。

3.  配置集群凭据。 

    您可以在集群信息页面查看集群凭据。

    1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。 
    2.  在 Kubernetes菜单下，单击**集群**进入 Kubernetes 集群列表页面。 
    3.  选择所需的集群并单击右侧的**管理**。 
    4.  您可以在**连接信息**处查看集群的连接地址。 

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16482/155348154110242_zh-CN.png)

    5.  拷贝集群凭据到本地文件中，您可创建并将集群凭据保存到 $HOME/.kube/config（kubectl 预期凭据所在的位置）。或者命名一个新的文件，如 /tmp/kubeconfig，并执行命令 `export KUBECONFIG=/tmp/kubeconfig`。 
    6.  执行上述操作后，您可执行以下命令，确认集群连接情况。 

        ```
        
        #kubectl get pod
        No resources found.
        ```


配置完成后，您即可使用 kubectl 从本地计算机访问 Kubernetes 集群。

