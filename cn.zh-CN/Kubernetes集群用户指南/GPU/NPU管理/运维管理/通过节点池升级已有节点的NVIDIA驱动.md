---
keyword: 升级节点NVIDIA驱动
---

# 通过节点池升级已有节点的NVIDIA驱动

如果您使用的CUDA库需要匹配更高版本的NVIDIA驱动，需要升级节点的NVIDIA驱动。本文升级方案是将节点从集群中移除，然后再将该节点添加到集群中，在添加的过程中节点会重装系统、安装指定版本的NVIDIA驱动。本文介绍在使用现有节点的场景下，如何通过节点池升级节点NVIDIA驱动。

ACK目前不支持在不移除节点的情况下仅升级节点NVIDIA驱动的场景，且您原有节点池也许还存在其它节点的场景，故不做升级原节点池的操作。

## 基于节点池升级节点NVIDIA驱动的优势

实现了对集群中不同节点上的NVIDIA驱动的批量管理功能。以下是两个使用实例：

-   将集群中的一部分节点的驱动升级到418.181.07版本，而另一部分的节点升级到450.102.0版本。此时，可以将一部分节点添加到节点池A，另一部分节点添加到节点池B。
-   将集群中需要升级到418.181.07版本的节点用节点池A管理，如果需要将任务调度到NVIDIA驱动版本为418.181.07的节点上，只需要将任务的**selector**指定为节点池A的标签即可。

**说明：**

-   升级NVIDIA驱动的方式本质上是将节点从集群中移除，然后再将该节点添加到集群中，在添加的过程中会重装系统、安装指定NVIDIA驱动等，所以在执行升级操作前，确认该节点没有正在运行的任务以及重要的数据。
-   为了降低风险，强烈建议您先升级一个节点，如果整个升级过程没有问题，再进行批量升级操作。

## 步骤一：确定NVIDIA驱动版本

您所使用的CUDA库与哪些NVIDIA驱动兼容是NVIDIA驱动版本选定的最重要依据。以下是CUDA库与NVIDIA驱动的兼容关系。

**说明：** ACK集群默认安装的NVIDIA驱动版本为418.87.01。

![1](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/9032725161/p245597.png)

