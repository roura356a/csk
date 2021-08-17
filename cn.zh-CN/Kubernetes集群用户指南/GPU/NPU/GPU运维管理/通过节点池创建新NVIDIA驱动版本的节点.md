---
keyword: 创建新NVIDIA驱动版本的节点
---

# 通过节点池创建新NVIDIA驱动版本的节点

ACK集群默认安装的NVIDIA驱动版本为418.87.01，如果您使用的CUDA库需要匹配更高版本的NVIDIA驱动，需要自定义安装节点的NVIDIA驱动。本文介绍在不使用现有节点的场景下，如何通过节点池创建新NVIDIA驱动版本的节点。

## 基于节点池安装节点NVIDIA驱动的优势

可以实现对集群中不同节点上的NVIDIA驱动的批量管理功能。以下是两个使用示例：

-   将集群中需要安装418.181.07版本的节点用节点池A管理，如果需要将任务调度到NVIDIA驱动版本为418.181.07的节点上，只需要将任务的**selector**指定为节点池A的标签即可。
-   将集群中的一部分节点安装418.181.07的驱动，而另一部分的节点安装版本为450.102.0的驱动。此时，可以将一部分节点添加到节点池A，另一部分节点添加到节点池B。

## 步骤一：确定NVIDIA驱动版本

您所使用的CUDA库与哪些NVIDIA驱动兼容是NVIDIA驱动版本选定的最重要依据。以下是CUDA库与NVIDIA驱动的兼容关系。

