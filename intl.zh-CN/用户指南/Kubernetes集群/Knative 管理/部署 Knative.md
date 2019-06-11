# 部署 Knative {#concept_525886 .concept}

## 前提条件 {#section_klu_a1g_mjo .section}

-   您已经成功创建一个Kubernetes 集群，且集群中Worker节点的数量大于等于3个。参见[创建Kubernetes集群](intl.zh-CN/用户指南/Kubernetes集群/集群管理/创建Kubernetes集群.md#)。
-   您已经成功部署 Istio，参见[部署Istio](intl.zh-CN/用户指南/Kubernetes集群/Istio管理/部署Istio.md#)。
-   仅支持 Kubernetes 版本 1.10 及以上的集群， 只支持标准 Kubernetes 托管版集群以及标准专有 Kubernetes 集群。

## 操作步骤 {#section_zag_dev_oq9 .section}

1.  登录[容器服务管理控制台](https://cs.console.aliyun.com/)。
2.  在 Kubernetes 菜单下，选择**Knative** \> **组件管理**，单击右上角的**一键部署**。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/423002/156024302448852_zh-CN.png)

3.  对模板进行相关配置，完成配置后单击**部署**。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/423002/156024302448853_zh-CN.png)

    -   **Bulid 组件**：提供从源码到镜像的通用构建能力。
    -   **Serving 组件**：管理 Serverless 工作负载，可以和事件很好的结合并且提供了基于请求驱动的自动扩缩的能力，而且在没有服务需要处理的时候可以缩容到零个实例。
    -   **Eventing 组件**：提供了事件的接入、触发等一整套事件管理的能力。

## 执行结果 {#section_tz4_npu_1cp .section}

部署完成后，可以看到部署结果信息。

-   您可以单击**进入组件管理**，查看组件信息。
-   您可以单击**进入应用管理**，查看应用相关操作。

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/423002/156024302548854_zh-CN.png)

