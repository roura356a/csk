# 升级安装Kubernetes集群的metrics-server组件 {#task_1664343 .task}

本文档介绍如何在不升级Kubernetes集群的情况下升级安装metrics-server组件。

-   您已成功创建一个Kubernetes集群。有关如何创建Kubernetes集群，参见[ZH-CN\_TP\_16639.md\#](cn.zh-CN/Kubernetes集群用户指南/集群管理/创建集群/创建Kubernetes 集群.md#)。
-   确保Kubernetes集群的初始版本是1.12.6及以前的版本。

Kubernetes集群组件的升级过程分为以下三个部分：切换数据采集组件、切换监控数据链路、变更组件兼容适配。

## 切换数据采集组件 {#section_be4_icm_j0b .section}

1.  创建并拷贝内容到metrics-server.yaml文件中，并执行`kubectl apply -f metrics-server.yaml`命令，将数据采集组件从Heapster切换到metrics-server。 

    ``` {#codeblock_s6c_vtk_7iu}
    apiVersion: v1
    kind: ServiceAccount
    metadata:
      name: admin
      namespace: kube-system
    ---
    apiVersion: rbac.authorization.k8s.io/v1beta1
    kind: ClusterRoleBinding
    metadata:
      name: admin
    roleRef:
      apiGroup: rbac.authorization.k8s.io
      kind: ClusterRole
      name: cluster-admin
    subjects:
    - kind: ServiceAccount
      name: admin
      namespace: kube-system
    ---
    apiVersion: v1
    kind: Service
    metadata:
      labels:
        task: monitoring
        # For use as a Cluster add-on (https://github.com/kubernetes/kubernetes/tree/master/cluster/addons)
        # If you are NOT using this as an addon, you should comment out this line.
        kubernetes.io/cluster-service: 'true'
        kubernetes.io/name: metrics-server
      name: heapster
      namespace: kube-system
    spec:
      ports:
      - port: 80
        targetPort: 8082
      selector:
        k8s-app: metrics-server
    ---
    apiVersion: v1
    kind: Service
    metadata:
      name: metrics-server
      namespace: kube-system
      labels:
        kubernetes.io/name: metrics-server
    spec:
      selector:
        k8s-app: metrics-server
      ports:
      - port: 443
        protocol: TCP
        targetPort: 443
    ---
    apiVersion: apiregistration.k8s.io/v1beta1
    kind: APIService
    metadata:
      name: v1beta1.metrics.k8s.io
    spec:
      service:
        name: metrics-server
        namespace: kube-system
      group: metrics.k8s.io
      version: v1beta1
      insecureSkipTLSVerify: true
      groupPriorityMinimum: 100
      versionPriority: 100
    ---
    apiVersion: extensions/v1beta1
    kind: Deployment
    metadata:
      name: metrics-server
      namespace: kube-system
      labels:
        k8s-app: metrics-server
    spec:
      selector:
        matchLabels:
          k8s-app: metrics-server
      template:
        metadata:
          name: metrics-server
          labels:
            k8s-app: metrics-server
        spec:
          serviceAccountName: admin
          containers:
          - name: metrics-server
            image: registry.##REGION##.aliyuncs.com/acs/metrics-server:v0.2.1-9dd9511-aliyun
            imagePullPolicy: Always
            command:
            - /metrics-server
            - '--source=kubernetes:https://kubernetes.default'
            - '--sink=socket:tcp://monitor.csk.##REGION##.aliyuncs.com:8093?clusterId=
    &public=true'
    ```

    **说明：** 您需要将`##REGION##`与`##CLUSTER_ID##`替换为所选集群的地域名称（例如，华东1：cn-hangzhou）与集群ID。


## 切换监控数据链路 {#section_siv_iop_kom .section}

1.  单击左侧导航栏中的**集群** \> **节点**，进入节点列表页面。
2.  选择目标**集群**。
3.  找到集群的三个master节点。单击master节点的实例ID，进入实例详情页面（本文档以master-01为例）。 

    ![切换链路1](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/129974/156568507539497_zh-CN.png)

4.  单击**远程连接**。进入 ECS 实例远程连接界面，根据页面指导，输入远程连接密码并单击**确定**。登录成功后，输入以下命令： 

    ``` {#codeblock_f62_mk6_3vn}
    sed -i 's/--horizontal-pod-autoscaler-use-rest-clients=false/--horizontal-pod-autoscaler-use-rest-clients=true/' /etc/kubernetes/manifests/kube-controller-manager.yaml                            
    ```

    ![切换链路2](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/129974/156568507539498_zh-CN.png)

5.  重复步骤[ZH-CN\_TP\_129974.md\#li\_03](cn.zh-CN/Kubernetes集群用户指南/集群管理/升级集群/升级安装Kubernetes集群的metrics-server组件.md#li_03)-[ZH-CN\_TP\_129974.md\#li\_04](cn.zh-CN/Kubernetes集群用户指南/集群管理/升级集群/升级安装Kubernetes集群的metrics-server组件.md#li_04)，在master-02和master-03节点上执行该命令。

执行完毕后，kube-controller-manager组件会被kubelet自动拉起更新。

## 组件兼容适配变更 {#section_1jj_wbe_2he .section}

1.  单击左侧导航栏中的**路由与负载均衡** \> **服务**，进入服务页面。
2.  选择目标**集群**和命名空间**kube-system**。单击服务**heapster**右侧的**查看YAML**。 

    ![组件兼容适配变更1](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/129974/156568507539499_zh-CN.png)

3.  在弹出的对话框中，修改selector中k8s-app的值为metrics-server。单击**更新**，完成修改。 

    ![组件兼容适配变更2](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/129974/156568507539500_zh-CN.png)

4.  单击左侧导航栏中的**应用** \> **无状态**，进入无状态页面。
5.  选择目标**集群**和**命名空间**kube-system。 

    ![组件兼容适配变更3](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/129974/156568507539506_zh-CN.png)

6.  选择Heapster相关组件（heapster和monitoring-influxdb），单击**更多** \> **删除**，在弹出的对话框中，单击**确定**，完成链路切换。 

    ![组件兼容适配变更4](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/129974/156568507639501_zh-CN.png)

    **说明：** 删除monitoring-influxdb 组件时，在弹出的删除monitoring-influxdb提示框中，勾选**移除关联的服务（Server）monitoring-influxdb**，单击**确定**。

7.  验证链路切换状态。 等待3分钟左右，数据链路会完成初始化。

    单击左侧导航栏中的**应用** \> **容器组**。在容器组页面。如果CPU（核）与内存（字节）显示正常，则表示链路切换成功。

    **说明：** 所有组件的CPU和内存值均为0则表示异常。

    ![组件兼容适配变更5](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/129974/156568507639502_zh-CN.png)


