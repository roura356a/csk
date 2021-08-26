---
keyword: [CoreDNS版本过低, 升级CoreDNS, 手动]
---

# 【组件升级】CoreDNS升级公告

为了提升ACK集群内域名解析服务的稳定性，建议您升级集群的CoreDNS至最新版本。本文介绍如何升级CoreDNS的版本。

低于1.7.0版本的CoreDNS存在的以下问题，可能会影响ACK集群内域名解析服务的稳定性：

-   因为CoreDNS需要通过watch apiserver拿到Service数据，所以health插件会检查CoreDNS与apiserver的连通性。如果连通性不正常（如网络抖动、apiserver重启等情况），健康检查接口会报错。CoreDNS在3次健康检查失败后会重启，重启过程中将导致服务不可以用。更多信息，请参见[coredns](https://github.com/coredns/coredns/issues/2629)。
-   CoreDNS会受到klog缺陷的影响：CoreDNS与apiserver断连后，klog尝试向一个不存在的目录/tmp写错误日志，此行为会失败并进一步导致CoreDNS不可用。更多信息，请参见[kubernetes: add mount of /tmp \#137](https://github.com/coredns/deployment/issues/137)。
-   默认使用的Memory Limit在大规模集群下可能产生的OOM情况。

## 自动升级CoreDNS

您可以通过控制台的组件管理入口进行CoreDNS的版本升级。关于升级CoreDNS前的注意事项，请参见[CoreDNS升级说明](/cn.zh-CN/Kubernetes集群用户指南/网络/服务发现DNS/CoreDNS升级说明.md)。关于如何自动升级组件的步骤，请参见[管理组件](/cn.zh-CN/Kubernetes集群用户指南/组件/管理组件.md)。

1.14.8及以上版本的ACK集群可以升级CoreDNS到最新版本，早于1.14.8版本的ACK集群最高可以升级到1.6.2 CoreDNS版本。

如果在控制台的组件管理页面的CoreDNS组件上无法看到升级按钮，且当前组件版本较低，说明您的集群无法进行CoreDNS的自动升级。针对无法自动升级CoreDNS的情况，可手动升级CoreDNS。具体步骤，请参见[手动升级CoreDNS](#section_7wd_grs_c5s)。

如果您之前有手动升级CoreDNS，那您在自动升级CoreDNS之前，需要检查CoreDNS配置文件是否已经开启ready插件。如果配置文件中没有ready插件，您需要开启ready插件，然后再进行自动升级CoreDNS操作。关于开启ready插件的具体操作，请参见[开启ready插件](#section_ksj_o3p_1n8)。

## 手动升级CoreDNS

1.  确认集群版本与CoreDNS版本的兼容性。

    您需先确认集群版本号，然后进一步确认与CoreDNS 1.6.2版本的兼容性。ACK Kubernetes集群版本与CoreDNS 1.6.2版本兼容情况如下表（Kubernetes 1.11/1.12/1.14/1.16均兼容CoreDNS 1.6.2版本）。

    |版本|兼容信息|
    |--|----|
    |Kubernetes版本|1.11|1.12|1.14|1.16|
    |CoreDNS|1.6.2|1.6.2|1.6.2|1.6.2|

    确认集群版本号的操作步骤如下：

    1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。

    2.  在控制台左侧导航栏中，单击**集群**。

    3.  在集群列表中，找到目标集群，然后查看集群Kubernetes版本。

        ![version](https://help-static-aliyun-doc.aliyuncs.com/assets/img/zh-CN/6899963061/p176529.png)

2.  确认集群CoreDNS的版本。

    -   您可以在ACK控制台确认CoreDNS的版本。
        1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。

        2.  在控制台左侧导航栏中，单击**集群**。
        3.  在集群列表页面中，单击目标集群名称或者目标集群右侧**操作**列下的**详情**。
        4.  在集群管理页面左侧导航栏选择**工作负载** \> **无状态**。
        5.  在**无状态**页面顶部设置**命名空间**为kube-system，然后查看CoreDNS的版本。

            ![dns](https://help-static-aliyun-doc.aliyuncs.com/assets/img/zh-CN/0800616261/p176315.png)

    -   您还可以通过以下kubectl命令查找CoreDNS的版本。

        ```
        kubectl get deployment coredns -n kube-system -o jsonpath="{.spec.template.spec.containers[0].image}"
        ```

        预期输出：

        ```
        registry-vpc.cn-hangzhou.aliyuncs.com/acs/coredns:1.3.1
        ```

3.  变更CoreDNS配置项。

    1.6.2版本CoreDNS废弃了Proxy插件，使用forward来替代。您需要先将kube-system命名空间下的配置项**coredns**内的proxy字段替换为forward字段。您还需要添加`ready`字段，使CoreDNS包含ready插件。

    -   您可以在ACK控制台上更新配置项**coredns**。
        1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。

        2.  在控制台左侧导航栏中，单击**集群**。
        3.  在集群列表页面中，单击目标集群名称或者目标集群右侧**操作**列下的**详情**。
        4.  在集群管理页面左侧导航栏选择**配置管理** \> **配置项**。
        5.  在**配置项**页面顶部设置**命名空间**为kube-system，然后单击coredns右侧**操作**列下的**YAML编辑**。
        6.  在**查看YAML**面板，将**proxy**修改为**forward**。

            ![forward](https://help-static-aliyun-doc.aliyuncs.com/assets/img/zh-CN/3622073061/p176548.png)

        7.  添加`ready`字段，然后单击**确定**。

            ```
            ready # 如果没有这一行，请增加本行，注意缩进与kubernetes一致。
            kubernetes cluster.local in-addr.arpa ip6.arpa {
               pods verified
               fallthrough in-addr.arpa ip6.arpa
            }
            ```

    -   您还可以通过kubectl命令变更配置项CoreDNS。

        ```
        # 打开编辑。
        kubectl edit configmap/coredns -n kube-system
        
        # 替换proxy字段为forward。
        # 添加ready字段，注意缩进与kubernetes一致。
        # 保存并退出。
        ```

4.  查看CoreDNS Pod的标准输出是否正常重新加载了配置（热加载新配置一般需要30s）。

    1.  执行以下命令查看集群内的CoreDNS Pod，确定其是否处于运行状态。

        ```
        kubectl get pods -n kube-system | grep coredns
        ```

        预期输出：

        ```
        coredns-78d4b8bd88-6g62w                           1/1     Running   0          9d
        coredns-78d4b8bd88-n6wjm                           1/1     Running   0          9d
        ```

    2.  执行以下命令查看CoreDNS Pod的日志。

        ```
        kubectl logs coredns-78d4b8bd88-n6wjm -n kube-system
        ```

        预期输出：

        ```
        .:53
        [INFO] plugin/reload: Running configuration MD5 = 71c5f1ff539d304c630521f315dc2ac2
        CoreDNS-1.6.7
        linux/amd64, go1.13.6, da7f65b
        [INFO] 127.0.0.1:48329 - 42313 "HINFO IN 1108347002237365533.4506541768939609094. udp 57 false 512" NXDOMAIN qr,rd,ra 132 0.008874794s
        ```

        预期输出中包含`plugin/reload`信息，说明加载了CoreDNS配置。

5.  变更CoreDNS应用内镜像版本到1.6.2。

    -   通过控制台操作。
        1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。

        2.  在控制台左侧导航栏中，单击**集群**。
        3.  在集群列表页面中，单击目标集群名称或者目标集群右侧**操作**列下的**详情**。
        4.  在集群管理页面左侧导航栏选择**工作负载** \> **无状态**。
        5.  在**无状态**页面顶部设置**命名空间**为kube-system，找到**coredns**，然后在其右侧选择**更多** \> **查看Yaml**。
        6.  在**编辑YAML**页面，更新image中的版本为**1.6.2**。然后单击**更新**。

            ![image](https://help-static-aliyun-doc.aliyuncs.com/assets/img/zh-CN/3622073061/p176558.png)

    -   通过kubectl命令操作。

        ```
        # 打开编辑。
        kubectl edit deployment/coredns -n kube-system
        
        # 变更镜像版本到1.6.2。
        # 保存并退出。
        ```

6.  验证结果。

    执行以下命令查看集群内容所有CoreDNS Pod是否都处于Running的正常状态。

    ```
    kubectl get pods -n kube-system | grep coredns
    ```

    预期输出：

    ```
    coredns-78d4b8bd88-6g62w                           1/1     Running   0          9d
    coredns-78d4b8bd88-n6wjm                           1/1     Running   0          9d
    ```


## 开启ready插件

若您此前按本文档进行过手动升级操作且当前CoreDNS版本大于1.5.0，请确认CoreDNS配置文件中已经打开了ready插件，如配置文件中没有ready插件，您需要开启ready插件，否则CoreDNS将无法正常启动。

1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。

2.  在控制台左侧导航栏中，单击**集群**。

3.  在集群列表页面中，单击目标集群名称或者目标集群右侧**操作**列下的**详情**。

4.  在集群管理页左侧导航栏中，选择**配置管理** \> **配置项**。

5.  在**配置项**页面顶部设置**命名空间**为kube-system，然后单击coredns右侧**操作**列下的**YAML编辑**。

6.  在**查看YAML**面板检查是否有`ready`字段，如果没有，请添加`ready`字段，然后单击**确定**。

    ```
    apiVersion: v1
    data:
      Corefile: |
        .:53 {
            errors
            health {
                lameduck 15s
            }
            ready # 如果没有这一行，请增加本行，注意缩进与 kubernetes 一致。
            kubernetes cluster.local in-addr.arpa ip6.arpa {
                pods verified
                fallthrough in-addr.arpa ip6.arpa
            }
            prometheus :9153
            forward . /etc/resolv.conf {
                max_concurrent 1000
            }
            cache 30
            loop
            log
            reload
            loadbalance
        }
    ```

7.  执行以下命令，检查CoreDNS Pod的标准输出是否加载了CoreDNS配置。热加载新配置一般需要30s。

    ```
    kubectl logs coredns-78d4b8bd88-n6wjm -n kube-system
    ```

    预期输出中包含`plugin/reload`信息，说明加载了CoreDNS配置。


**相关文档**  


[CoreDNS](/cn.zh-CN/产品发布记录/组件介绍与变更记录/网络/CoreDNS.md)

