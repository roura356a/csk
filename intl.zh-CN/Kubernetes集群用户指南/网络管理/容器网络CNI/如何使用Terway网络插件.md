# 如何使用Terway网络插件

本文介绍如何使用阿里云容器服务Kubernetes版集群的Terway网络插件。

## Terway网络插件

Terway网络插件是阿里云容器服务自研的网络插件，使用原生的弹性网卡分配给Pod实现Pod网络：

-   将阿里云的弹性网卡和辅助IP分配给容器。
-   支持基于Kubernetes标准的Network Policy来定义容器间的访问策略，兼容Calico的Network Policy。

![Terway网络插件](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/4385659951/p32414.png)

在Terway网络插件中，每个Pod拥有自己网络栈和IP地址。同一台ECS内的Pod之间通信，直接通过机器内部的转发，跨ECS的Pod通信，报文通过VPC的弹性网卡直接转发。由于不需要使用VxLAN等的隧道技术封装报文，因此具有较高的通信性能。

## 注意事项

-   如果要使用Terway插件，建议您选择较高规格和较新类型的ECS机型，即5代或者6代的8核以上机型。
-   单节点所支持的最大Pod数取决于该节点的弹性网卡（ENI）数。

    -   共享ENI支持的最大Pod数 =（ECS支持的ENI数-1）×单个ENI支持的私有IP数
    -   独占ENI支持的最大Pod数 = ECS支持的ENI数－1
    **说明：** 在创建集群或扩容集群的选择实例规格区域，可以查看ECS支持的ENI数量。


## 使用Terway网络插件

1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。

2.  在控制台左侧导航栏中，单击**集群**。

3.  单击页面右上角的**创建集群**。

4.  在选择集群模板页面，单击**标准版集群**。

    **说明：** 本文以创建标准托班集群为例，具体步骤请参见[创建Kubernetes托管版集群](/intl.zh-CN/Kubernetes集群用户指南/集群管理/创建集群/创建Kubernetes托管版集群.md)。

5.  设置启动用的网络插件，选择**Terway**。

    ![设置网络插件](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/9888232061/p32375.png)

    网络插件选择**Terway**时，需要配置**Terway模式**：

    -   设置是否选中**Pod独占弹性网卡以获得最佳性能**

-   如果选中，Pod将独占一个专有的弹性网卡。
-   如果不选中，使用弹性网卡的辅助IP分配给Pod，一个Pod占用一个弹性网卡辅助IP地址。
    -   设置是否选中**IPvlan**

