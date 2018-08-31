# 通过 Kubernetes Dashboard 创建应用 {#task_cpq_w4l_vdb .task}

您可以通过 Kubernetes Dashboard 创建应用。

1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。 
2.  在 Kubernetes 菜单下，单击左侧导航栏中的**集群**，进入 Kubernetes 集群列表页面。 
3.  选择所需的集群并单击右侧的**控制台**，进入 Kubernetes Dashboard。 

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16657/153570256411092_zh-CN.png)

4.  在 Kubernetes Dashboard 中，单击页面右上角的**创建**。 

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16657/153570256411093_zh-CN.png)

5.  在弹出的对话框中，设置应用的信息。 

    您可以通过以下 3 种方法之一创建应用：

    -   **使用文本创建**：直接输入 YAML 或 JSON 格式的编排代码创建应用。您需要了解对应的编排格式，如下所示是一个 YAML 格式的编排模板。

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16657/153570256411095_zh-CN.png)

    -   **使用文件创建**：通过导入已有的 YAML 或 JSON 配置文件创建应用。
    -   **使用配置创建**：本例中通过指定下边的设置创建应用。

        -   **应用名称**：所创建应用的名称。本示例中为 `nginx`。
        -   **容器镜像**：所要使用的镜像的 URL。本示例使用的是 Docker [Nginx](https://hub.docker.com/_/nginx/)。
        -   **容器组数量**：创建的应用的 pod 个数。
        -   **服务**：可设置为**外部** 或**内部**。**外部**表示创建一个可以从集群外部访问的服务；**内部**表示创建一个集群内部可以访问的服务。
        -   **高级选项**：您可以选择**显示高级选项**，对标签、环境变量等选项进行配置。 此设置将流量负载均衡到三个 Pod。
        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16657/153570256411098_zh-CN.png)

6.  单击**部署** 部署这些容器和服务。 

    您也可以单击**显示高级选项** 展开高级选项进一步配置相关参数。


单击**部署**后，您可以查看应用的服务或查看应用的容器。

单击左侧导航栏中的**容器**，您可以通过左侧的图标查看每个 Kubernetes 对象的状态。![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16657/153570256411100_zh-CN.png) 表示对象仍然处于部署状态。![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16657/153570256411101_zh-CN.png)表示对象已经完成部署。

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16657/153570256411103_zh-CN.png)

