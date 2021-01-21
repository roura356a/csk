# 快速部署Serverless应用

在Knative中服务代表应用的概念，本文主要为您介绍如何创建Knative服务。

-   [创建Kubernetes托管版集群](/cn.zh-CN/Kubernetes集群用户指南/集群管理/创建集群/创建Kubernetes托管版集群.md)
-   [一键部署Knative](/cn.zh-CN/Kubernetes集群用户指南/Knative管理/Knative组件管理/一键部署Knative.md)
-   部署Serving组件，请参见[部署Knative组件](/cn.zh-CN/Kubernetes集群用户指南/Knative管理/Knative组件管理/部署Knative组件.md)。

## 步骤一：部署Knative服务

1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。

2.  在集群列表页面中，单击目标集群名称或者目标集群右侧**操作**列下的**详情**。

3.  在集群管理页左侧导航栏中，选择**应用** \> **Knative**。

4.  在服务管理页签，单击右上角的**创建服务**。

5.  设置配置信息。

    |参数|说明|
    |--|--|
    |**集群**|选择需要创建服务的集群。|
    |**命名空间**|选择该服务所属的命名空间。|
    |**服务名称**|自定义该服务的名称。|
    |**镜像名称**|您可以单击选择镜像，在弹出的对话框中选择所需的镜像并单击**确定**。您还可以填写私有registry。填写的格式为domainname/namespace/imagename:tag。本例中为registry.cn-hangzhou.aliyuncs.com/knative-sample/helloworld-go。 |
    |**镜像版本**|您可以单击选择镜像版本选择镜像的版本。本例中为73fbdd56。

![举例](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/8895659951/p127510.png) |
    |**访问协议**|支持**HTTP**和**gRPC**两种访问协议。 |
    |**容器端口**|设置暴露的容器访问端口，端口号必须介于1~65535。 |
    |**仅内部访问**|选中**仅内部访问**后，不支持通过公网访问该服务。 |
    |**请求最大并发数**|容器允许的最大请求并发数。默认0，表示不限制并发数。 |
    |**最小保留实例数**|在无访问请求的情况下，最小保留的运行实例数。设置为0时，表示没有访问请求时，实例缩为0。 |
    |**最大扩容实例数**|允许扩容出来的最多实例个数。 |
    |**资源限制**|可指定该应用所能使用的资源上限，包括CPU和内存两种资源，防止占用过多资源。其中，CPU资源的单位为cores，即一个核；内存的单位为Bytes，可以为Mi。 |
    |**生命周期**|包含命令（Command）和参数（Args），配置说明如下：

    -   如果均不配置，则使用镜像默认的命令和参数。
    -   如果仅配置参数，则使用镜像默认的命令及新配置的参数。
    -   如果均配置，则会覆盖镜像默认的配置。 |
    |**环境变量**|支持通过键值对的形式配置环境变量。 |
    |**数据卷**|支持增加本地存储和云存储声明（PVC）。    -   本地存储：支持主机目录（hostpath）、配置项（configmap）、保密字典（secret）和临时目录，将对应的挂载源挂载到容器路径中。更多信息参见[volumes](https://kubernetes.io/docs/concepts/storage/volumes/?spm=0.0.0.0.8VJbrE)
    -   云存储声明（PVC）：支持云存储。 |

6.  单击**创建**。


## 步骤二：访问服务

Knative服务创建完成后，通过绑定Host域名与访问网关，可以直接访问服务地址。操作步骤如下。

1.  在服务管理页签，单击服务名称。

    您可以在Knative服务详情页的**基本信息**区域，查看访问网关及域名。

    ![新服务](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/8895659951/p52562.png)

2.  将访问网关地址与需要访问的域名进行Host绑定，在Hosts文件中添加绑定信息。

    绑定样例如下。

    ```
    121.xx.xxx.xx helloworld-go.default.example.com
    ```

3.  完成Host绑定后，可通过域名直接对服务进行访问。

    ![访问服务](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/0995659951/p52568.png)


