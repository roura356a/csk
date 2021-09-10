---
keyword: [node-local-dns, NodeLocal DNSCache, DaemonSet]
---

# 在ACK集群中使用NodeLocal DNSCache

在ACK集群中部署NodeLocal DNSCache可以提升服务发现的稳定性和性能，NodeLocal DNSCache通过在集群节点上作为DaemonSet运行DNS缓存代理来提高集群DNS性能。本文介绍如何安装NodeLocal DNSCache，并在应用中使用NodeLocal DNSCache。

-   已创建ACK集群。具体操作，请参见[创建Kubernetes托管版集群](/cn.zh-CN/Kubernetes集群用户指南/集群/创建集群/创建Kubernetes托管版集群.md)。
-   使用kubectl命令连接集群。具体操作，请参见[通过kubectl工具连接集群](/cn.zh-CN/Kubernetes集群用户指南/集群/连接集群/通过kubectl工具连接集群.md)。

## 使用限制

-   NodeLocal DNSCache不支持ASK集群、以及托管版、专有版集群中部署的ECI类型的Pod。
-   如果集群网络类型为Terway，请更新Terway至v1.0.10.301及以上版本。如果是基于IPVLAN的Terway多IP模式，需要对Terway进行配置修改。更多信息，请参见[修改Terway配置](#section_wcp_x4c_oyc)。
-   组件管理中的NodeLocal DNSCache和应用目录中的ack-node-local-dns为同一应用，不需要重复安装。
-   NodeLocal DNSCache不提供Hosts、Rewrite等插件能力，仅作为CoreDNS的透明缓存代理。如有需要，可在CoreDNS配置中修改。
-   使用NodeLocal DNSCache前，您需要对CoreDNS配置进行修改，否则可能导致集群中PrivateZone域名或部分云产品提供的\*.vpc-proxy.aliyuncs.com等域名无法解析。具体操作，请参见[CoreDNS修改说明](#section_ajk_4vf_oqk)。

## NodeLocal DNSCache简介

ACK NodeLocal DNSCache是基于社区开源项目NodeLocal DNSCache的一套DNS本地缓存解决方案。ACK NodeLocal DNSCache在Helm Chart名称是ack-node-local-dns。该方案主要由DNS本地缓存DaemonSet和DNSConfig动态注入控制器Deployment组成：

-   DNSConfig动态注入控制器Deployment，基于Admission Webhook机制拦截Pod创建的请求，自动注入使用DNS缓存的Pod DNSConfig信息。

    **说明：** 如果不启用DNSConfig动态注入控制器Deployment，您需要手动注入DNS域名相关配置至Pod配置中。具体操作，请参见[在应用中使用NodeLocal DNSCache](#section_kf0_5uj_ay2)下的方式二：手动指定DNSConfig。

-   DNS缓存DaemonSet可以在每个节点上创建一个虚拟网络接口（默认监听IP 169.254.20.10，如需修改请[提交工单](https://selfservice.console.aliyun.com/ticket/createIndex)咨询），配合Pod的DNSConfig和节点上的网络配置，Pod内产生的DNS请求会被该DaemonSet服务所代理。

    **说明：** DaemonSet服务内同样基于CoreDNS提供服务，但其仅具有代理和缓存的功能，请勿启用额外的插件能力（如Hosts、Rewrite等）。


![工作原理](../images/p266014.png "NodeLocal DNSCache工作原理图")

|序号|描述|
|--|--|
|①|已注入DNS本地缓存的Pod，默认会通过NodeLocal DNSCache监听于节点上的IP（169.254.20.10）解析域名。|
|②|NodeLocal DNSCache本地若无缓存应答解析请求，则会通过kube-dns服务请求CoreDNS进行解析。|
|③|CoreDNS对于非集群内域名，会通过VPC DNS服务器进行解析。|
|④|已注入DNS本地缓存的Pod，当无法连通NodeLocal DNSCache时，会继而直接通过kube-dns服务连接到CoreDNS进行解析，此链路为备用链路。|
|⑤|未注入DNS本地缓存的Pod，会通过标准的kube-dns服务链路连接到CoreDNS进行解析。|

## 安装NodeLocal DNSCache

您可以通过组件管理或者应用目录页面安装NodeLocal DNSCache，建议您通过组件管理安装NodeLocal DNSCache。

**通过组件管理安装NodeLocal DNSCache**

1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。

2.  在控制台左侧导航栏中，单击**集群**。

3.  在集群列表页面中，选择目标集群名称或者目标集群右侧**操作**列下的**更多** \> **系统组件管理**。

4.  在组件管理页面单击**网络**页签，找到ACK NodeLocal DNSCache。

5.  单击ACK NodeLocal DNSCache的**安装**，在弹出的对话框中单击**确定**。


**通过应用目录安装NodeLocal DNSCache**

1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。

2.  在控制台左侧导航栏中，选择**市场** \> **应用目录**。

3.  在应用目录页面搜索ack-node-local-dns，找到并单击**ack-node-local-dns**。

4.  在**创建**面板选择集群。

5.  仔细阅读**说明**页签中关于**ack-node-local-dns**的介绍及参数说明，然后单击**参数**页签，配置参数。

    需设置的参数说明如下表。

    |参数|描述|获取路径|
    |--|--|----|
    |upstream\_ip|kube-system命名空间下 kube-dns服务的ClusterIP。NodeLocal DNSCache通过该服务与CoreDNS通信来解析集群内域名。|如果您需要指定不同的上游DNS服务器，可以修改此参数。|
    |clusterDomain|集群主域名。|查看节点上kubelet进程的--cluster-domain参数，默认为cluster.local。|

6.  在右侧**创建**面板单击**创建**。


## 在应用中使用NodeLocal DNSCache

**说明：** NodeLocal DNSCache默认不会部署至Master节点。如果您的业务需要部署至Master节点，且您的Master节点设置了污点，您需要修改命名空间kube-system下node-local-dns DaemonSet的污点容忍。

为了能使应用原本请求CoreDNS的流量改为由DNS缓存DaemonSet代理，需要使Pod内部的中nameservers配置成169.254.20.10和kube-dns对应的IP地址，您有以下几种方式可以选择：

-   方式一：借助DNSConfig动态注入控制器在Pod创建时配置DNSConfig自动注入，推荐使用此方式。
-   方式二：创建Pod时手动指定DNSConfig。
-   方式三：修改kubelet参数，并重启节点kubelet。存在业务中断风险，不推荐使用此方式。

**方式一：配置DNSConfig自动注入**

DNSConfig动态注入控制器可用于自动注入DNSConfig至新建的Pod中，避免您手工配置Pod YAML进行注入。本应用默认会监听包含`node-local-dns-injection=enabled`标签的命名空间中新建Pod的请求，您可以通过以下命令给命名空间打上Label标签。

```
kubectl label namespace default node-local-dns-injection=enabled
```

**说明：**

-   上述命令仅会开启default命名空间的自动注入，如需对其它命名空间开启自动注入，则需要替换`default`为目标命名空间名称。
-   在命名空间DNSConfig自动注入开启的情况下，如需对部分Pod进行豁免（即不进行注入），可以调整其Pod Template中Labels标签字段，加上`node-local-dns-injection=disabled`标签。

开启自动注入后，您创建的Pod会被增加以下字段，为了最大程度上保证业务DNS请求高可用，nameservers中会额外加入kube-dns的ClusterIP地址作为备份的DNS服务器。

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

自动注入需满足以下条件。如果自动注入失败，请检查是否满足：

-   新建Pod不位于kube-system和kube-public命名空间。
-   新建Pod所在命名空间的Labels标签包含`node-local-dns-injection=enabled`。
-   新建Pod所在命名空间的Labels不包含ECI Pod相关标签，例如`virtual-node-affinity-injection`、`eci`、`alibabacloud.com/eci`等。
-   新建Pod没有被打上`eci`、`alibabacloud.com/eci`等ECI相关标签，或打上禁用DNS注入`node-local-dns-injection=disabled`标签。
-   新建Pod的网络为`hostNetwork`且DNSPolicy为`ClusterFirstWithHostNet`，或Pod为非`hostNetwork`且DNSPolicy为`ClusterFirst`。

**方式二：手动指定DNSConfig**

如果不适用Admission Webhook Deployment自动注入，您可以通过修改Pod进行手动指定DNSConfig。

```
apiVersion: v1
kind: Pod
metadata:
  name: alpine
  namespace: default
spec:
  containers:
  - image: alpine
    command:
      - sleep
      - "10000"
    imagePullPolicy: Always
    name: alpine
  dnsPolicy: None
  dnsConfig:
    nameservers: ["169.254.20.10","172.21.0.10"]
    searches:
    - default.svc.cluster.local
    - svc.cluster.local
    - cluster.local
    options:
    - name: ndots
      value: "3"
    - name: attempts
      value: "2"
    - name: timeout 
      value: "1"
```

-   dnsPolicy：必须为`None`。
-   nameservers：配置成169.254.20.10和kube-dns的ClusterIP对应的IP地址。
-   searches：设置搜索域，保证集群内部域名能够被正常解析。
-   ndots：默认为5，可以适当降低ndots以提升解析效率。更多信息，请参见[resolv.conf](https://linux.die.net/man/5/resolv.conf)。

**方式三：配置kubelet启动参数**

kubelet通过--cluster-dns和--cluster-domain两个参数来全局控制Pod DNSConfig。在/etc/systemd/system/kubelet.service.d/10-kubeadm.conf配置文件中需要增加一个--cluster-dns参数，设置值为链路本地地址169.254.20.10。修改后执行`systemctl daemon-reload`和`systemctl restart kubelet`来生效。

```
--cluster-dns=169.254.20.10 --cluster-dns=<kube-dns ip> --cluster-domain=<search domain>
```

-   cluster-dns：部署Pod时，默认采用的DNS服务器地址，默认只引用了``kube-dns``的服务IP，需要增加一个对链路本地地址169.254.20.10的引用。
-   cluster-domain：部署Pod时，默认采用的DNS搜索域，保持原有搜索域即可，一般为``cluster.local``。

**配置应用使用NodeLocal DNSCache示例**

通过在default命名空间下部署Deployment类型示例应用，演示如何配置应用使用NodeLocal DNSCache。

1.  执行以下命令，给接入NodeLocal DNSCache的应用所在的命名空间（default）设置标签。

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

    2.  执行以下命令，使示例应用在集群中生效。

        ```
        kubectl apply -f ubuntu-deployment.yaml
        ```

        预期输出：

        ```
        deployment.apps/ubuntu created
        ```

    3.  执行以下命令，查看应用信息。

        ```
        kubectl get deployment ubuntu
        ```

        预期输出：

        ```
        NAME     READY   UP-TO-DATE   AVAILABLE   AGE
        ubuntu   2/2     2            2           7s
        ```

3.  查看是否成功注入dnsConfig。

    1.  执行以下命令，查看Pod信息。

        ```
        kubectl get pods
        ```

        预期输出：

        ```
        NAME                      READY   STATUS    RESTARTS   AGE
        ubuntu-766448f68c-mj8qk   1/1     Running   0          4m39s
        ubuntu-766448f68c-wf5hw   1/1     Running   0          4m39s
        ```

    2.  执行以下命令，查看Pod的dnsConfig是否已经接入NodeLocal DNSCache。

        ```
        kubectl get pod ubuntu-766448f68c-mj8qk -o=jsonpath='{.spec.dnsConfig}'
        ```

        预期输出：

        ```
        map[nameservers:[169.254.20.10 172.21.0.10] options:[map[name:ndots value:5]] searches:[default.svc.cluster.local svc.cluster.local cluster.local]]
        ```

        当输出以上内容时，说明成功为应用接入NodeLocal DNSCache。

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


## 升级NodeLocal DNSCache

如果您是在组件管理页面安装NodeLocal DNSCache，您需要在组件管理页面升级NodeLocal DNSCache。如果您是在应用目录页面安装NodeLocal DNSCache，您需要在Helm页面卸载NodeLocal DNSCache，然后再重新安装NodeLocal DNSCache。

**通过组件管理页面升级NodeLocal DNSCache**

1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。

2.  在控制台左侧导航栏中，单击**集群**。

3.  在集群列表页面中，单击目标集群名称或者目标集群右侧**操作**列下的**详情**。

4.  在集群管理页左侧导航栏中，选择**运维管理** \> **组件管理**。

5.  在组件管理页面单击NodeLocal DNSCache下的**升级**，在弹出的对话框中单击**确定**。

    **说明：** 若您对node-local-dns DaemonSet进行过污点容忍等修改操作，升级时污点容忍等操作将会被覆盖，升级后需要再次设置。


**通过应用目录页面升级NodeLocal DNSCache**

如果您是通过应用目录页面安装NodeLocal DNSCache，您可以先卸载NodeLocal DNSCache，再重新安装。具体操作，请参见[卸载NodeLocal DNSCache](#step_9sz_4y8_vju)和[安装NodeLocal DNSCache](#section_rzr_wqx_ss3)。

## 卸载NodeLocal DNSCache

如果您是在组件管理页面安装NodeLocal DNSCache，您需要在组件管理页面卸载NodeLocal DNSCache。如果您是在应用目录页面安装NodeLocal DNSCache，您需要在Helm页面卸载NodeLocal DNSCache。

**通过组件管理页面卸载NodeLocal DNSCache**

1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。

2.  在控制台左侧导航栏中，单击**集群**。

3.  在集群列表页面中，单击目标集群名称或者目标集群右侧**操作**列下的**详情**。

4.  在集群管理页左侧导航栏中，选择**运维管理** \> **组件管理**。

5.  在组件管理页面单击NodeLocal DNSCache下的**卸载**，在弹出的对话框中单击**确定**。

    **说明：** 卸载后所有解析流量会请求至CoreDNS，建议提前对CoreDNS副本数进行扩容。


**通过应用目录页面卸载NodeLocal DNSCache**

1.  卸载前检查。

    1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。

    2.  在控制台左侧导航栏中，单击**集群**。

    3.  在集群列表页面中，单击目标集群名称或者目标集群右侧**操作**列下的**详情**。

    4.  在集群管理页左侧导航栏选择**应用** \> **Helm**。

    5.  在Helm页面单击**ack-node-local-dns-default**。

    6.  单击**参数**页签，检查参数。

        -   如果本地监听IP地址（local\_dns\_ip）等同于kube-dns的ClusterIP地址，则您可以执行[步骤2](#step_9sz_4y8_vju)卸载NodeLocal DNSCache。
        -   如果高可用开关（high\_availability）为打开状态，且本地监听IP地址（local\_dns\_ip）为169.254.20.10。则您可以执行[步骤2](#step_9sz_4y8_vju)卸载NodeLocal DNSCache。
        -   如果参数不满足以上条件，请按照以下流程进行卸载：
            1.  执行`kubectl get ns -o yaml`命令，检查哪些命名空间被打上了node-local-dns-injection=enabled的标签，并将标签删除。
            2.  执行`kubectl get pod -o yaml`命令，检查哪些业务Pod被注入DNSConfig，删除这些Pod并重建。
            3.  执行[步骤2](#step_9sz_4y8_vju)卸载NodeLocal DNSCache。
2.  卸载NodeLocal DNSCache。

    1.  在控制台左侧导航栏中，选择**市场** \> **应用目录**。

    2.  在控制台左侧导航栏中，单击**集群**。

    3.  在集群列表页面中，单击目标集群名称或者目标集群右侧**操作**列下的**详情**。

    4.  在集群管理页左侧导航栏选择**应用** \> **Helm**。

    5.  在Helm页面单击ack-node-local-dns-default**操作**列的**删除**，在弹出的对话框中单击**确定**。


## CoreDNS修改说明

NodeLocal DNSCache会默认采用TCP协议与CoreDNS进行通信，CoreDNS会根据请求来源使用的协议与上游进行通信，即与VPC内DNS服务器进行通信。

VPC内DNS服务器目前无法以TCP协议提供PrivateZone域名和部分云产品提供的\*.vpc-proxy.aliyuncs.com域名的解析，因此通过NodeLocal DNSCache解析此类域名时，解析会失败。您需要参考以下方式修改CoreDNS配置，强制CoreDNS默认以UDP协议与上游VPC内DNS服务器进行通信。

建议您使用以下方式修改CoreDNS配置文件，即修改命名空间kube-system下**coredns**。具体操作，请参见[管理配置项](/cn.zh-CN/Serverless Kubernetes集群用户指南/配置项及密钥/管理配置项.md)。在forward插件中指定请求上游的协议为``perfer_udp``，修改之后CoreDNS会优先使用UDP协议与上游通信。修改方式如下所示：

```
# 修改前
forward . /etc/resolv.conf
# 修改后
forward . /etc/resolv.conf {
  prefer_udp
}
```

## 修改Terway配置

1.  执行以下命令，查看Terway配置文件。

    ```
    kubectl -n kube-system edit cm eni-config -o yaml
    ```

2.  检查Terway配置文件。

    -   检查配置文件中`eniip_virtual_type`字段是否开启IPVLAN模式。如果配置文件中此项不存在或不是IPVLAN，则您不需要按后续步骤进行配置，可以直接安装NodeLocal DNSCache。具体操作，请参见[安装NodeLocal DNSCache](#section_rzr_wqx_ss3)。
    -   检查配置文件中是否已配置`host_stack_cidrs`字段。如果配置文件中已配置`host_stack_cidrs`字段，则您不需要按后续步骤进行配置，可以直接安装NodeLocal DNSCache。具体操作，请参见[安装NodeLocal DNSCache](#section_rzr_wqx_ss3)。
3.  在Terway配置文件中添加`host_stack_cidrs`字段，并输入网段169.254.20.10/32，保存并退出。

    ```
     10-terway.conf: |
      {
        "cniVersion": "0.3.0",
        "name": "terway",
        "eniip_virtual_type": "IPVlan",
        "host_stack_cidrs": ["169.254.20.10/32"], 
        "type": "terway"
      }
    ```

4.  执行以下命令，查看当前Terway的所有DaemonSet容器组。

    ```
    kubectl -n kube-system get pod | grep terway-eniip
    ```

    预期输出：

    ```
    terway-eniip-7d56l         2/2     Running   0          30m
    terway-eniip-s7m2t         2/2     Running   0          30m
    ```

5.  执行以下命令，触发Terway容器组重建。

    ```
     kubectl -n kube-system delete pod terway-eniip-7d56l terway-eniip-s7m2t
    ```

6.  登录任意集群节点，执行以下命令，查看Terway配置文件。

    如果Terway配置文件包含添加的网段，则说明配置成功。

    ```
    cat /etc/cni/net.d/*
    ```

    预期输出：

    ```
     {
       "cniVersion": "0.3.0",
       "name": "terway-chainer",
       "plugins": [
         {
           "eniip_virtual_type": "IPVlan",
           "host_stack_cidrs": [ 
             "169.254.20.10/32",
           ],
           "type": "terway"
         },
         {
           "type": "cilium-cni"
         }
       ]
     }
    ```

    所有Terway Pod重建并正常后，您可以继续安装NodeLocal DNSCache了。


**相关文档**  


[基于DNS的服务发现概述](/cn.zh-CN/Kubernetes集群用户指南/网络/服务发现DNS/基于DNS的服务发现概述.md)

[ACK集群内DNS原理和配置说明](/cn.zh-CN/Kubernetes集群用户指南/网络/服务发现DNS/ACK集群内DNS原理和配置说明.md)

[优化集群DNS性能](/cn.zh-CN/Kubernetes集群用户指南/网络/服务发现DNS/优化集群DNS性能.md)

