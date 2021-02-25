# Serverless集群基于云解析PrivateZone的服务发现

阿里云Serverless Kubernetes已经支持服务发现功能，目前支持Intranet service、Headless service、ClusterIP service。

-   需要先开通云解析PrivateZone，在云解析DNS控制台中开通。
-   [创建Serverless Kubernetes集群](/intl.zh-CN/Serverless Kubernetes集群用户指南/快速入门/创建Serverless Kubernetes集群.md)。
-   您已成功连接到Kubernetes集群，参见[通过kubectl连接Kubernetes集群](/intl.zh-CN/Serverless Kubernetes集群用户指南/集群管理/管理和访问集群/通过kubectl连接Kubernetes集群.md)。

云解析PrivateZone，是基于阿里云专有网络VPC（Virtual Private Cloud）环境的私有域名解析和管理服务。您能够在自定义的一个或多个专有网络中将私有域名映射到IP资源地址，同时在其他网络环境无法访问您的私有域名。

1.  部署Deployment和创建Service。

    样例模板如下所示，在YMAL文件中复制如下YAML代码，然后执行`kubectl create -f nginx-service.yaml`命令进行创建。

    ```
    apiVersion: v1
    kind: Service
    metadata:
      name: nginx-headless-service
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

2.  执行以下命令，查看应用的运行状况。

    ```
    kubectl get svc,pod,deployment
    ```

3.  登录[云解析DNS控制台](https://dns.console.aliyun.com/)。

4.  在左侧导航栏中，单击**PrivateZone**进入**全部Zone**页签。

5.  选中目标Zone，单击**解析设置**，弹出解析设置详情页面。

    **说明：** Zone里面的Record格式为`$svc.$ns`，对应相应的IP解析。解析规则如下：

    -   LoadBalancer service：PrivateZone中只对应一条解析Record，为SLB IP。
    -   ClusterIP service：PrivateZone中只对应一条解析Record，为Cluster IP。
    -   Headless service：PrivateZone中对应多条解析Record，分别为后端Pod的IP。
    您可在该VPC网络环境中通过私有域名访问Service。

    -   长域名访问：`$svc.$ns.svc.cluster.local.$clusterId`，通过这种方式也可以访问其他集群中同步到PrivateZone的Service。
    -   短域名访问：您可以通过`$svc`访问本Namespace下的Service，通过`$svc.$ns`访问其他Namespace中的Service。
    更多信息请参见[serverless-k8s-examples](https://github.com/AliyunContainerService/serverless-k8s-examples)。


