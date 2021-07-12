# 升级Kubernetes集群的metrics-server组件

本文档介绍如何在不升级Kubernetes集群的情况下升级安装metrics-server组件。

-   您已成功创建一个专有版Kubernetes集群。有关如何创建Kubernetes集群，参见[创建Kubernetes专有版集群](/cn.zh-CN/Kubernetes集群用户指南/集群/创建集群/创建Kubernetes专有版集群.md)。
-   确保Kubernetes集群的初始版本是1.12.6及以前的版本。

Kubernetes集群组件的升级过程分为以下三个部分：切换数据采集组件、切换监控数据链路、变更组件兼容适配。

## 切换数据采集组件

1.  创建并拷贝内容到metrics-server.yaml文件中，并执行`kubectl apply -f metrics-server.yaml`命令，将数据采集组件从Heapster切换到metrics-server。

    ```
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
    apiVersion: apps/v1
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
            - '--sink=socket:tcp://monitor.csk.##REGION##.aliyuncs.com:8093?clusterId=##CLUSTER_ID##
    &public=true'
    ```

    **说明：** 您需要将`##REGION##`与`##CLUSTER_ID##`替换为所选集群的地域名称（例如，华东1：cn-hangzhou）与集群ID。


## 切换监控数据链路

1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。

2.  在控制台左侧导航栏中，单击**集群**。

3.  在集群列表页面中，单击目标集群名称或者目标集群右侧**操作**列下的**详情**。

4.  找到集群的三个Master节点。单击Master节点的实例ID。

5.  在实例详情页面，单击**远程连接**。

    在ECS实例远程连接界面，根据页面指导，输入远程连接密码并单击**确定**。登录成功后，输入以下命令：

    ```
    sed -i 's/--horizontal-pod-autoscaler-use-rest-clients=false/--horizontal-pod-autoscaler-use-rest-clients=true/' /etc/kubernetes/manifests/kube-controller-manager.yaml                            
    ```

    ![切换链路2](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/9475659951/p39498.png)

6.  重复步骤5-6，在另外两个Master节点上执行该命令。


执行完毕后，kube-controller-manager组件会被kubelet自动拉起更新。

## 组件兼容适配变更

1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。

2.  在控制台左侧导航栏中，单击**集群**。

3.  在集群列表页面中，单击目标集群名称或者目标集群右侧**操作**列下的**详情**。

4.  在集群管理页左侧导航栏中，选择**网络** \> **服务**。

5.  选择目标命名空间**kube-system**，并单击服务**heapster**右侧**操作**列下的**查看YAML**。

6.  在弹出的对话框中，修改selector中k8s-app的值为metrics-server。单击**更新**。

    ![组件兼容适配变更2](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/9475659951/p39500.png)

7.  在集群管理页左侧导航栏中，选择**工作负载** \> **无状态**。

8.  在无状态页签，选择目标**命名空间**kube-system。

9.  选择Heapster相关组件（heapster和monitoring-influxdb），并单击右侧**操作**列下的**更多** \> **删除**。

10. 在弹出的对话框中，单击**确定**，完成链路切换。

    **说明：** 删除monitoring-influxdb组件时，在弹出的删除monitoring-influxdb提示框中，勾选**移除关联的服务（Server）monitoring-influxdb**，单击**确定**。

11. 验证链路切换状态。

    等待3分钟左右，数据链路会完成初始化。

    在集群管理页，单击**工作负载** \> **容器组**。在容器组页面，如果CPU（核）与内存（字节）显示正常，则表示链路切换成功。

    **说明：** 所有组件的CPU和内存值均为0则表示异常。

    ![组件兼容适配变更5](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/9475659951/p160951.png)