![1](https://help-static-aliyun-doc.aliyuncs.com/assets/img/zh-CN/4483785161/p245597.png)

## 步骤二：创建节点池并指定驱动版本

**方式一：从容器服务提供的可用驱动版本列表中选择驱动创建节点池**

**说明：** 选择此种方式安装驱动比较简单，只需要给节点池的节点打上标签`ack.aliyun.com/nvidia-driver-version=<驱动版本>`，然后将前面步骤中集群移除的节点添加到该节点池即可。

本文以418.181.07版本为例介绍整个操作流程：

1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。

2.  在控制台左侧导航栏中，单击**集群**。

3.  在集群列表页面中，单击目标集群名称或者目标集群右侧**操作**列下的**详情**。

4.  在集群管理页左侧导航栏中，选择**节点管理** \> **节点池**。

5.  单击右上角**创建节点池**。

6.  在**创建节点池**对话框中配置相关参数。有关配置项的详细说明，请参见[创建Kubernetes专有版集群](/cn.zh-CN/Kubernetes集群用户指南/集群/创建集群/创建Kubernetes专有版集群.md)。部分配置项说明如下：

    1.  单击**显示高级选项**。

    2.  在**节点标签**参数栏添加标签，单击![1](https://help-static-aliyun-doc.aliyuncs.com/assets/img/zh-CN/9032725161/p245638.png)图标，在**键**文本框中输入`ack.aliyun.com/nvidia-driver-version`，在**值**文本框中输入`418.181.07`。

        容器服务提供可用NVIDIA驱动版本如下：

        -   418.181.07
        -   450.102.04
        -   460.32.03
    3.  参数配置完成后，单击**确认配置**。


**方式二：自定义驱动版本创建节点池**

**自定义驱动版本**

以下以上传NVIDIA-Linux-x86\_64-460.32.03.run驱动文件为例介绍如何自定义驱动版本。驱动文件可以在[NVIDIA官方网站下载](https://www.nvidia.com/Download/index.aspx?lang=en-us)。

**说明：** NVIDIA-Linux-x86\_64-460.32.03.run需要放在OSS Bucket的根目录下。

1.  在对象存储[OSS管理控制台](https://oss.console.aliyun.com/)中创建Bucket。具体操作，请参见[创建存储空间](/cn.zh-CN/控制台用户指南/存储空间管理/创建存储空间.md)。

2.  将NVIDIA-Linux-x86\_64-460.32.03.run驱动文件上传至OSS。具体操作，请参见[上传文件](/cn.zh-CN/控制台用户指南/文件管理/上传文件.md)。

3.  文件上传成功后，在对象存储管理页左侧导航栏中，单击**文件管理**。

4.  在**文件管理**页面，单击已上传驱动文件右侧**操作**列下的**详情**。

5.  在**详情**面板，关闭**使用HTTPS**开关，取消使用HTTPS的配置。

    **说明：** ACK创建集群时会通过URL拉取驱动文件，URL中使用的协议是HTTP协议。OSS默认使用的是HTTPS协议，您需要关闭**使用HTTPS**开关，取消使用HTTPS的配置。

6.  确认驱动文件配置信息。记录该文件的URL，且将URL拆成两部分，即endpoint和runfile。以`[http://nvidia-XXX-XXX-cn-beijing.aliyuncs.com/NVIDIA-Linux-x86\_64-460.32.03.run](http://nvidia-XXX-XXX-cn-beijing.aliyuncs.com/NVIDIA-Linux-x86_64-460.32.03.run)`为例，将其分成以下两部分：

    -   endpoint：nvidia-XXX-XXX-cn-beijing.aliyuncs.com
    -   runfile：NVIDIA-Linux-x86\_64-460.32.03.run

**通过自定义驱动版本后创建节点池**

1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。

2.  在控制台左侧导航栏中，单击**集群**。

3.  在集群列表页面中，单击目标集群名称或者目标集群右侧**操作**列下的**详情**。

4.  在集群管理页左侧导航栏中，选择**节点管理** \> **节点池**。

5.  单击右上角**创建节点池**。

6.  在**创建节点池**对话框中配置相关参数。有关配置项的详细说明，请参见[创建Kubernetes专有版集群](/cn.zh-CN/Kubernetes集群用户指南/集群/创建集群/创建Kubernetes专有版集群.md)。部分配置项说明如下：

    1.  单击**显示高级选项**。

    2.  在**节点标签**参数栏添加两个标签，单击![1](https://help-static-aliyun-doc.aliyuncs.com/assets/img/zh-CN/9032725161/p245638.png)图标：

        -   输入第一个标签的**键**为`ack.aliyun.com/nvidia-driver-oss-endpoint`，**值**为`nvidia-XXX-XXX-cn-beijing.aliyuncs.com`。
        -   输入第二个标签的**键**为`ack.aliyun.com/nvidia-driver-runfile`，**值**为`NVIDIA-Linux-x86_64-460.32.03.run`。
    3.  参数配置完成后，单击**确认配置**。


**方式三：在OpenAPI中使用指定版本创建节点池**

通过在OpenAPI中调用CreateClusterNodePool，配置相关请求参数，可为集群创建指定NVIDIA驱动版本的节点池。具体操作，请参见[创建节点池](/cn.zh-CN/API参考/节点池/创建节点池.md)。

方式一：在OpenAPI中使用指定驱动版本创建节点池

如果使用OpenAPI方式创建或者扩容集群，那么只需要在上传的`body`中的`tags`域添加特定标签即可，代码示例如下所示。

```
{
  // 其他部分省略
  ......
    "tags": [
        {
            "key": "ack.aliyun.com/nvidia-driver-version",
            "value": "410.104"
        }
    ],
  // 其他部分省略
  ......
}
```

方式二：在OpenAPI中使用指定驱动版本的文件下载地址创建节点池

如果需要指定NVIDIA驱动下载地址，那么需要指定OSS产品`endpoint`和`runfile`名称，代码示例如下所示。关于如何获取OSS产品`endpoint`和`runfile`名称，请参见[确认驱动文件配置信息](#step_4ru_5y3_a84)。

```
{
  // 其他部分省略
  ......
    "tags": [
      {
              "key": "ack.aliyun.com/nvidia-driver-oss-endpoint",
              "value": "nvidia-XXX-XXX-cn-beijing.aliyuncs.com"
      },
      {
        "key": "ack.aliyun.com/nvidia-driver-runfile",
        "value": "NVIDIA-Linux-x86_64-410.104.run"
      }
    ],
  // 其他部分省略
  ......
}
```

## 步骤三：验证通过节点池自定义安装NVIDIA驱动是否成功

1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。

2.  在控制台左侧导航栏中，单击**集群**。

3.  在目标集群右侧**操作**列选择**更多** \> **通过CloudShell管理集群**。

4.  执行以下命令，查看带有`component: nvidia-device-plugin`标签的Pod。

    ```
    kubectl get po -n kube-system -l component=nvidia-device-plugin -o wide
    ```

    预期输出：

    ```
    NAME                                            READY   STATUS    RESTARTS   AGE   IP              NODE                       NOMINATED NODE   READINESS GATES
    nvidia-device-plugin-cn-beijing.192.168.1.127   1/1     Running   0          6d    192.168.1.127   cn-beijing.192.168.1.127   <none>           <none>
    nvidia-device-plugin-cn-beijing.192.168.1.128   1/1     Running   0          17m   192.168.1.128   cn-beijing.192.168.1.128   <none>           <none>
    nvidia-device-plugin-cn-beijing.192.168.8.12    1/1     Running   0          9d    192.168.8.12    cn-beijing.192.168.8.12    <none>           <none>
    nvidia-device-plugin-cn-beijing.192.168.8.13    1/1     Running   0          9d    192.168.8.13    cn-beijing.192.168.8.13    <none>           <none>
    nvidia-device-plugin-cn-beijing.192.168.8.14    1/1     Running   0          9d    192.168.8.14    cn-beijing.192.168.8.14    <none>           <none>
    ```

    通过NODE列找到集群中刚添加节点所对应的Pod名称为`nvidia-device-plugin-cn-beijing.192.168.1.128`。

5.  执行以下命令查看节点的驱动版本是否符合预期。

    ```
    kubectl exec -ti nvidia-device-plugin-cn-beijing.192.168.1.128 -n kube-system -- nvidia-smi 
    ```

    预期输出：

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

    从以上输出的信息，可以知道驱动版本为418.181.07，通过节点池自定义安装NVIDIA驱动成功。


**相关文档**  


[通过节点池升级已有节点的NVIDIA驱动](/cn.zh-CN/Kubernetes集群用户指南/GPU/NPU/GPU运维管理/通过节点池升级已有节点的NVIDIA驱动.md)

