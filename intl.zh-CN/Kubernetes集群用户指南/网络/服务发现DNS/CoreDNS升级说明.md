---
keyword: [CoreDNS升级, DNS解析请求失败, 超时]
---

# CoreDNS升级说明

在CoreDNS组件的升级过程中，ACK会进行CoreDNS配置文件即Corefile的配置迁移，同时进行CoreDNS容器组的重建。整个升级过程中，您可能会遇到部分DNS解析请求失败或者超时的情况。本文介绍CoreDNS升级前的检查和优化操作，以降低DNS解析请求失败或者超时的可能性。

-   已创建一个ACK集群。具体操作，请参见[创建Kubernetes托管版集群](/intl.zh-CN/Kubernetes集群用户指南/集群/创建集群/创建Kubernetes托管版集群.md)或[创建Kubernetes专有版集群](/intl.zh-CN/Kubernetes集群用户指南/集群/创建集群/创建Kubernetes专有版集群.md)。
-   已通过kubectl工具连接集群。具体操作，请参见[通过kubectl工具连接集群](/intl.zh-CN/Kubernetes集群用户指南/集群/连接集群/通过kubectl工具连接集群.md)。

## 升级CoreDNS过程说明

CoreDNS升级过程中，ACK会用新版本的YAML模板覆盖您集群中旧版本的CoreDNS YAML模板，只有以下类型变更会被保留：

-   CoreDNS的副本数。
-   CoreDNS配置项，即ConfigMap **coredns**。

从1.7.0版本起，CoreDNS的默认内存限制会被调整至2 GB，正常情况下不会出现Pod内存溢出OOM（Out of Memory）情况，无需对内存限制再次修改。

若您此前对YAML模板进行手动自定义变更，修改了容忍Toleration、内存和CPU的资源请求和限制等字段，将会被覆盖。针对这种情况，您可以手动升级CoreDNS，或者在自动升级CoreDNS后再次更新YAML模板内容。具体操作，请参见[【组件升级】CoreDNS升级公告](/intl.zh-CN/产品公告/【组件升级】CoreDNS升级公告.md)。

升级CoreDNS组件前，进行以下操作可降低DNS解析请求失败或者超时的可能性：

