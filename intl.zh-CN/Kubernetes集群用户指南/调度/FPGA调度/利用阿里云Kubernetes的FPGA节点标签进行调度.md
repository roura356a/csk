---
keyword: [FPGA节点标签, 灵活调度]
---

# 利用阿里云Kubernetes的FPGA节点标签进行调度

在使用Kubernetes集群实现FPGA计算时，为了有效利用FPGA设备，可根据需要将应用调度到具有FPGA设备的节点上。本文介绍根据FPGA节点标签进行节点调度。

-   您已成功创建一个拥有FPGA节点的Kubernetes集群。具体操作，请参见[创建托管FPGA集群]()。
-   您已连接到Kubernetes集群，方便快速查看节点标签等信息。具体操作，请参见[通过kubectl连接Kubernetes集群](/intl.zh-CN/Kubernetes集群用户指南/集群/连接集群/通过kubectl连接Kubernetes集群.md)。

阿里云Kubernetes在部署FPGA节点的时候会发现FPGA的属性，并且作为NodeLabel信息暴露给您，拥有如下优势：

-   可以快速筛选FPGA节点。
-   部署时可以作为调度条件使用。

## 步骤一：查看FPGA节点的标签

**方式一：通过控制台查看FPGA节点的标签**

1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。

2.  在控制台左侧导航栏中，单击**集群**。

3.  在集群列表页面中，单击目标集群名称或者目标集群右侧**操作**列下的**详情**。

4.  在集群管理页左侧导航栏中，选择**节点管理** \> **节点**。

5.  在**节点列表**页面中，选择FPGA节点，单击**操作**列的**更多** \> **详情**。

    查看FPGA节点的标签。

    ![节点详情](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/4598249161/p21059.png)


**方式二：通过kubectl命令查看FPGA节点的标签**

1.  执行以下命令查看FPGA节点详情。

    ```
    kubectl get nodes
    ```

    预期输出：

    ```
    NAME                       STATUS   ROLES    AGE     VERSION
    cn-beijing.192.168.XX.X1   Ready    <none>   3h51m   v1.18.8-aliyun.1
    cn-beijing.192.168.XX.X2   Ready    <none>   3h41m   v1.18.8-aliyun.1             
    ```

2.  选择一个FPGA节点，执行以下命令查看该FPGA节点的标签。

    ```
    kubectl describe node cn-beijing.192.168.XX.X2
    ```

    预期输出：

    ```
    Name:               cn-beijing.192.168.XX.X2
    Roles:              <none>
    Labels:             ack.aliyun.com=c05888610e***
                        alibabacloud.com/nodepool-id=npfda879b6***
                        beta.kubernetes.io/arch=amd64
                        beta.kubernetes.io/instance-type=ecs.f3-c4f1.xlarge
                        beta.kubernetes.io/os=linux
                        failure-domain.beta.kubernetes.io/region=cn-beijing
                        failure-domain.beta.kubernetes.io/zone=cn-beijing-h
                        fpga.k8s.aliyun.com=f3
                        kubernetes.io/arch=amd64
                        kubernetes.io/hostname=cn-beijing.192.168.XX.X2
                        kubernetes.io/os=linux
                        node.kubernetes.io/instance-type=ecs.f3-c4f1.xlarge
                        topology.diskplugin.csi.alibabacloud.com/zone=cn-beijing-h
                        topology.kubernetes.io/region=cn-beijing
                        topology.kubernetes.io/zone=cn-beijing-h
    ```

    从预期输出可得，该FPGA节点包含节点标签（NodeLabel）`fpga.k8s.aliyun.com=f3`，下方示例使用该节点标签实现应用调度。


## 步骤二：调度应用到FPGA节点

1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。

2.  在控制台左侧导航栏中，单击**集群**。

3.  在集群列表页面中，单击目标集群名称或者目标集群右侧**操作**列下的**详情**。

4.  在集群管理页左侧导航栏中，选择**工作负载** \> **无状态**。

5.  在无状态页面中，单击**使用YAML创建资源**。

6.  选择自定义示例模板，将以下示例复制到**模板**中。

    ```
    apiVersion: batch/v1
    kind: Job
    metadata:
      name: fpga-run-task1
    spec:
      backoffLimit: 0
      completions: 1
      parallelism: 1
      template:
        spec:
          nodeSelector:
            fpga.k8s.aliyun.com: f3
          containers:
          - image: <your image>
            imagePullPolicy: Always
            name: fpga-run-task1
            resources:
              limits:
                xilinx.com/fpga-aliyun-f3: 1
            securityContext:
              privileged: true
    ```

    **说明：** `<your image>`替换为您自定义的应用镜像地址。

7.  单击**创建**。

    在集群管理页左侧导航栏选择**工作负载** \> **容器组**。

    在容器组列表中，您可看到示例的Pod（容器组）已被成功调度到对应的节点上，从而实现基于FPGA节点标签的灵活调度。


