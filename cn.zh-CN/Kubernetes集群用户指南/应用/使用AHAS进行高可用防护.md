---
keyword: [AHAS, 流量防护, 应用流控]
---

# 使用AHAS进行高可用防护

对于部署在容器服务Kubernetes版ACK（Container Service for Kubernetes）中的Java应用，您可以使用应用高可用服务AHAS（Application High Availability Service）来对系统进行高可用防护，实现服务间限流、降级、应用系统保护等操作，保障应用24小时高可用。

-   [创建Kubernetes托管版集群](/cn.zh-CN/Kubernetes集群用户指南/集群/创建集群/创建Kubernetes托管版集群.md)
-   [开通AHAS](/cn.zh-CN/快速入门/开通AHAS.md)

AHAS是一款阿里云应用高可用服务相关产品。只要为部署在ACK中的Java应用安装AHAS应用防护组件后，您无需修改任何代码，就能借助AHAS对Java应用进行全方位系统防护，针对性的对系统进行流量管控、服务降级等操作。更多信息，请参见[什么是应用防护](/cn.zh-CN/应用防护/什么是应用防护.md)。

## 步骤一：安装AHAS应用防护组件

1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。

2.  在控制台左侧导航栏中，选择**市场** \> **应用目录**。

3.  在应用目录页面的搜索框中搜索**ack-ahas-sentinel-pilot**，然后单击**ack-ahas-sentinel-pilot**。

4.  在右侧创建面板中选择集群，然后单击**创建**。


## 步骤二：为Java应用开启AHAS

1.  在[容器服务管理控制台](https://cs.console.aliyun.com)左侧导航栏单击**集群**。

2.  在集群列表页面中，单击目标集群名称或者目标集群右侧**操作**列下的**详情**。

3.  在集群管理页左侧导航栏中，选择**工作负载** \> **无状态**。

4.  为Java应用开启AHAS。

    以下步骤分别对应创建新应用和已有应用这两种情况：

    -   如需在创建新应用的同时开启AHAS应用防护，请按以下步骤操作。
        1.  单击**无状态**页签，然后单击右上角的**使用模板创建**。
        2.  选择**示例模板**，并在模板中将以下`annotations`添加到`spec>template>metadata`层级下。

            ```
            annotations:
              #是否开启AHAS应用防护插件，on、true表示开启，off、false表示关闭。
              ahasPilotAutoEnable: "on"
              #AHAS应用名。
              ahasAppName: "<your-deployment-name>"
              #AHAS license。公网环境需配置AHAS license，VPC环境无需配置，AHAS license可以在AHAS控制台>应用防护>新应用接入中获取。
              #ahasLicense: "<your-license>"
            ```

            完整YAML示例模板如下：

            ```
            apiVersion: apps/v1
            kind: Deployment
            metadata:
              name: agent-foo
              labels:
                name: agent-foo
            spec:
              replicas: 1
              selector:
                matchLabels:
                  name: agent-foo
              template:
                metadata:
                  labels:
                    name: agent-foo
                  annotations:
                    ahasPilotAutoEnable: "on"
                    ahasAppName: "K8sFooTest"
                    ahasNamespace: "default"
                spec:
                  containers:
                  - name: foo
                    image: registry.cn-hangzhou.aliyuncs.com/sentinel-docker-repo/foo:0.1.1
                    imagePullPolicy: Always
            ```

    -   如需为现有应用开启AHAS应用防护，请按以下步骤操作。
        1.  单击**无状态**或**有状态**页签，然后在目标应用右侧**操作**列中选择**更多** \> **查看YAML**。
        2.  在编辑YAML对话框中将以下`annotations`添加到`spec>template>metadata`层级下，并单击**更新**。

            ```
            annotations:
              ahasPilotAutoEnable: "on"
              ahasAppName: "<your-deployment-name>"
              #ahas namespace, 默认default
              #ahasNamespace: "default"
              #ahas license, 公网需要
              #ahasLicense: "<your-license>"
            ```

            **说明：** 请将`<your-deployment-name>`替换为您的应用名称。

    以下表格介绍了YAML中部分配置项和默认值。

    |参数|描述|默认值|
    |--|--|---|
    |`image.imageTag`|Pilot镜像Tag。|`0.1.1`|
    |`image.imagePullPolicy`|镜像拉取策略，必须是Always、IfNotPresent、Never三者中的一个。|`Always`|
    |`controller.logLevel`|Pilot日志级别，1表示INFO，2表示DEBBUG。|`1`|
    |`controller.region_id`|目标集群所在的region，如cn-hangzhou、cn-beijing、cn-shenzhen、cn-shanghai，如果是公网，则填写为cn-public。|`cn-hangzhou`|


## 后续步骤

完成上述步骤后，您就为部署在ACK中的应用开启了AHAS服务。在无状态或有状态页面，单击目标应用**操作**列的**应用流控**，进入AHAS控制台，为应用设置流量防护规则。

## 参考文档

为应用设置流量防护规则的具体步骤，请参见以下文档：

-   [配置流控规则](/cn.zh-CN/应用防护/配置规则/配置流控规则.md)
-   [配置隔离规则](/cn.zh-CN/应用防护/配置规则/配置隔离规则.md)
-   [配置熔断规则](/cn.zh-CN/应用防护/配置规则/配置熔断规则.md)

