---
keyword: [虚拟交换机vswitch, Terway, 扩容]
---

# 扩容Terway场景下的虚拟交换机VSwitch

Terway网络场景下，当虚拟交换机（VSwitch）IP资源不足时，您需要对VSwitch扩容。本文介绍如何新增虚拟交换机以增加新的IP地址资源供ACK集群使用。

## 虚拟交换机VSwitch的IP资源不足特征

Terway网络场景下，VSwitch IP资源不足的特征如下：

-   如果您发现Pod创建不成功，且状态显示为ContainerCreating，执行以下命令查看Pod所在节点的Terway的日志。

    ```
    kubectl logs --tail=100 -f terway-eniip-zwjwx -n kube-system -c terway
    ```

    若系统输出类似以下错误信息，说明该节点的Terway所使用的VSwitch没有空余的IP地址，Pod会因为没有IP资源而一直处于ContainerCreating状态。

    ```
    time="2020-03-17T07:03:40Z" level=warning msg="Assign private ip address failed: Aliyun API Error: RequestId: 2095E971-E473-4BA0-853F-0C41CF52651D Status Code: 403 Code: InvalidVSwitchId.IpNotEnough Message: The specified VSwitch \"vsw-AAA\" has not enough IpAddress., retrying"
    ```

-   登录[专有网络管理控制台](https://vpc.console.aliyun.com/)，在控制台左侧导航栏中，单击**交换机**查看该VSwitch的**可用IP数**为0。

## 新增虚拟交换机VSwitch

您可通过执行以下操作步骤新增VSwitch。

1.  在[专有网络管理控制台](https://vpc.console.aliyun.com/)创建新的VSwitch，该VSwitch必须与IP资源不足的VSwitch在同一个区域。具体关于如何创建VSwitch，请参见[创建交换机](/cn.zh-CN/专有网络和交换机/管理交换机/创建交换机.md)。

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


如果您在上述操作中遇到异常情况，请[提交工单](https://selfservice.console.aliyun.com/ticket/createIndex)。

