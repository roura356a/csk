---
keyword: [node-local-dns, dns, 服务发现, dnsConfig]
---

# 在ACK集群中部署Node Local DNS

在ACK集群中部署Node Local DNS可以提升服务发现的稳定性和性能，Node Local DNS通过在集群节点上作为DaemonSet运行DNS缓存代理来提高集群DNS性能。本文介绍如何为应用部署Node Local DNS。

在ACK集群中部署Node Local DNS前，请确保您已完成以下操作：

-   您已创建一个ACK专有版或者托管版集群。具体操作，请参见[创建Kubernetes托管版集群](/cn.zh-CN/Kubernetes集群用户指南/集群管理/创建集群/创建Kubernetes托管版集群.md)。
-   您可以使用kubectl命令连接集群。具体操作，请参见[通过kubectl连接Kubernetes集群](/cn.zh-CN/Kubernetes集群用户指南/集群管理/连接集群/通过kubectl连接Kubernetes集群.md)。

## Node Local DNS简介

ACK中的Node Local DNS组件由一个Admission Controller Deployment和一组DNS缓存DaemonSet组成。

-   Admission Controller Deployment可以被用来拦截Pod创建的请求，自动注入使用DNS缓存的Pod dnsConfig信息。

    **说明：** 如果不启用Admission Controller Deployment，您需要手动注入DNS域名相关配置至Pod配置中。具体操作，请参见[第四步](#step_310_r09_qbx)提及的**说明**页签中的**手动指定DNSConfig**。

-   DNS缓存DaemonSet可以在每个节点上创建一个虚拟网络接口（默认监听IP 169.254.20.10，如需修改请[提交工单](https://selfservice.console.aliyun.com/ticket/createIndex)咨询），配合Pod的DNSConfig和节点上的网络配置，Pod内产生的DNS请求会被该DaemonSet服务所代理。

    **说明：** DaemonSet服务内同样基于CoreDNS提供服务，但其仅具有代理和缓存的功能，请勿启用额外的插件能力（如Hosts、Rewrite等）。


![原理图](../images/p199061.png "Node Local DNS工作原理图")

|序号|描述|
|--|--|
|①|已注入DNS本地缓存的Pod，默认会通过Node Local DNS监听于节点上的IP（169.254.20.10）解析域名。|
|②|Node Local DNS本地若无缓存应答解析请求，则会通过node-local-upstream服务请求CoreDNS进行解析。|
|③|CoreDNS对于非集群内域名，会通过VPC DNS服务器进行解析。|
|④|已注入DNS本地缓存的Pod，当无法连通Node Local DNS时，会继而直接通过kube-dns服务连接到CoreDNS进行解析，此链路为备用链路。|
|⑤|未注入DNS本地缓存的Pod，会通过标准的kube-dns服务链路连接到CoreDNS进行解析。|

## 步骤一：安装Node Local DNS

1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。

2.  在控制台左侧导航栏中，选择**市场** \> **应用目录**。

3.  在**阿里云应用**页签，找到并单击**ack-node-local-dns**。

4.  仔细阅读**说明**页签中关于**ack-node-local-dns**的介绍及参数说明，然后单击**参数**页签，配置参数。

    需设置的参数说明如下表。

    |参数|描述|获取路径|
    |--|--|----|
    |upstream\_ip|kube-system命名空间下kube-upstream-ip服务的ClusterIP。Node Local DNS通过该服务与CoreDNS通信来解析集群内域名。|您可以在集群**基本信息**中查看集群的Service CIDR，设置一个该网段尚未使用的IP。关于如何查看集群的基本信息，请参见[查看基本信息](/cn.zh-CN/Kubernetes集群用户指南/集群管理/查看集群信息.md)。**说明：** upstream\_ip参数，需被设置成集群Service网段尚未被使用到的任一IP。 |
    |clusterDomain|集群主域名。|查看节点上kubelet进程的--cluster-domain参数，默认为cluster.local。|

5.  在右侧**创建**面板中，选择目标集群、命名空间、和设置发布名称，然后单击**创建**。


## 步骤二：将Node Local DNS接入至应用

本文通过在default命名空间下部署Deployment类型示例应用，演示如何部署Node Local DNS。

1.  执行以下命令给接入Node Local DNS的应用所在的命名空间（default）设置标签。

    ```
    kubectl label namespace default node-local-dns-injection=enabled
    ```

    **说明：** Admission Controller会忽略kube-system和kube-public命名空间下的应用，请勿在这两个命名空间下进行自动注入dnsConfig操作。

2.  在集群的default命名空间下部署示例应用。

    1.  使用以下YAML模板创建名为ubuntu-deployment的示例应用。

        ```
        apiVersion: apps/v1 # for versions before 1.8.0 use apps/v1beta1
        kind: Deployment
        metadata:
          name: ubuntu
          labels:
            app: ubuntu
        spec:
          replicas: 2
          selector:
            matchLabels:
              app: ubuntu
          template:
            metadata:
              labels:
                app: ubuntu
            spec:
              containers:
              - name: ubuntu
                image: ubuntu
                command: ["sh", "-c"]
                args: ["sleep 100000"]
        ```

    2.  执行以下命令使示例应用在集群中生效。

        ```
        kubectl apply -f ubuntu-deployment.yaml
        ```

        输出：

        ```
        deployment.apps/ubuntu created
        ```

    3.  执行以下命令查看应用信息。

        ```
        kubectl get deployment ubuntu
        ```

        输出：

        ```
        NAME     READY   UP-TO-DATE   AVAILABLE   AGE
        ubuntu   2/2     2            2           7s
        ```

3.  查看是否成功注入dnsConfig。

    1.  执行以下命令查看Pod信息。

        ```
        kubectl get pods
        ```

        输出：

        ```
        NAME                      READY   STATUS    RESTARTS   AGE
        ubuntu-766448f68c-mj8qk   1/1     Running   0          4m39s
        ubuntu-766448f68c-wf5hw   1/1     Running   0          4m39s
        ```

    2.  执行以下命令查看Pod的dnsConfig是否已经接入Node Local DNS。

        输入：

        ```
        kubectl get pod ubuntu-766448f68c-mj8qk -o=jsonpath='{.spec.dnsConfig}'
        ```

        输出：

        ```
        map[nameservers:[169.254.20.10 172.21.0.10] options:[map[name:ndots value:5]] searches:[default.svc.cluster.local svc.cluster.local cluster.local]]
        ```

        当输出以上内容时，说明成功为应用接入Node Local DNS。

        开启自动注入后，您创建的Pod会被增加以下字段，为了最大程度上保证业务DNS请求高可用，nameservers中会额外加入kube-dns服务的ClusterIP地址（172.21.0.10）作为备份的DNS服务器。

        ```
          dnsConfig:
            nameservers:
            - 169.254.20.10
            - 172.21.0.10
            options:
            - name: ndots
              value: "3"
            - name: attempts
              value: "2"
            - name: timeout
              value: "1"
            searches:
            - default.svc.cluster.local
            - svc.cluster.local
            - cluster.local
          dnsPolicy: None
        ```


## 未注入DNSConfig问题排查

自动注入需满足以下条件，请检查是否满足：

-   新建Pod不位于kube-system和kube-public命名空间。
-   新建Pod所在命名空间的Labels标签包含`node-local-dns-injection=enabled`。
-   新建Pod所在命名空间的Labels不包含ECI Pod相关标签，例如`virtual-node-affinity-injection`、`eci`、`alibabacloud.com/eci`等。
-   新建Pod没有被打上`eci`、`alibabacloud.com/eci`等ECI相关标签，或打上禁用DNS注入`node-local-dns-injection=disabled`标签。
-   新建Pod的网络为`hostNetwork`且DNSPolicy为`ClusterFirstWithHostNet`，或Pod为非`hostNetwork`且DNSPolicy为`ClusterFirst`。

**相关文档**  


[基于DNS的服务发现概述](/cn.zh-CN/Kubernetes集群用户指南/网络管理/服务发现DNS/基于DNS的服务发现概述.md)

[ACK集群内DNS原理和配置说明](/cn.zh-CN/Kubernetes集群用户指南/网络管理/服务发现DNS/ACK集群内DNS原理和配置说明.md)

[优化集群DNS性能](/cn.zh-CN/Kubernetes集群用户指南/网络管理/服务发现DNS/优化集群DNS性能.md)