-   [配置IPVS类型集群的UDP超时时间](#section_m9z_3lo_iv4)
-   [配置Kube-DNS服务](#section_66f_5fj_cl0)
-   [配置CoreDNS优雅退出](#section_btw_3tp_tte)

## 配置IPVS类型集群的UDP超时时间

如果您的ACK集群使用了kube-proxy IPVS模式，需要降低IPVS UDP类型的会话保持超时时间至10秒。如果ACK集群中有UDP类型的业务，请确认业务影响面后再进行操作。

**说明：** 如果您的集群不是IPVS类型，请忽略配置IPVS类型集群的UDP超时时间的操作。关于如何查看kube-proxy代理模式，请参见[查看基本信息](/intl.zh-CN/Kubernetes集群用户指南/集群/管理集群/查看集群信息.md)。

**K8s 1.18及以上版本集群**

**控制台操作方式**

1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。

2.  在控制台左侧导航栏中，单击**集群**。

3.  在集群列表页面中，单击目标集群名称或者目标集群右侧**操作**列下的**详情**。

4.  在集群管理页左侧导航栏中，选择**配置管理** \> **配置项**。

5.  在顶部选择**kube-system**命名空间，然后单击配置项kube-proxy-worker右侧的**YAML编辑**。

6.  在**查看YAML**面板中的ipvs字段下，添加`udpTimeout: 10s`，然后单击**确定**。

    ```
    apiVersion: v1
    data:
      config.conf: |
        apiVersion: kubeproxy.config.k8s.io/v1alpha1
        kind: KubeProxyConfiguration
        // 其它不相关字段已省略。
        mode: ipvs
        // 如果ipvs键不存在，需要添加此键。
        ipvs:
          udpTimeout: 10s
    ```

7.  重建所有名为kube-proxy-worker的容器。

    1.  在集群管理页左侧导航栏中，选择**工作负载** \> **守护进程集**。

    2.  在守护进程集列表中，找到并单击**kube-proxy-worker**。

    3.  在**kube-proxy-worker**页面中的**容器组**页签下对应容器组右侧，选择**更多** \> **删除**，然后单击**确定**。

        重复操作删除所有容器组。删除容器组后，系统会自动重建所有容器。

8.  验证UDP超时时间的配置是否成功。

    1.  执行以下命令安装ipvsadm。

        ipvsadm是IPVS模块的管理工具。更多信息，请参见[ipvsadm](https://software.opensuse.org/package/ipvsadm)。

        ```
        yum install -y ipvsadm
        ```

    2.  在集群任意一台ECS节点中执行以下命令查看第三个数字。

        ```
        ipvsadm -L --timeout
        ```

        如果输出结果中第三个数字是10，则说明IPVS类型集群的UDP超时时间变更成功。

        **说明：** 变更成功后，请观察至少五分钟后再进行下一步操作。


**命令行操作方式**

1.  执行以下命令修改kube-proxy的配置文件kube-proxy-worker。

    ```
    kubectl -n kube-system edit configmap kube-proxy-worker
    ```

2.  在kube-proxy配置文件中的ipvs字段下，添加`udpTimeout: 10s`并保存退出。

    ```
    apiVersion: v1
    data:
      config.conf: |
        apiVersion: kubeproxy.config.k8s.io/v1alpha1
        kind: KubeProxyConfiguration
        // 其它不相关字段已省略。
        mode: ipvs
        // 如果ipvs键不存在，需要添加此键。
        ipvs:
          udpTimeout: 10s
    ```

3.  执行以下命令重建所有名为kube-proxy-worker的容器。

    1.  执行以下命令查看存在的容器组信息。

        ```
        kubectl -n kube-system get pod -o wide | grep kube-proxy-worker
        ```

    2.  执行以下命令删除上步骤中查看所有容器，系统将会自动重建名为kube-proxy-worker容器。

        ```
        kubectl -n kube-system delete pod <kube-proxy-worker-****>
        ```

        **说明：** 将<kube-proxy-worker-\*\*\*\*\>替换为上述打印的所有容器组名称。

4.  验证UDP超时时间的配置是否成功。

    1.  执行以下命令安装ipvsadm。

        ipvsadm是IPVS模块的管理工具。更多信息，请参见[ipvsadm](https://software.opensuse.org/package/ipvsadm)。

        ```
        yum install -y ipvsadm
        ```

    2.  在集群任意一台ECS节点中执行以下命令查看第三个数字。

        ```
        ipvsadm -L --timeout
        ```

        如果输出结果中第三个数字是10，则说明IPVS类型集群的UDP超时时间变更成功。

        **说明：** 变更成功后，请观察至少五分钟后再进行下一步操作。


**K8s 1.16及以下版本集群**

此类版本集群的kube-proxy不支持udpTimeout参数，推荐使用OOS服务批量在所有集群机器上执行`ipvsadm`命令以调整UDP超时时间配置。命令如下：

```
yum install -y ipvsadm
ipvsadm -L --timeout > /tmp/ipvsadm_timeout_old
ipvsadm --set 900 120 10
ipvsadm -L --timeout > /tmp/ipvsadm_timeout_new
diff /tmp/ipvsadm_timeout_old /tmp/ipvsadm_timeout_new
```

关于OOS的批量操作实例介绍，请参见[批量操作实例]()。

## 配置Kube-DNS服务

**控制台操作方式**

1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。

2.  在控制台左侧导航栏中，单击**集群**。

3.  在集群列表页面中，单击目标集群名称或者目标集群右侧**操作**列下的**详情**。

4.  在集群管理页左侧导航栏中，选择**网络** \> **服务**。

5.  在kube-system命名空间下，单击服务**kube-dns**右侧的**查看YAML**。

6.  -   如果发现sessionAffinity字段为`None`，则无需进行以下步骤。
-   如果发现sessionAffinity为`ClientIP`，则进行以下步骤。
7.  删除sessionAffinity、sessionAffinityConfig及所有子键，然后单击**更新**。

    ```
    // 删除以下所有内容。
    sessionAffinity: ClientIP
      sessionAffinityConfig:
        clientIP:
          timeoutSeconds: 10800
    ```

8.  再次单击服务**kube-dns**右侧的**查看YAML**，校验sessionAffinity字段是否为`None`，为`None`则Kube-DNS服务变更成功。


**命令行操作方式**

1.  执行以下命令查看kube-dns服务配置信息。

    ```
    kubectl -n kube-system get svc kube-dns -o yaml
    ```

2.  -   如果发现sessionAffinity字段为`None`，则无需进行以下步骤。
-   如果发现sessionAffinity为`ClientIP`，则进行以下步骤。
3.  执行以下命令打开并编辑名为kube-dns的服务。

    ```
    kubectl -n kube-system edit service kube-dns
    ```

4.  删除sessionAffinity相关设置（sessionAffinity、sessionAffinityConfig及所有子键），并保存退出。

    ```
    // 删除以下所有内容。
    sessionAffinity: ClientIP
      sessionAffinityConfig:
        clientIP:
          timeoutSeconds: 10800
    ```

5.  修改完成后，再次运行以下命令查看sessionAffinity字段是否为`None`，为`None`则Kube-DNS服务变更成功。

    ```
    kubectl -n kube-system get svc kube-dns -o yaml
    ```


## 配置CoreDNS优雅退出

**说明：** CoreDNS刷新配置过程中，可能会占用额外内存。修改CoreDNS配置项后，请观察Pod运行状态，如果出现Pod内存不足的情况，请及时修改CoreDNS Deployment中容器内存限制，建议将内存调整至2 GB。

**控制台操作方式**

1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。

2.  在控制台左侧导航栏中，单击**集群**。

3.  在集群列表页面中，单击目标集群名称或者目标集群右侧**操作**列下的**详情**。

4.  在集群管理页左侧导航栏中，选择**配置管理** \> **配置项**。

5.  在kube-system命名空间下，单击配置项coredns右侧的**YAML编辑**。

6.  参考下列的YAML文件，保证health插件开启，并调整lameduck参数为`15s`，然后单击**确定**。

    ```
    .:53 {
            errors
            
            // health插件在不同的CoreDNS版本中可能有不同的设置情形。
            // 情形一：默认未启用health插件。
            
            // 情形二：默认启用health插件，但未设置lameduck时间。
            health
            
            // 情形三：默认启用health插件，设置了lameduck时间为5秒。   
            health {
                lameduck 5s
            }
            
            // 对于以上三种情形，应统一修改成以下，调整lameduck参数为15秒。
            health {
                lameduck 15s
            }
            
            // 其它插件不需要修改，此处省略。
        }
    ```

    如果CoreDNS Pod正常运行则说明CoreDNS优雅退出的配置更新成功。如果CoreDNS Pod出现异常，可以通过查看Pod事件及日志定位原因。


**命令行操作方式**

1.  执行以下命令打开CoreDNS配置文件。

    ```
    kubectl -n kube-system edit configmap coredns
    ```

2.  参考下列的YAML文件，保证health插件开启，并调整lameduck参数为`15s`。

    ```
    .:53 {
            errors
            
            // health插件在不同的CoreDNS版本中可能有不同的设置情形。
            // 情形一：默认未启用health插件。
            
            // 情形二：默认启用health插件，但未设置lameduck时间。
            health
            
            // 情形三：默认启用health插件，设置了lameduck时间为5秒。   
            health {
                lameduck 5s
            }
            
            // 对于以上三种情形，应统一修改成以下，调整lameduck参数为15秒。
            health {
                lameduck 15s
            }
            
            // 其它插件不需要修改，此处省略。
        }
    ```

3.  修改CoreDNS配置文件后保存退出。

    如果CoreDNS正常运行则说明CoreDNS优雅退出的配置更新成功。如果CoreDNS Pod出现异常，可以通过查看Pod事件及日志定位原因。


**相关文档**  


[In IPVS mode, kube-proxy doesn't handle graceful termination of UDP flows](https://github.com/kubernetes/kubernetes/issues/71514)

[CoreDNS](/intl.zh-CN/产品发布记录/组件介绍与变更记录/网络/CoreDNS.md)

