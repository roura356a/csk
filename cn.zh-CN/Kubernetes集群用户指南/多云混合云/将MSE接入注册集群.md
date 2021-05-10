---
keyword: [MSE, 注册集群]
---

# 将MSE接入注册集群

微服务引擎MSE（Microservice Engine）是一个面向业界主流开源微服务框架Spring Cloud和Dubbo一站式微服务平台，提供治理中心、托管的注册中心和托管的配置中心。本文介绍如何将MSE接入注册集群应用中。

您已通过容器服务Kubernetes版接入一个Kubernetes集群。具体操作，请参见[创建注册集群并接入本地数据中心集群](/cn.zh-CN/Kubernetes集群用户指南/多云混合云/注册集群管理/创建注册集群并接入本地数据中心集群.md)。

## 安装ack-mse-pilot组件

1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。

2.  在控制台左侧导航栏中，选择**市场** \> **应用目录**。

3.  在**应用目录**页面搜索并单击ack-mse-pilot。

4.  在**应用目录- ack-mse-pilot**页面右侧区域选择目标**集群**，单击**参数**页签，设置相应的参数。

    ![MSE镜像](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/2675260261/p272022.png)

    |参数|说明|
    |--|--|
    |image|ack-mse-pilot组件镜像地址。如果您是通过公网注册的外部集群，需要删除镜像参数中的VPC。例如，镜像参数为`registry-vpc.cn-hangzhou.aliyuncs.com/arms-docker-repo/arms-pilot:v1.41-mse`，删除VPC后的镜像参数为registry.cn-hangzhou.aliyuncs.com/arms-docker-repo/arms-pilot:v1.41-mse。 |
    |AccessKeyId|您的阿里云AccessKeyId。AccessKey需要包含MSE访问权限。具体操作，请参见[为集群授予MSE治理中心的访问权限](/cn.zh-CN/快速入门/微服务治理/ACK微服务应用接入MSE治理中心.md)。|
    |AccessKeySecret|您的阿里云AccessKeySecret。|

5.  单击**创建**。

    安装ack-mse-pilot组件大约需要2分钟，请耐心等待。

    创建成功后，会自动跳转到目标集群的**发布**页面，检查安装结果。若下图中所有资源创建成功，则说明组件安装成功。

    ![Helm-发布-MSC Pilot](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/8730698951/p100153.png)


## 在创建新应用时开启MSE微服务治理

1.  登录[容器服务控制台](https://cs.console.aliyun.com)。

2.  在左侧导航栏单击**集群**，然后在**集群列表**页面单击目标集群的集群名称。

3.  在集群信息左侧导航栏选择**工作负载** \> **无状态**，然后在**无状态**页面左上角选择**命名空间**，在右上角单击**使用YAML创建资源**。

    ![工作负载](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/1426640261/p100159.png)

4.  在**创建**页面上选择**示例模板**，并在**模板**中将以下`annotations`添加到**spec** \> **template** \> **metadata**层级下，然后单击**创建**。

    ```
    annotations:
      msePilotAutoEnable: "on"
      msePilotCreateAppName: "<your-deployment-name>"
    ```

    **说明：** 需要将`<your-deployment-name>`替换为您实际使用的应用名称。

    创建一个无状态（Deployment）应用并开启MSE微服务治理的完整YAML示例模板如下：

    ```
    apiVersion: apps/v1
    kind: Deployment
    metadata:
      labels:
        app: productserivce
      name: productserivce
      namespace: default
    spec:
      replicas: 1
      selector:
        matchLabels:
          app: productserivce
      template:
        metadata:
          annotations:
            msePilotAutoEnable: 'on'
            msePilotCreateAppName: productserivce
          labels:
            app: productserivce
        spec:
          containers:
            - image: >-
                registry.cn-hangzhou.aliyuncs.com/alibabacloud-microservice-demo/productservice
              imagePullPolicy: Always
              name: productserivce
              resources:
                requests:
                  cpu: 250m
                  memory: 512Mi
    ```


## 为已有应用开启MSE微服务治理

如果已经在容器服务Kubernetes版中部署了应用（包括有状态和无状态），则可以为有状态或无状态应用开启MSE微服务治理。有状态和无状态应用的开启步骤一致，本文仅以无状态应用为例介绍如何开启MSE微服务治理。

1.  登录[容器服务控制台](https://cs.console.aliyun.com)。

2.  在左侧导航栏单击**集群**，然后在**集群列表**页面单击目标集群的集群名称。

3.  在集群信息左侧导航栏选择**工作负载** \> **无状态**，然后在**无状态**页面左上角选择**命名空间**，并在目标应用的**操作**列中单击**更多**，在列表中单击**查看Yaml**。

    ![无状态-查看 YAML](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/1426640261/p99783.png)

4.  在**编辑YAML**对话框中将以下annotations添加到**spec** \> **template** \> **metadata** ，并单击**更新**。

    ```
    annotations:
      msePilotAutoEnable: "on"
      msePilotCreateAppName: "<your-deployment-name>"
    ```

    **说明：** 需要将`<your-deployment-name>`替换为您实际使用的应用名称。


完成上述步骤后，您就为部署在容器服务Kubernetes版中的应用开启了MSE微服务治理。登录[MSE治理中心控制台](https://mse.console.aliyun.com/#/msc/home)，即可使用MSE微服务治理对您的Spring Cloud和Dubbo应用进行服务治理，相关内容，请参见[使用指引](/cn.zh-CN/.md)。