-   只在弹性网卡共享模式支持选中。
-   如果选中，采用IPVLAN eBPF作为网卡共享模式虚拟化技术，并且只能使用Alibaba Cloud Linux 2系统，性能优于默认模式。
-   如果不选中，则使用默认模式，采用策略路由作为网卡共享模式虚拟化技术，同时兼容Centos 7和Alibaba Cloud Linux 2的系统。
**说明：** 当前只有白名单用户可使用上述**Pod独占弹性网卡以获得最佳性能**和**IPvlan**两种功能。[提交工单](https://workorder-intl.console.aliyun.com/console.htm)申请使用。

    -   设置是否选中**NetworkPolicy支持**

-   只在弹性网卡共享模式下支持选中，默认不选中。
-   如果选中，集群支持使用Kubernetes的NetworkPolicy策略对Pod进行网络控制。
-   如果不选中，集群将不会支持使用Kubernetes的NetworkPolicy策略对Pod进行网络控制，这样将不存在网络策略对Kubernetes的API Server产生过大的负载。

## 扩容Terway场景下的VSwitch

Terway网络场景下，当VSwitch IP资源不足时，您需要对VSwitch扩容。VSwitch IP资源不足的特征如下。

-   如果您发现Pod创建不成功，且状态显示为ContainerCreating，执行以下命令查看Pod所在节点的Terway的日志。

    ```
    kubectl logs --tail=100 -f terway-eniip-zwjwx -n kube-system -c terway
    ```

    若系统输出类似以下错误信息，说明该节点的Terway所使用的VSwitch没有空余的IP地址，Pod会因为没有IP资源而一直处于ContainerCreating状态。

    ```
    time="2020-03-17T07:03:40Z" level=warning msg="Assign private ip address failed: Aliyun API Error: RequestId: 2095E971-E473-4BA0-853F-0C41CF52651D Status Code: 403 Code: InvalidVSwitchId.IpNotEnough Message: The specified VSwitch \"vsw-AAA\" has not enough IpAddress., retrying"
    ```

-   登录[专有网络管理控制台](https://vpc.console.aliyun.com/)，在控制台左侧导航栏中，单击**交换机**查看该VSwitch的**可用IP数**为0。

您可通过执行以下操作步骤新增VSwitch，以增加新的IP地址资源供ACK集群使用。

1.  在[专有网络管理控制台](https://vpc.console.aliyun.com/)创建新的VSwitch，该VSwitch必须与IP资源不足的VSwitch在同一个区域。具体关于如何创建VSwitch，请参见[创建交换机](/intl.zh-CN/专有网络和交换机/管理交换机/创建交换机.md)。

    **说明：** 因Pod密度越来越大，为了满足Pod对IP地址日益增长的需求，建议您创建给Pod使用的VSwitch的网络位小于等于19，即网段中至少包含8192个IP地址。

2.  执行以下命令，添加VSwitch到Terway的ConfigMap配置中。

    ```
    kubectl edit cm eni-config -n kube-system
    ```

    添加VSwitch具体样例如下：

    ```
    eni_conf: |
    {
    "version": "1",
    "max_pool_size": 25,
    "min_pool_size": 10,
    "vswitches": {"cn-shanghai-f":["vsw-AAA", "vsw-BBB"]},
    "service_cidr": "172.21.0.0/20",
    "security_group": "sg-CCC"
    }
    ```

    样例中添加`vsw-BBB`到`VSwitches`部分，其中`vsw-AAA`是已经存在的且IP资源不足的VSwitch。

3.  如果Terway版本不是最新的，您需要执行步骤3~6，将Terway版本升到最新版本，否则直接跳转至步骤7继续执行操作。
4.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。

5.  在控制台左侧导航栏中，单击**集群**。

6.  在集群列表页面中，单击目标集群名称或者目标集群右侧**操作**列下的**详情**。

7.  在控制台左侧导航栏中，选择**运维管理** \> **组件管理**，然后将Terway版本升到最新版本。

    1.  在组件管理页面，单击**网络**页签。

    2.  在网络页签中选择需要升级的目标Terway组件，单击**升级**。

8.  执行以下命令删除全部Terway Pod后，系统自动重建全部Terway Pod。

    -   针对ENI多IP场景，执行以下命令删除全部Terway Pod：

        ```
        kubectl delete -n kube-system pod -l app=terway-eniip
        ```

    -   针对ENI单IP场景，执行以下命令删除全部Terway Pod：

        ```
        kubectl delete -n kube-system pod -l app=terway-eni
        ```

    执行以下命令检查全部是否成功重建Terway Pod。

    ```
    kubectl get pod -n kube-system  | grep terway
    ```

9.  创建Pod，验证新创建的Pod是否可以从新添加的VSwitch成功分配获得IP地址。


如果您在上述操作中遇到异常情况，请[提交工单]()。

## Flannel与Terway

在创建Kubernetes集群时，阿里云容器服务提供两种网络插件：Terway和Flannel。

-   Flannel：使用的是简单稳定的社区的[Flannel](https://github.com/coreos/flannel) CNI插件，配合阿里云的VPC的高速网络，能给集群高性能和稳定的容器网络体验，但功能偏简单，支持的特性少。例如，不支持基于Kubernetes标准的Network Policy。
-   Terway：是阿里云容器服务Kubernetes版自研的网络插件，将阿里云的弹性网卡分配给容器，支持基于Kubernetes标准的Network Policy来定义容器间的访问策略，支持对单个容器做带宽的限流。对于不需要使用Network Policy的用户，可以选择Flannel，其他情况建议选择Terway。

    **说明：**

    -   Terway的Network Policy是通过集成Calico的Felix组件实现的，因此Network Policy的能力和Calico完全一致。对于最初为了使用Calico而自建集群的客户，目前可以通过Terway转换到容器服务Kubernetes版上来。
    -   Terway目前集成的Felix版本为3.5.2。

