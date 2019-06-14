# 为容器（Pod）分配弹性网卡（ENI） {#task_fp2_w3v_vfb .task}

本文介绍如何将ENI网卡分配给Pod。

-   创建Kubernetes集群时，**网络插件**请选择**Terway**，可参考[创建Kubernetes集群](intl.zh-CN/用户指南/Kubernetes集群/集群管理/创建Kubernetes集群.md#)。
-   对于已经选择的Terway网络插件创建的集群，请升级Terway版本至v1.0.0.1及以上版本。

    **说明：** 

    1.  登录容器服务管理控制台，在 Kubernetes 菜单下，选择**集群**，进入集群列表页面。
    2.  选择目标集群，单击操作列**更多** \> **系统组件升级**。
    3.  在系统组件升级页面，查看Terway当前版本。
    4.  根据**当前版本**及**可升级版本**，判断是否需要升级。如需升级，请单击操作列的**升级**。

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/64468/156047905832549_zh-CN.png)


1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。
2.  在 Kubernetes 菜单下，单击左侧导航栏中的**应用** \> **无状态**，进入无状态（Deployment）页面。
3.  单击页面右上角的**使用模板创建**。 

    您可以使用如下 yaml 示例模板创建Pod。

    ```
    apiVersion: v1
    kind: Pod
    metadata:
      name: terway-pod
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx
        ports:
        - containerPort: 80
        resources:
          limits:
            aliyun/eni: 1
    ```


1.  在 Kubernetes 菜单下，单击左侧导航栏中的**应用** \> **容器组**，可以看到名称为**terway-pod**的pod已部署成功。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/64468/156047905832570_zh-CN.png)

2.  在 Kubernetes 菜单下，单击左侧导航栏中的**集群**，进入进群列表页面。
3.  选择目标集群，单击集群名称，查看集群详细信息。
4.  在集群资源区域，单击**虚拟专有网络 VPC**查看集群的VPC网段。
5.  执行以下命令，获取已部署Pod的IP地址，且此IP地址在集群VPC网段内。

    ```
    $ kubectl get pod -o wide
    ```


