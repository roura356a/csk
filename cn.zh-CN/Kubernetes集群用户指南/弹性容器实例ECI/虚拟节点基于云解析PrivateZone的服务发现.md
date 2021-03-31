---
keyword: [虚拟节点, 云解析PrivateZone, 服务发现]
---

# 虚拟节点基于云解析PrivateZone的服务发现

阿里云Kubernetes集群的虚拟节点已经支持服务发现功能，目前支持Intranet service、Headless service、ClusterIP service。

-   需要先开通云解析PrivateZone，在[云解析DNS控制台](https://dns.console.aliyun.com/)中开通。
-   您在Kubernetes集群创建一个虚拟节点，请参见[通过部署ACK虚拟节点组件创建ECI Pod](/cn.zh-CN/Kubernetes集群用户指南/弹性容器实例ECI/通过部署ACK虚拟节点组件创建ECI Pod.md)。
-   您已成功连接到Kubernetes集群，请参见[通过kubectl连接Kubernetes集群](/cn.zh-CN/Kubernetes集群用户指南/集群/连接集群/通过kubectl连接Kubernetes集群.md)。

## 环境准备

通过Chart部署虚拟节点后，需要确认已正确配置相关环境变量。

1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。

2.  在控制台左侧导航栏中，选择**市场** \> **应用目录**。

3.  在**阿里云应用**页签中，选择**ack-virtual-node**。

4.  在Kubernetes菜单下，单击左侧导航栏中的**市场** \> **应用目录**， 在右侧选中**ack-virtual-node**。

5.  在应用目录 - ack-virtual-node中，单击**参数**，可以通过修改参数配置进行定制化，如下所示。

    ![应用目录](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/0563659951/p65159.png)

    -   `ECI_VPC`：Kubernetes集群使用的VPC ID，需要替换成您当前集群使用的VPC ID。
    -   `ALIYUN_CLUSTERID`：Kubernetes集群ID，需要替换成您当前使用集群的ID。不允许为空或者设置为default。

## 操作步骤

1.  部署Deployment和创建Service。

    样例模板如下所示，在YAML文件中复制如下YAML代码，然后执行`kubectl create -f nginx-service-ack.yaml`命令进行创建。

    ```
    apiVersion: v1
    kind: Service
    metadata:
      name: nginx-headless-service
      annotations:
         service.beta.kubernetes.io/alibaba-cloud-private-zone-enable: "true"
    spec:
      ports:
      - port: 80
        protocol: TCP
      selector:
        app: nginx
      clusterIP: None
    ---
    apiVersion: v1
    kind: Service
    metadata:
      name: nginx-clusterip-service
      annotations:
         service.beta.kubernetes.io/alibaba-cloud-private-zone-enable: "true"
    spec:
      ports:
      - port: 80
        protocol: TCP
      selector:
        app: nginx
      type: ClusterIP
    ---
    apiVersion: v1
    kind: Service
    metadata:
      name: nginx-intranet-service
      annotations:
        service.beta.kubernetes.io/alicloud-loadbalancer-address-type: intranet
        service.beta.kubernetes.io/alibaba-cloud-private-zone-enable: "true"
    spec:
      ports:
      - port: 80
        protocol: TCP
      selector:
        app: nginx
      type: LoadBalancer
    ---
    apiVersion: apps/v1
    kind: Deployment
    metadata:
      name: nginx-deployment
      labels:
        app: nginx
    spec:
      replicas: 3
      selector:
        matchLabels:
          app: nginx
      template:
        metadata:
          labels:
            app: nginx
        spec:
          containers:
          - name: nginx
            image:  nginx:alpine
            ports:
            - containerPort: 80
    ```

    **说明：** Kubernetes集群中的Service默认是不会同步到PrivateZone，需要给Service加上以下信息：

    ```
      annotations:
         service.beta.kubernetes.io/alibaba-cloud-private-zone-enable: "true"
    ```

    此时，虚拟节点Controller才会把Service的解析记录同步到PrivateZone中。

2.  登录[云解析DNS控制台](https://dns.console.aliyun.com/)。

3.  在左侧导航栏中单击**PrivateZone**，在**权威Zone**页签，可看到在Zone中自动生成IP解析记录。

    ![Zone列表](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/7694163161/p10241.png)

4.  在目标Zone右侧单击**解析设置**，弹出解析设置详情页面。

    ![解析设置](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/1563659951/p65338.png)

    **说明：** Zone里面的Record格式为`$svc.$ns`，对应相应的IP解析。解析规则如下：

    -   LoadBalancer service：PrivateZone中只对应一条解析Record，为SLB IP。
    -   ClusterIP service：PrivateZone中对应多条解析Record，分别为后端Pod的IP。
    -   Headless service：PrivateZone中对应多条解析Record，分别为后端Pod的IP。
    您可在该VPC网络环境中通过私有域名访问Service。

    -   长域名访问：`$svc.$ns.svc.cluster.local.$clusterId`，通过这种方式也可以访问其他集群中同步到PrivateZone的service。
    -   短域名访问：您可以通过`$svc`访问本Namespace下的Service，通过`$svc.$ns`访问其他Namespace中的Service。