更多信息，请参见[cuda-compatibility](https://docs.nvidia.com/deploy/cuda-compatibility/index.html)。

## 步骤二：移除节点

1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。

2.  在控制台左侧导航栏中，单击**集群**。

3.  在集群列表页面中，单击目标集群名称或者目标集群右侧**操作**列下的**详情**。

4.  在集群管理页左侧导航栏中，选择**节点管理** \> **节点**。

5.  选中待升级驱动的节点，单击**批量移除**。

6.  在**移除节点**对话框，选中**自动排空节点（drain）**，然后单击**确定**。


## 步骤三：创建节点池并指定驱动版本

**方式一：从容器服务提供的可用驱动版本列表中选择驱动创建节点池**

**说明：** 选择此种方式安装驱动比较简单，只需要给节点池的节点打上标签`ack.aliyun.com/nvidia-driver-version=<驱动版本>`，然后将前面步骤中集群移除的节点添加到该节点池即可。

本文以418.181.07版本为例介绍整个操作流程：

1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。

2.  在控制台左侧导航栏中，单击**集群**。

3.  在集群列表页面中，单击目标集群名称或者目标集群右侧**操作**列下的**详情**。

4.  在集群管理页左侧导航栏中，选择**节点管理** \> **节点池**。

5.  单击右上角**创建节点池**。

6.  在**创建节点池**对话框中配置相关参数。有关配置项的详细说明，请参见[创建Kubernetes专有版集群](/cn.zh-CN/Kubernetes集群用户指南/集群管理/创建集群/创建Kubernetes专有版集群.md)。部分配置项说明如下：

    |参数|描述|
    |--|--|
    |**虚拟交换机**|虚拟交换机和实例规格参数的取值任意，因为这些信息在扩容节点时才会用到，升级驱动属于添加已有节点，不会用到这些信息。|
    |**实例规格**|
    |**操作系统**|节点重新加入集群后所使用的操作系统。|
    |**数量**|实例数量填写为0，如果实例数量不为0，ACK将会创建新的实例。![ACK2](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/9032725161/p245634.png) |

    1.  单击**显示高级选项**。

    2.  在**节点标签**参数栏添加标签，单击![1](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/9032725161/p245638.png)，在**键**文本框中入`ack.aliyun.com/nvidia-driver-version`，在**值**文本框中入`418.181.07`。

        容器服务提供可用NVIDIA驱动版本如下：

        -   418.181.07
        -   450.102.04
    3.  参数配置完成后，单击**确认配置**。


**方式二：自定义驱动版本创建节点池**

**自定义驱动版本**

以下以上传NVIDIA-Linux-x86\_64-460.32.03.run驱动文件为例介绍怎样自定义驱动版本。驱动文件可以在[NVIDIA官方网站下载](https://www.nvidia.com/Download/index.aspx?lang=en-us)。

**说明：** NVIDIA-Linux-x86\_64-460.32.03.run需要放在OSS Bucket的根目录下。

1.  在对象存储OSS控制台中创建Bucket。具体操作，请参见[创建存储空间](/cn.zh-CN/控制台用户指南/存储空间管理/创建存储空间.md)。

2.  将NVIDIA-Linux-x86\_64-460.32.03.run驱动文件上传至OSS。具体操作，请参见[上传文件](/cn.zh-CN/控制台用户指南/文件管理/上传文件.md)。

3.  文件上传成功后，单击已上传驱动文件右侧**操作**列下的**详情**。

4.  在**详情**面板，打开**使用HTTPS**![gpu-1](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/0132725161/p246955.png)开关，取消使用HTTPS的配置。

    **说明：** ACK创建集群时会通过URL拉取驱动文件，URL中使用的协议是HTTP协议。OSS默认使用的是HTTPS协议，您需要单击**使用HTTPS**按钮，取消使用HTTPS的配置。

5.  确认驱动文件配置信息。记录该文件的URL，且将URL拆成两部分，即endpoint和runfile。以`[http://nvidia-XXX-XXX-cn-beijing.aliyuncs.com/NVIDIA-Linux-x86\_64-460.32.03.run](http://nvidia-XXX-XXX-cn-beijing.aliyuncs.com/NVIDIA-Linux-x86_64-460.32.03.run)`为例，将其分成以下两部分。

    -   endpoint：nvidia-XXX-XXX-cn-beijing.aliyuncs.com
    -   runfile：NVIDIA-Linux-x86\_64-460.32.03.run

**通过自定义驱动版本后创建节点池**

1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。

2.  在控制台左侧导航栏中，单击**集群**。

3.  在集群列表页面中，单击目标集群名称或者目标集群右侧**操作**列下的**详情**。

4.  在集群管理页左侧导航栏中，选择**节点管理** \> **节点池**。

5.  单击右上角**创建节点池**。

6.  在**创建节点池**对话框中配置相关参数。有关配置项的详细说明，请参见[创建Kubernetes专有版集群](/cn.zh-CN/Kubernetes集群用户指南/集群管理/创建集群/创建Kubernetes专有版集群.md)。部分配置项说明如下：

    |参数|描述|
    |--|--|
    |**虚拟交换机**|虚拟交换机和实例规格参数的取值任意，因为这些信息在扩容节点时才会用到，升级驱动属于添加已有节点，不会用到这些信息。|
    |**实例规格**|
    |**操作系统**|节点重新加入集群后所使用的操作系统。|
    |**数量**|实例数量填写为0，如果实例数量不为0，ACK将会创建新的实例。![ACK2](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/9032725161/p245634.png) |

    1.  单击**显示高级选项**。

    2.  在**节点标签**参数栏添加两个标签，单击![1](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/9032725161/p245638.png)：

        -   输入第一个标签的**键**为`ack.aliyun.com/nvidia-driver-oss-endpoint`，**值**为`nvidia-XXX-XXX-cn-beijing.aliyuncs.com`。
        -   输入第二个标签的**键**为`ack.aliyun.com/nvidia-driver-runfile`，**值**为`NVIDIA-Linux-x86_64-460.32.03.run`。
    3.  参数配置完成后，单击**确认配置**。


## 步骤四：添加节点到节点池

节点池创建完成以后，需要将先前移除的节点添加到该节点池。具体操作，请参见[添加已有节点](/cn.zh-CN/Kubernetes集群用户指南/节点与节点池/节点/添加已有节点.md)。

## 步骤五：验证通过节点池升级节点NVIDIA驱动是否成功

1.  在目标集群右侧**操作**列选择**更多** \> **通过CloudShell管理集群**。

2.  执行以下命令，查看带有`component: nvidia-device-plugin`标签的Pod。

    ```
    kubectl get po -n kube-system -l component=nvidia-device-plugin -o wide
    ```

    系统输出类似以下结果：

    ```
    NAME                                            READY   STATUS    RESTARTS   AGE   IP              NODE                       NOMINATED NODE   READINESS GATES
    nvidia-device-plugin-cn-beijing.192.168.1.127   1/1     Running   0          6d    192.168.1.127   cn-beijing.192.168.1.127   <none>           <none>
    nvidia-device-plugin-cn-beijing.192.168.1.128   1/1     Running   0          17m   192.168.1.128   cn-beijing.192.168.1.128   <none>           <none>
    nvidia-device-plugin-cn-beijing.192.168.8.12    1/1     Running   0          9d    192.168.8.12    cn-beijing.192.168.8.12    <none>           <none>
    nvidia-device-plugin-cn-beijing.192.168.8.13    1/1     Running   0          9d    192.168.8.13    cn-beijing.192.168.8.13    <none>           <none>
    nvidia-device-plugin-cn-beijing.192.168.8.14    1/1     Running   0          9d    192.168.8.14    cn-beijing.192.168.8.14    <none>           <none>
    ```

    通过NODE列找到集群中刚添加节点所对应的Pod名称为nvidia-device-plugin-cn-beijing.192.168.1.128。

3.  执行以下命令查看节点的驱动版本是否符合预期。

    ```
    kubectl exec -ti nvidia-device-plugin-cn-beijing.192.168.1.128 -n kube-system -- nvidia-smi 
    ```

    系统输出类似以下结果：

    ```
    Sun Feb  7 04:09:01 2021       
    +-----------------------------------------------------------------------------+
    | NVIDIA-SMI 418.181.07   Driver Version: 418.181.07   CUDA Version: N/A      |
    |-------------------------------+----------------------+----------------------+
    | GPU  Name        Persistence-M| Bus-Id        Disp.A | Volatile Uncorr. ECC |
    | Fan  Temp  Perf  Pwr:Usage/Cap|         Memory-Usage | GPU-Util  Compute M. |
    |===============================+======================+======================|
    |   0  Tesla V100-SXM2...  On   | 00000000:00:07.0 Off |                    0 |
    | N/A   27C    P0    40W / 300W |      0MiB / 16130MiB |      0%      Default |
    +-------------------------------+----------------------+----------------------+
    |   1  Tesla V100-SXM2...  On   | 00000000:00:08.0 Off |                    0 |
    | N/A   27C    P0    40W / 300W |      0MiB / 16130MiB |      0%      Default |
    +-------------------------------+----------------------+----------------------+
    |   2  Tesla V100-SXM2...  On   | 00000000:00:09.0 Off |                    0 |
    | N/A   31C    P0    39W / 300W |      0MiB / 16130MiB |      0%      Default |
    +-------------------------------+----------------------+----------------------+
    |   3  Tesla V100-SXM2...  On   | 00000000:00:0A.0 Off |                    0 |
    | N/A   27C    P0    41W / 300W |      0MiB / 16130MiB |      0%      Default |
    +-------------------------------+----------------------+----------------------+
                                                                                   
    +-----------------------------------------------------------------------------+
    | Processes:                                                       GPU Memory |
    |  GPU       PID   Type   Process name                             Usage      |
    |=============================================================================|
    |  No running processes found                                                 |
    +-----------------------------------------------------------------------------+
    ```

    从以上输出的信息，可以知道驱动版本为418.181.07，通过节点池升级节点NVIDIA驱动成功。


**相关文档**  


[通过节点池创建新NVIDIA驱动版本的节点]()

