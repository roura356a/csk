---
keyword: [CronJob, 定时任务, Job, k8s]
---

# 创建定时任务工作负载CronJob

定时任务CronJob用于创建周期性以及重复性的任务，例如执行备份操作或者发送邮件。Job负责批量处理短暂的一次性任务，即仅执行一次的任务，而CronJob则是在Job上增加了时间调度。本文介绍如何创建定时任务CronJob。

## 通过控制台创建CronJob

**使用镜像创建CronJob**

1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。

2.  在控制台左侧导航栏中，单击**集群**。

3.  在集群列表页面中，单击目标集群名称或者目标集群右侧**操作**列下的**详情**。

4.  在集群管理页左侧导航栏中，选择**工作负载** \> **定时任务**。

5.  在**定时任务**页面右上角，单击**使用镜像创建**。

6.  设置创建CronJob的参数。

    1.  在**应用基本信息**配置向导页面设置应用基本信息。关于参数的详细描述，请参见[步骤五：设置应用基本信息](/intl.zh-CN/Kubernetes集群用户指南/应用/工作负载/创建任务工作负载Job.md)。

    2.  在**容器配置**配置向导页面设置容器信息。关于参数的详细描述，请参见[容器配置](/intl.zh-CN/Kubernetes集群用户指南/应用/工作负载/创建任务工作负载Job.md)。

    3.  在**高级配置**配置向导页面完成CronJob的高级参数的设置。

        |分类|参数|描述|
        |--|--|--|
        |定时任务|定时规则|您可以按天、按星期、按月，或者使用Cron表达式设置定时规则。Cron表达式是一个字符串，字符串以5或6个空格隔开，分为6或7个域，每一个域代表一个含义，Cron有以下两种语法格式：

        -   ```
秒（Seconds） 分（Minutes） 小时（Hours） 日期（DayofMonth） 月份（Month） 星期（DayofWeek） 年（Year）
```

        -   ```
秒（Seconds） 分（Minutes） 小时（Hours） 日期（DayofMonth） 月份（Month） 星期（DayofWeek）
```

关于更多Cron表达式说明，请参见[Cron Expressions](https://docs.oracle.com/cd/E12058_01/doc/doc.1014/e12030/cron_expressions.htm)。 |
        |并发策略|您可以设置以下三种并发策略：

        -   Allow：允许并发运行任务，会抢占集群资源。
        -   Forbid：禁止并发运行，如果前一个还没有完成，则直接跳过下一个任务。
        -   Replace：已到新任务创建时间点，但前一个任务还未完成，新的任务会取代前一个任务。 |
        |任务记录|可以设置保留执行成功或执行失败的任务个数，设置为0表示不保留。 |
        |任务设置|成功运行的Pod数|关于任务设置的参数描述，请参见[任务配置](/intl.zh-CN/Kubernetes集群用户指南/应用/工作负载/创建任务工作负载Job.md)。|
        |并行运行的Pod数|
        |超时时间|
        |重试次数|
        |重启策略|
        |标签和注解|Pod标签|为Pod设置标签的名称和值。**说明：** 标签名称长度为1~253字符，只能包含大小写字母、数字、短划线（-）、下划线（\_）和小数点（.）。 |
        |Pod注解|为Pod设置注解的名称和值。**说明：** 注解名称长度为1~253字符，只能包含大小写字母、数字、短划线（-）、下划线（\_）和小数点（.）。 |

7.  单击**创建**。

    成功创建定时任务后，在定时任务列表中可以查看新创建的定时任务。


**使用YAML创建CrobJob**

1.  在**定时任务**页面右上角，单击**使用YAML创建资源**。

2.  在创建页面，在**模板**区域编辑模板信息。

3.  单击**创建**。


## 通过kubectl命令行创建CronJob

在使用kubectl创建CronJob前，您需要下载kubectl并使用kubectl命令连接集群。具体操作，请参见[通过kubectl连接Kubernetes集群](/intl.zh-CN/Kubernetes集群用户指南/集群/连接集群/通过kubectl连接Kubernetes集群.md)。

创建CronJob涉及的主要参数如下：

|参数|描述|
|--|--|
|.spec.schedule|指定任务运行周期，格式同[Cron schedule](https://kubernetes.io/docs/concepts/workloads/controllers/cron-jobs/#cron-schedule-syntax)。|
|.spec.jobTemplate|指定需要运行的任务，格式同[Job patterns](https://kubernetes.io/docs/concepts/workloads/controllers/job/#job-patterns)。|
|.spec.startingDeadlineSeconds|指定任务开始的截止期限。|
|.spec.concurrencyPolicy|指定任务的并发策略，支持Allow、Forbid和Replace三个选项：-   Allow：允许并发运行任务，会抢占集群资源。
-   Forbid：禁止并发运行，如果前一个还没有完成，则直接跳过下一个任务。
-   Replace：已到新任务创建时间点，但前一个任务还未完成，新的任务会取代前一个任务。 |

本文以创建一个名为hello的CronJob为例，说明如何使用Kubectl创建CronJob。

1.  保存以下YAML内容至cronjob.yaml文件中。

    ```
    apiVersion: batch/v2alpha1
    kind: CronJob
    metadata:
      name: hello
    spec:
      schedule: "*/1 * * * *"
      jobTemplate:
        spec:
          template:
            spec:
              containers:
              - name: hello
                image: busybox
                args:
                - /bin/sh
                - -c
                - date; echo Hello from the Kubernetes cluster
              restartPolicy: OnFailure
    ```

2.  执行以下命令创建CronJob。

    ```
    kubectl create -f cronjob.yaml
    ```

    当输出`cronjob.batch/hello created`时，则说明成功创建定时任务CronJob。


## 相关操作

创建完定时任务后，您可以进行以下操作：

-   在定时任务名称右侧的**操作**列中，单击**详情**查看定时任务的基本信息，包括任务列表、事件、日志。
-   在定时任务名称右侧的**操作**列中，选择**更多** \> **查看Yaml**、**更多** \> **停止**、**更多** \> **删除**，查看定时任务的YAML信息、停止任务、删除任务。

