# 部署Serving Hello World应用

本文以Hello World示例为您介绍如何部署Knative应用。

-   [创建Kubernetes托管版集群](/cn.zh-CN/Kubernetes集群用户指南/集群管理/创建集群/创建Kubernetes托管版集群.md)
-   [部署Knative](/cn.zh-CN/Kubernetes集群用户指南/Knative管理/Knative组件管理/一键部署Knative.md)
-   您已经成功部署Serving组件，参见[部署Knative组件](/cn.zh-CN/Kubernetes集群用户指南/Knative管理/Knative组件管理/部署Knative组件.md)。

## 步骤一：部署服务

1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。

2.  在集群列表页面中，单击目标集群名称或者目标集群右侧**操作**列下的**详情**。

3.  在集群管理页左侧导航栏中，选择**应用** \> **Knative**。

4.  单击服务管理页签，然后单击页面右上角的**创建服务**。

5.  设置**命名空间**、**服务名称**，选择所要使用的镜像和镜像版本等配置信息。

    |参数|描述|
    |--|--|
    |**服务名称**|自定义该服务的名称。本例为helloworld-go。|
    |**镜像名称**|您可以单击选择镜像，在弹出的对话框中选择所需的镜像并单击**确定**。您还可以填写私有registry。填写的格式为domainname/namespace/imagename:tag。本例中为registry.cn-hangzhou.aliyuncs.com/knative-sample/helloworld-go。|
    |**镜像版本**|您可以单击选择镜像版本选择镜像的版本。若不指定，默认为latest。本例中为73fbdd56。|
    |**环境变量**|支持通过键值对的形式配置环境变量。本例中，`TARGET=Knative`。|

    界面其他参数详细信息请参见[参数说明](/cn.zh-CN/Kubernetes集群用户指南/Knative管理/Knative服务管理/创建Knative服务.md)。

6.  单击**创建**。

    创建完成后，您可以在服务管理页面的服务列表中，看到新创建的服务。

    ![创建服务](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/0995659951/p52893.png)


## 步骤二：访问服务

服务部署完成后，通过绑定Host域名与访问网关，然后可以直接访问服务URL。

1.  在服务管理页签，单击服务名称。

    您可以在Knative服务详情页的**基本信息**区域，查看访问网关及域名。

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

