---
keyword: [共享GPU, 基础版, 专业版, 升级]
---

# 在ACK Pro版集群中将共享GPU基础版升级为共享GPU专业版

ACK专有版集群安装的共享GPU基础版在ACK Pro版集群中是无法正常使用的，ACK Pro集群仅支持共享GPU专业版。所以当集群由ACK专有版集群迁移至ACK Pro版集群后，共享GPU组件也需要升级。本文介绍如何在ACK Pro版集群将共享GPU基础版升级为共享GPU专业版。

已将ACK专有版集群迁移至ACK Pro托管版集群中，且迁移前的ACK专有版集群已安装共享GPU基础版。具体操作，请参见[热迁移ACK专有版集群至Pro托管集群](/cn.zh-CN/Kubernetes集群用户指南/集群/迁移至Pro集群/热迁移ACK专有版集群至Pro托管集群.md)。

1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。

2.  在控制台左侧导航栏中，单击**集群**。

3.  在集群列表页面中，单击目标集群名称或者目标集群右侧**操作**列下的**详情**。

4.  在集群管理页左侧导航栏中，选择**工作负载** \> **任务**。

5.  在任务页面，单击右上角的**使用YAML创建资源**。

6.  在创建页面，选择**自定义**的**示例模板**，将以下YAML示例复制到**模板**。

    该任务用于卸载旧版共享GPU组件及转换共享GPU节点标签。

    ```
    apiVersion: batch/v1
    kind: Job
    metadata:
      name: gpushare-migration
      namespace: kube-system
    spec:
      backoffLimit: 0
      template:
        spec:
          serviceAccount: admin
          containers:
          - name: gpushare-migration
            # 根据不同集群的地域，您需要修改以下镜像地址中的地域<cn-beijing>信息。
            image: registry-vpc.cn-beijing.aliyuncs.com/acs/gpushare-migration:v0.1.0
            env:
              - name: CHANGE_LABELS_INFO
                value: "cgpu=true::ack.node.gpu.schedule=cgpu,gpushare=true::ack.node.gpu.schedule=share"
          restartPolicy: Never
    ```

7.  单击**创建**，单击任务**gpushare-migration**的链接查看创建进度。

    ![gpushare-migration](https://help-static-aliyun-doc.aliyuncs.com/assets/img/zh-CN/7601789261/p311696.png)

    在任务**gpushare-migration**详情页面的容器组页签下，若任务的状态为**Completed**，表示任务运行成功。

8.  安装共享GPU专业版。具体操作，请参见[步骤一：安装共享GPU组件](/cn.zh-CN/Kubernetes集群用户指南/ACK Pro集群/GPU调度/共享GPU调度专业版/安装并使用共享GPU组件和资源工具.md)。

9.  安装查询集群GPU显存使用情况的工具。具体操作，请参见[步骤四：安装和使用GPU资源查询工具](/cn.zh-CN/Kubernetes集群用户指南/ACK Pro集群/GPU调度/共享GPU调度专业版/安装并使用共享GPU组件和资源工具.md)。


关于如何验证共享GPU专业版的共享调度及显存隔离能力，请参见[运行共享GPU示例](/cn.zh-CN/Kubernetes集群用户指南/ACK Pro集群/GPU调度/共享GPU调度专业版/运行共享GPU示例.md)。

