# 集群管理FAQ {#task_1478634 .task}

本文主要为您介绍集群管理常见问题。

-   [集群创建失败](#section_2f5_2qz_j8h)
-   [删除 Kubernetes 集群失败：ROS stack 无法删除](#section_8vj_v2c_ij2)
-   [收集 Kubernetes 诊断信息](#section_5sg_5qx_xma)

## 集群创建失败 {#section_2f5_2qz_j8h .section}

1.  查看失败原因。 您可以通过查看集群的创建事件来查看集群创建失败的原因。
    1.  登录 [ROS 管理控制台](https://ros.console.aliyun.com/)。
    2.  选择集群所在的地域，选择所需的集群并单击右侧的**管理**，单击左侧导航栏中的**事件**，将鼠标移动到失败事件上查看具体的失败报错信息。 

        ![查看失败原因](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/15844/15656021529797_zh-CN.png)

    3.  您可以参考以下失败异常码及解决方法进行修复。 
        -   Resource CREATE failed: ResponseException: resources.k8s\_SNat\_Eip: Elastic IP address quota exceeded Code: QuotaExceeded.Eip

            解决方法：

            释放多余的 EIP 或者提交 VPC 工单提高 EIP 限额。

        -   Resource CREATE failed: ResponseException: resources.k8s\_master\_slb\_internet: The maximum number of SLB instances is exceeded. Code: ORDER.QUANTITY\_INVALID

            解决方法：

            释放多余的 SLB 实例或者提交 SLB 工单提高 SLB 限额。

        -   Resource CREATE failed: ResponseException: resources.k8s\_vpc: VPC quota exceeded. Code: QuotaExceeded.Vpc

            解决方法：

            释放多余的 VPC 或者提交 VPC 工单提高 VPC 限额。

        -   Status Code: 403 Code: InvalidResourceType.NotSupported Message: This resource type is not supported;

            解决方法：

            ECS 没有库存或者类型不支持，请选择其他 ECS 规格重试。

        -   Resource CREATE failed: ResponseException: resources.k8s\_master\_1: The specified image does not support cloud-init. Code: ImageNotSupportCloudInit

            解决方法：

            使用自定义镜像创建集群，自定义镜像必须是基于最新的 CentOS 镜像。

        -   Resource CREATE failed: ResponseException: resources.k8s\_nodes: The resource is out of stock in the specified zone. Please try other types, or choose other regions and zones. Code: OperationDenied.NoStock

            解决方法：

            当前所选实例规格已售罄，请选择其他可用区或实例规格。

        -   Resource CREATE failed: ResponseException: resources.k8s\_NAT\_Gateway: A route entry already exists, which CIDR is '0.0.0.0/0' Code: RouterEntryConflict.Duplicated

            解决方法：

            当前 VPC 路由表内已存在默认路由，请移除默认路由，或取消勾选为专有网络创建 SNAT后重试。

        -   Resource CREATE failed: ResponseException: resources.KubernetesWorkerRole: The number of role is limited to 200. Code: LimitExceeded.Role

            解决方法：

            RAM 角色数量已达到配额限制，请清理部分角色或提交 RAM 工单提高配额。

        -   Resource CREATE failed: ResponseException: resources.k8s\_NAT\_Gateway: The Account failed to create order. Code: OrderFailed

            解决方法：

            下单失败，请提交工单咨询。

        -   Resource CREATE failed: ResponseException: resources.k8s\_master\_1: This operation is forbidden by Aliyun RiskControl system. Code: Forbidden.RiskControl

            解决方法：

            您的账户出现异常，可参考[收到账号风险通知，怎么办？](https://www.alibabacloud.com/help/zh/faq-detail/69598.htm)

        -   Resource CREATE failed: WaitConditionFailure: resources.k8s\_node\_cloudinit\_wait\_cond: See output value for more details.

            解决方法：

            配置集群出错，请稍后重试或提交工单咨询。

        -   Resource CREATE failed: WaitConditionTimeout: resources.k8s\_master1\_cloudinit\_wait\_cond: 0 of 2 received:

            解决方法：

            配置集群出错，请稍后重试或提交工单咨询。

        -   Resource CREATE failed: ResponseException: resources.k8s\_master\_1: The request processing has failed due to some unknown error. Code: UnknownError

            解决方法：

            未知错误，请稍后重试或提交工单咨询。

        -   Resource CREATE failed: ResponseException: resources.k8s\_nodes: The request processing has failed due to some unknown error. Code: UnknownError

            解决方法：

            未知错误，请稍后重试或提交工单咨询。


## 删除 Kubernetes 集群失败：ROS stack 无法删除 {#section_8vj_v2c_ij2 .section}

用户在 ROS 创建的资源下手动添加了一些资源（比如在 ROS 创建的 VPC 下手动添加了一个 VSwitch），ROS 是没有权限删除这些资源的。这就会导致 ROS 删除 Kubernetes 资源时无法处理该 VPC，最终导致删除失败。

**说明：** 有关创建 Kubernetes 集群时 ROS 自动创建的资源，参见[../../../../dita-oss-bucket/SP\_235/DNcsk1877477/ZH-CN\_TP\_16639.md\#](../../../../intl.zh-CN/Kubernetes集群用户指南/集群管理/创建集群/创建Kubernetes 集群.md#)。

1.  集群删除失败时（集群的状态显示**删除失败**），跳转到 [ROS 管理控制台](https://ros.console.aliyun.com/)。 

    ![失败报错](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/15845/15656021529798_zh-CN.png)

2.  选择集群所在的地域，找到集群对应的资源栈 `k8s-for-cs-{cluster-id}`，可以看到其状态为**删除失败**。 

    ![失败显示](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/15845/15656021539799_zh-CN.png)

3.  单击资源栈的名称进入资源栈详情页面，单击左侧导航栏中的**资源**。 您可以看到哪些资源删除失败了。本示例中负载均衡下的 VSwitch 删除失败。

    ![失败定位](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/15845/15656021539800_zh-CN.png)

4.  进入删除失败的资源所在产品的控制台，并找到该资源。 本示例中，登录 VPC 管理控制台，找到集群所在的 VPC，并在该 VPC 下找到删除失败的 VSwitch。

    ![vswitch](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/15845/15656021539801_zh-CN.png)

5.  单击 VSwitch 右侧的**删除** 尝试手动删除。 本示例中，由于 VSwitch 下还有资源未释放，所以删除失败。

    ![错误提示](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/15845/15656021539802_zh-CN.png)

    手动释放该 VSwitch 下的资源，然后再次尝试删除该 VSwitch。

6.  使用类似的方法手动删除 Kubernetes 集群下所有删除失败的资源，然后再次尝试删除 Kubernetes 集群。

## 收集 Kubernetes 诊断信息 {#section_5sg_5qx_xma .section}

当Kubernetes集群出现问题，或者节点异常时，您需要收集Kubernetes诊断信息。

**说明：** 

-   当集群异常时，需要在master节点完成收集。
-   当worker节点异常时，则需要在master节点和异常的worker节点上完成收集。

完成以下步骤在master/worker节点收集诊断信息。

1.  在 master/worker节点下载诊断脚本，并增加运行权限。 

    ``` {#codeblock_xfv_3b1_7jc}
    curl -o /usr/local/bin/diagnose_k8s.sh http://aliacs-k8s-cn-hangzhou.oss-cn-hangzhou.aliyuncs.com/public/diagnose/diagnose_k8s.sh
    chmod u+x /usr/local/bin/diagnose_k8s.sh
    ```

2.  执行诊断脚本。 

    ``` {#codeblock_np7_zok_raw}
    diagnose_k8s.sh
     ......
     + echo 'please get diagnose_1514939155.tar.gz for diagnostics'   ##每次执行诊断脚本，产生的日志文件的名称不同
     please get diagnose_1514939155.tar.gz for diagnostics
     + echo '请上传 diagnose_1514939155.tar.gz'
     请上传 diagnose_1514939155.tar.gz
    ```

3.  列出并上传产生的日志文件。 

    ``` {#codeblock_kf0_ddg_ttf}
    cd /usr/local/bin
    ls -ltr|grep diagnose_1514939155.tar.gz           ##注意替换为生成的日志文件名
    ```


