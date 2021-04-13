---
keyword: [CoreDNS版本过低, 升级CoreDNS, 手动]
---

# 升级CoreDNS至1.6.2新版本公告

为了提升ACK集群内域名解析服务的稳定性，建议您升级集群的CoreDNS至1.6.2及以上版本。本文介绍如何手动升级CoreDNS的版本。

低版本CoreDNS（1.4及之前的版本）存在的以下两个问题，影响了ACK 1.16以前版本集群内域名解析服务的稳定性：

-   因为CoreDNS需要通过watch apiserver拿到Service数据，所以health插件会检查CoreDNS与apiserver的连通性。如果连通性不正常（如网络抖动、apiserver重启等情况），健康检查接口会报错。CoreDNS在3次健康检查失败后会重启，重启过程中将导致服务不可以用。详情请参见[coredns](https://github.com/coredns/coredns/issues/2629)。
-   CoreDNS会受到klog缺陷的影响：CoreDNS与apiserver断连后，klog尝试向一个不存在的目录/tmp写错误日志，此行为会失败并进一步导致CoreDNS不可用。详情请参见[add mount of /tmp \#137](https://github.com/coredns/deployment/issues/137)。

您可以选择以下两种升级方案以解决低版本CoreDNS带来的稳定性问题：

-   长期解决方案：升级Kubernetes集群到1.16及以上版本，集群升级时会自动将CoreDNS升级到1.6.2及以上版本。有关在控制台中升级集群的具体操作步骤，请参见[升级集群](/intl.zh-CN/Kubernetes集群用户指南/集群/升级集群/升级集群.md)；有关通过API升级集群的详细信息，请参见[升级集群](/intl.zh-CN/API参考/升级/升级集群.md)。
-   短期解决方案：如果不方便升级集群版本，您可以选择手动升级CoreDNS到1.6.2及以上版本。有关如何手动升级CoreDNS版本的具体步骤，请参见本文[升级步骤](#section_7wd_grs_c5s)。

    **说明：** 对于1.4及之前的版本的CoreDNS，您才需要进行手动升级。


## 升级步骤

1.  确认集群版本与CoreDNS版本的兼容性。

    您需先确认集群版本号，然后进一步确认与CoreDNS 1.6.2版本的兼容性。ACK Kubernetes集群版本与CoreDNS 1.6.2版本兼容情况如下表（Kubernetes 1.11/1.12/1.14/1/16均兼容CoreDNS 1.6.2版本）。

    |版本|兼容信息|
    |--|----|
    |Kubernetes版本|1.11|1.12|1.14|1.16|
    |CoreDNS|1.6.2|1.6.2|1.6.2|1.6.2|

    确认集群版本号的操作步骤如下：

    1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。

    2.  在控制台左侧导航栏中，单击**集群**。

    3.  在集群列表中，找到目标集群，然后查看集群Kubernetes版本。

        ![version](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/6899963061/p176529.png)

2.  确认集群CoreDNS的版本。

    -   您可以在ACK控制台确认CoreDNS的版本。
        1.  登录[容器服务管理控制台](https://cs.console.aliyun.com).

        2.  在控制台左侧导航栏，单击**集群**，然后单击目标集群右侧**操作**列下的**应用管理**。
        3.  在**无状态**页签中，选择**kube-system**命名空间，然后查看CoreDNS的版本。

            ![dns](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/8393963061/p176315.png)

    -   您还可以通过以下kubectl命令查找CoreDNS的版本。

        ```
        kubectl get deployment coredns -n kube-system -o jsonpath="{.spec.template.spec.containers[0].image}"
        ```

        输出：

        ```
        registry-vpc.cn-hangzhou.aliyuncs.com/acs/coredns:1.3.1
        ```

3.  变更CoreDNS配置项。

    1.6.2版本CoreDNS废弃了Proxy插件，使用forward来替代。您需要先将kube-system命名空间下的配置项**coredns**内的proxy字段替换为forward字段。

    -   您可以在ACK控制台上直接更新配置项**coredns**。
        1.  登录[容器服务管理控制台](https://cs.console.aliyun.com).

        2.  在控制台左侧导航栏，单击**集群**，然后单击目标集群名称。
        3.  在集群管理左侧导航栏，选择**配置管理** \> **配置项**。
        4.  在**配置项**页面，找到**coredns**并单击其右侧的**YAML 编辑**。
        5.  在**编辑YAML**页面，将**proxy**修改为**forward**。

            ![forward](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/3622073061/p176548.png)

    -   您还可以通过kubectl命令变更配置项CoreDNS。

        ```
        # 打开编辑。
        kubectl edit configmap/coredns -n kube-system
        
        # 替换proxy字段为forward。
        # 保存并退出。
        ```

    更新完配置后，查看CoreDNS Pod的标准输出是否正常重新加载了配置（一般需要30s时间来热加载新配置）。

    1.  执行以下命令查看集群内的CoreDNS Pod，确定其是否处于运行状态。

```
kubectl get pods -n kube-system | grep coredns
```

输出：

```
coredns-78d4b8bd88-6g62w                           1/1     Running   0          9d
coredns-78d4b8bd88-n6wjm                           1/1     Running   0          9d
```

    2.  执行以下命令查看CoreDNS Pod的日志。

```
kubectl logs coredns-78d4b8bd88-n6wjm -n kube-system
```

输出：

```
.:53
[INFO] plugin/reload: Running configuration MD5 = 71c5f1ff539d304c630521f315dc2ac2
CoreDNS-1.6.7
linux/amd64, go1.13.6, da7f65b
[INFO] 127.0.0.1:48329 - 42313 "HINFO IN 1108347002237365533.4506541768939609094. udp 57 false 512" NXDOMAIN qr,rd,ra 132 0.008874794s
```

        当出现plugin/reload信息时，说明重新加载了CoreDNS新配置。

4.  变更CoreDNS应用。

    变更CoreDNS应用内镜像版本到1.6.2。

    -   通过控制台操作。

1.  登录[容器服务管理控制台](https://cs.console.aliyun.com).

2.  在控制台左侧导航栏，单击**集群**，然后单击目标集群右侧**操作**列下的**应用管理**。
3.  在**无状态**页签，找到**coredns**，然后在其右侧选择**更多** \> **查看Yaml**。
4.  在**编辑YAML**页面，更新image中的版本为**1.6.2**。

    ![image](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/3622073061/p176558.png)

    -   通过kubectl命令操作。

        ```
        # 打开编辑。
        kubectl edit deployment/coredns -n kube-system
        
        # 变更镜像版本到1.6.2。
        # 保存并退出。
        ```

5.  验证结果。

    执行以下命令查看集群内容所有CoreDNS Pod是否都处于Running的正常状态。

    ```
    kubectl get pods -n kube-system | grep coredns
    ```

    输出：

    ```
    coredns-78d4b8bd88-6g62w                           1/1     Running   0          9d
    coredns-78d4b8bd88-n6wjm                           1/1     Running   0          9d
    ```


