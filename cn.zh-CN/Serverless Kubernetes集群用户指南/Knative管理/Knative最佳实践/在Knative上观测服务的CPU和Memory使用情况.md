# 在Knative上观测服务的CPU和Memory使用情况

在服务运行过程中，您可以在Knative上观测当前服务运行的状况，包括CPU和Memory使用情况。本文主要介绍如何观测这些信息。

-   阿里云Serverless Kubernetes（ASK）集群已开通Knative功能。具体操作，请参见[开启Knative](/cn.zh-CN/Serverless Kubernetes集群用户指南/Knative管理/Knative组件管理/开启Knative.md)。
-   ASK集群已开通阿里云Prometheus监控功能。具体操作，请参见[阿里云Prometheus监控](/cn.zh-CN/Serverless Kubernetes集群用户指南/监控管理/阿里云Prometheus监控.md)。

## 操作步骤

1.  登录[ARMS控制台](https://arms.console.aliyun.com/#/home)。

2.  在控制台左侧导航栏中，单击**Prometheus监控**，然后在该页面单击目标ASK集群。

    **说明：** 如果您没有安装过Prometheus，需先在Prometheus监控页面单击**安装**。

    ![98-ch](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/8152139061/p207447.png)

3.  在大盘列表页面的**名称**列单击**Prometheus**。

4.  单击左侧**Explore**，在**Select datasource**下拉列表选择目标集群。

    ![99](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/8152139061/p207728.png)

5.  查询Knative服务的CPU使用情况。

    在**Metrics**文本框中输入以下PromQL查询命令，以**helloworld-go**服务为例，单击**Run Query**查询该服务的CPU使用情况：

    ```
    sum (rate (container_cpu_usage_seconds_total{pod_name=~"helloworld-go.*",namespace="default"}[1m])) 
    ```

    ![100](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/8152139061/p207740.png)

6.  查询Knative服务的Memory使用情况。

    在**Metrics**文本框中输入以下PromQL查询命令，以**helloworld-go**服务为例，单击**Run Query**查询该服务的Memory使用情况：

    ```
    sum (rate (container_memory_working_set_bytes{pod_name=~"helloworld-go.*",namespace="default"}[1m])) 
    ```

    ![101](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/8152139061/p207742.png)


