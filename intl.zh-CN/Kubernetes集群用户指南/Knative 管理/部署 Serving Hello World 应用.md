# 部署 Serving Hello World 应用 {#task_1305477 .task}

本文以 Hello World 示例为您介绍 Knative 。

-   您已经成功创建一个 Kubernetes 集群，参见[创建Kubernetes集群](intl.zh-CN//创建Kubernetes 集群.md#)。
-   您已经成功部署 Knative，参见 [部署 Knative](intl.zh-CN/Kubernetes集群用户指南/Knative 管理/部署 Knative.md#)。
-   您已经成功部署 Serving 组件，参见[部署组件](intl.zh-CN/Kubernetes集群用户指南/Knative 管理/部署组件.md#)。

1.  登录[容器服务管理控制台](https://cs.console.aliyun.com/)。
2.  在 Kubernetes 菜单下，选择**Knative** \> **服务管理**，进入服务管理页面。
3.  单击右上角的**创建服务**。![创建服务](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/1040500/156464520552550_zh-CN.png)


4.  设置集群名称、命名空间、服务名称，选择所要使用的镜像和镜像版本等配置信息。 

    ![创建服务](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/1068378/156464520552892_zh-CN.png)

    **说明：** 

    本示例中，界面参数取值参考如下：

    -   **服务名称**：自定义该服务的名称。本例为helloworld-go。
    -   **镜像名称**：您可以单击选择镜像，在弹出的对话框中选择所需的镜像并单击**确定**。您还可以填写私有 registry。填写的格式为domainname/namespace/imagename:tag。本例中为 registry.cn-hangzhou.aliyuncs.com/knative-sample/helloworld-go。
    -   **镜像版本**：您可以单击选择镜像版本选择镜像的版本。若不指定，默认为 latest。本例中为73fbdd56。
    -   **环境变量**：支持通过键值对的形式配置环境变量。本例中，`TARGET=Knative`。
    界面其他参数详细信息请参见[参数说明](intl.zh-CN/Kubernetes集群用户指南/Knative 管理/创建 Knative 服务.md#step_bw0_9pr_p44)。

5.  单击**创建**。 创建完成后，您可以在Knative 服务管理列表中，看到新创建的服务。

    ![创建服务](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/1068378/156464520552893_zh-CN.png)


服务部署完成后，通过绑定Host域名与访问网关，然后可以直接访问服务URL。

1.  在 Kubernetes 菜单下，单击左侧导航栏的**Knative** \> **组件管理** ，进入组件管理页面。可以看到访问网关。

    ![查看网关](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/1040500/156464520652564_zh-CN.png)

2.  将访问网关地址与需要访问的域名进行Host绑定，在Hosts文件中添加绑定信息。

    ``` {#codeblock_moj_j2x_k2h}
    网关+ 域名
    ```

    样例如下：

    ``` {#codeblock_kbo_6ju_7kp}
    47.95.XX.XX helloworld-go.default.example.com
    ```

3.  完成Host绑定后，可通过域名http://helloworld-go.default.example.com直接对服务进行访问。

    ![访问服务](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/1040500/156464520652568_zh-CN.png)


