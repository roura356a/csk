# 创建 Knative 服务 {#task_1305477 .task}

本文主要为您介绍如何创建 Knative 服务。

-   您已经成功创建一个 Kubernetes 集群，参见[创建Kubernetes集群](intl.zh-CN//创建Kubernetes 集群.md#)。
-   您已经成功部署 Knative，参见 [部署 Knative](intl.zh-CN/Kubernetes集群用户指南/Knative 管理/部署 Knative.md#)。
-   您已经成功部署 Serving 组件，参见[部署组件](intl.zh-CN/Kubernetes集群用户指南/Knative 管理/部署组件.md#)。

1.  登录[容器服务管理控制台](https://cs.console.aliyun.com/)。
2.  在 Kubernetes 菜单下，选择**Knative** \> **服务管理**，进入服务管理页面。
3.  单击右上角的**创建服务**。![创建服务](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/1040500/156464507352550_zh-CN.png)


4.  设置集群名称、命名空间、服务名称，选择所要使用的镜像和镜像版本等配置信息。 

    ![配置页面](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/1040500/156464507452851_zh-CN.png)

    -   **集群**：选择需要创建服务的集群。
    -   **命名空间**：选择该服务所属的命名空间。
    -   **服务名称**：自定义该服务的名称。
    -   **镜像名称**：您可以单击选择镜像，在弹出的对话框中选择所需的镜像并单击**确定**。您还可以填写私有 registry。填写的格式为domainname/namespace/imagename:tag。本例中为 registry.cn-hangzhou.aliyuncs.com/knative-sample/helloworld-go。
    -   **镜像版本**：您可以单击选择镜像版本选择镜像的版本。若不指定，默认为 latest。本例中为73fbdd56。
    -   **请求最大并发数**：容器允许的最大请求并发数。默认0，表示不限制并发数。
    -   **最小保留实例数**：在无访问请求的情况下，最小保留的运行实例数。设置为0时，表示没有访问请求时，实例缩为 0。
    -   **最大扩容实例数**：允许扩容出来的最多实例个数。
    -   **资源限制**：可指定该应用所能使用的资源上限，包括 CPU 和 内存两种资源，防止占用过多资源。其中，CPU 资源的单位为 cores，即一个核；内存的单位为 Bytes，可以为 Mi 。
    -   **生命周期**：包含命令（Command） 和参数（Args）。
        -   如果均不配置，则使用镜像默认的命令和参数。
        -   如果仅配置参数，则使用镜像默认的命令及新配置的参数。
        -   如果均配置，则会覆盖镜像默认的配置。
    -   **环境变量**：支持通过键值对的形式配置环境变量。
5.  单击**创建**。 创建完成后，您可以在Knative 服务管理列表中，看到新创建的服务。

    ![新服务](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/1040500/156464507452562_zh-CN.png)


服务部署完成后，通过绑定Host域名与访问网关，然后可以直接访问服务URL。

1.  在 Kubernetes 菜单下，单击左侧导航栏的**Knative** \> **组件管理** ，进入组件管理页面。可以看到访问网关。

    ![查看网关](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/1040500/156464507452564_zh-CN.png)

2.  将访问网关地址与需要访问的域名进行Host绑定，在Hosts文件中添加绑定信息。

    ``` {#codeblock_26a_8jt_m55}
    网关+ 域名
    ```

    样例如下：

    ``` {#codeblock_x42_ys9_xvj}
    47.95.XX.XX test.default.example.com
    ```

3.  完成Host绑定后，可通过域名http://test.default.example.com直接对服务进行访问。

    ![访问服务](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/1040500/156464507452568_zh-CN.png)


