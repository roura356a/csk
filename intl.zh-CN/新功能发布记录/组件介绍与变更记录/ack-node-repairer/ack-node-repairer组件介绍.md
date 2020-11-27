---
keyword: [ack-node-repairer, 节点健康检测和自愈, ACK的自愈系统（ACK Node Repairer）]
---

# ack-node-repairer组件介绍

当Node Problem Detector（简称NPD）组件检测到节点上的故障并生成节点的事件（Event）或者Condition上报给集群时，ACK的自愈系统（ACK Node Repairer）会监听每个节点上的新故障事件，并根据配置对故障节点进行相应的修复操作。本文介绍如何安装和配置ACK Node Repairer。

-   您已创建以下任一类型集群：
    -   [创建Kubernetes托管版集群](/intl.zh-CN/Kubernetes集群用户指南/集群管理/创建集群/创建Kubernetes托管版集群.md)。
    -   [创建Kubernetes专有版集群](/intl.zh-CN/Kubernetes集群用户指南/集群管理/创建集群/创建Kubernetes专有版集群.md)。
-   您已安装ACK Node Problem Detector。具体步骤，请参见[场景3：使用node-problem-detector与eventer实现节点异常告警](/intl.zh-CN/Kubernetes集群用户指南/监控管理/事件监控.md)。
-   [通过kubectl连接Kubernetes集群](/intl.zh-CN/Kubernetes集群用户指南/集群管理/连接集群/通过kubectl连接Kubernetes集群.md)。

节点自愈系统默认集成了常见的节点故障以及对应的节点修复操作。当节点出现故障时，自愈系统会自动对故障所在节点触发相应的修复操作。当故障被修复后，NPD会修改故障状态，达到故障检测以及修复的闭环。运维人员也可以自定义需要采取修复操作的故障以及具体的修复操作。

NPD是Kubernetes节点诊断的工具，可以将节点的异常，例如Docker Engine Hang、Linux Kernel Hang、网络出网异常、文件描述符异常转换为节点的事件。

## 安装ack-node-repairer

使用ACK的自愈系统（ACK Node Repairer）前，您需要安装ack-node-repairer组件。

1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。

2.  在控制台左侧导航栏中，选择**市场** \> **应用目录**。

3.  找到并单击ack-node-repairer，然后在应用目录 - ack-node-repairer页面的**参数**页签上，设置账号的AK信息。

    ![AK](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/8429646061/p185927.png)

    AK信息设置如下：

    -   accessKey：nodeRepairer.accessKey
    -   accessSecret：nodeRepairer.accessSecret
4.  在页面右侧，单击**创建**。


## 配置ack-node-repairer

安装ACK的自愈系统（ACK Node Repairer）后，ACK Node Repairer会默认开启当前版本所支持的所有自愈功能。您可对某个自愈功能进行开关和配置，以下以NTP服务故障为例。

1.  查看default-node-repairer资源的YAML文件。

    在安装ACK自愈系统后，集群会默认在kube-system命名空间下创建类型为noderepairers.nodes.alibabacloud.com，名为default-node-repairer的资源，该资源定义了自愈系统所监控的节点问题及相应的修复操作。执行以下命令查看YAML文件中定义的内容。

    ```
    kubectl -n kube-system get noderepairers.nodes.alibabacloud.com default-node-repairer -o yaml
    ```

2.  修改default-node-repairer。

    在spec.rules列表中加入用于检测`NTPProblem`的detector和对应问题修复的healers，其定义片段如下：

    ```
    spec:
      rules:
      # 用于检测NTPProblem的detector，和对应修复问题的nodeOperation。
      - detector:
          conditionType: NTPProblem
          type: conditionType
          paused: false
        healers:
        - nodeOperation: restart-ntpd
          type: nodejob
    ```

    **说明：** 针对每一种故障场景的自愈，您需要关联故障的节点Condition与其对应的修复操作。rules.detector.conditionType是集群中出现的异常Condition。当设置rules.detector.paused为true时，此问题的自愈功能将被关闭。

    至此，当集群节点上有NTP服务异常时，ACK的自愈框架将会通过[OOS]()到节点上执行`systemctl restart chronyd.service`命令，重启NTP服务。


## 自愈过程与结果记录

集群会默认在kube-system命名空间下创建类型为nodejobs.nodes.alibabacloud.com的资源来记录每次执行的自愈操作。您可执行以下命令查看资源中定义的内容，并通过Status字段查看自愈执行的结果。

```
kubectl -n kube-system get nodejobs.nodes.alibabacloud.com {nodejob_cr_name} -o yaml
```

