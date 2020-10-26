# 通过Kubernetes Dashboard创建应用

您可以通过Kubernetes Dashboard创建应用。

1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。

2.  在控制台左侧导航栏中，单击**集群**。

3.  在集群列表页面，选择目标集群，选择**操作**列下的**更多** \> **Dashboard**。

4.  在Kubernetes Dashboard中，单击页面右上角的**创建**。

5.  在**创建资源**页面，设置应用的信息。

    您可以通过以下3种方法之一创建应用：

    -   **使用文本创建**：直接输入YAML或JSON格式的编排代码创建应用。您需要了解对应的编排格式，如下所示是一个YAML格式的编排模板。

        ![文本创建](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/zh-CN/8975659951/p11095.png)

    -   **使用文件创建**：通过导入已有的YAML或JSON配置文件创建应用。
    -   **使用配置创建**：本例中通过指定下边的设置创建应用。

        |参数|描述|
        |--|--|
        |**应用名称**|所创建应用的名称。本示例中为`nginx`。|
        |**容器镜像**|所要使用的镜像的URL。本示例使用的是Docker [Nginx](https://hub.docker.com/_/nginx/)。|
        |**容器组数量**|创建的应用的Pod个数。|
        |**服务**|可设置为**外部**或**内部**：        -   **外部**表示创建一个可以从集群外部访问的服务。
        -   **内部**表示创建一个集群内部可以访问的服务。 |
        |**高级选项**|您可以选择**显示高级选项**，对标签、环境变量等选项进行配置。 此设置将流量负载均衡到三个Pod。|

6.  单击**部署**部署这些容器和服务。

    您也可以单击**显示高级选项**展开高级选项进一步配置相关参数。


单击**部署**后，您可以查看应用的服务或查看应用的容器。

单击左侧导航栏中的**容器组**，您可以通过左侧的图标查看每个容器的状态。![小图标](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/zh-CN/8975659951/p11100.png)表示容器仍然处于部署状态。![部署](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/zh-CN/8975659951/p11101.png)表示容器已经完成部署。

![部署完成](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/zh-CN/8975659951/p11103.png)

