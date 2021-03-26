---
keyword: [快速部署Serverless应用, Knative]
---

# 快速部署Serverless应用

本文以Hello World示例为您介绍如何通过Knative快速部署一个Serverless应用。

-   [创建Serverless Kubernetes集群](/cn.zh-CN/Serverless Kubernetes集群用户指南/快速入门/创建Serverless Kubernetes集群.md)。
-   [部署Knative](/cn.zh-CN/Kubernetes集群用户指南/Knative/Knative组件管理/一键部署Knative.md)。

## 操作步骤

1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。

2.  在控制台左侧导航栏中，单击**集群**。

3.  在集群列表页面中，单击目标集群名称或者目标集群右侧**操作**列下的**详情**。

4.  在集群管理页左侧导航栏中，选择**应用** \> **Knative**。

5.  在服务管理页签右上角，单击**创建服务**。

6.  设置**集群**、**命名空间**、**服务名称**，选择所要使用的镜像和镜像版本等配置信息。

    |参数|描述|
    |--|--|
    |**服务名称**|自定义该服务的名称。本例为helloworld-go。|
    |**镜像名称**|您可以单击选择镜像，在弹出的对话框中选择所需的镜像并单击**确定**。您还可以填写私有registry。填写的格式为domainname/namespace/imagename:tag。本例中为registry.cn-hangzhou.aliyuncs.com/knative-sample/helloworld-go。|
    |**镜像版本**|您可以单击选择镜像版本选择镜像的版本。若不指定，默认为latest。本例中为73fbdd56。|
    |**环境变量**|支持通过键值对的形式配置环境变量。本例中，`TARGET=Knative`。|

    界面其他参数详细信息请参见[参数说明](/cn.zh-CN/Kubernetes集群用户指南/Knative/Knative服务管理/快速部署Serverless应用.md)。

7.  单击**创建**。

    创建完成后，您可以在**服务管理**页签的列表中，看到新创建的服务。


## 服务访问

服务部署完成后，通过绑定Host域名与访问网关，然后可以直接访问服务URL。

1.  在Kubernetes菜单下，单击左侧导航栏的**Knative** \> **组件管理**，进入组件管理页面。可以看到访问网关。

2.  将访问网关地址与需要访问的域名进行Host绑定，在Hosts文件中添加绑定信息。

    ```
    网关+ 域名
    ```

    样例如下：

    ```
    47.95.XX.XX helloworld-go.default.example.com
    ```

3.  完成Host绑定后，可通过域名http://helloworld-go.default.example.com直接对服务进行访问。

    ![访问服务](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/0995659951/p52568.png)


## 更多示例

关于Knative更多示例请参见[Knative系列文章](https://yq.aliyun.com/articles/719274)。

