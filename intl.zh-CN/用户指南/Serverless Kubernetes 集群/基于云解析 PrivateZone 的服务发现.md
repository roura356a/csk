# 基于云解析 PrivateZone 的服务发现 {#concept_efk_nsq_b2b .concept}

阿里云Serverless Kubernetes已经支持服务发现功能，目前支持内网SLB和Headless Service的服务发现。

## 关于云解析PrivateZone {#section_q15_qsq_b2b .section}

云解析 PrivateZone，是基于阿里云专有网络VPC（Virtual Private Cloud）环境的私有域名解析和管理服务。您能够在自定义的一个或多个专有网络中将私有域名映射到IP资源地址，同时在其他网络环境无法访问您的私有域名。

## 前提条件 {#section_cd5_h5q_b2b .section}

1.  需要先开通云解析 PrivateZone，在云解析DNS控制台中开通。
2.  您已成功创建一个 Serverless Kubernetes 集群，参见[创建 Serverless Kubernetes 集群](intl.zh-CN/用户指南/Serverless Kubernetes 集群/集群管理/创建 Serverless Kubernetes 集群.md#)。
3.  您已成功连接到 Kubernetes 集群，参见[通过 kubectl 连接 Kubernetes 集群](intl.zh-CN/用户指南/Serverless Kubernetes 集群/集群管理/通过 kubectl 连接 Kubernetes 集群.md#)。

## 操作步骤 {#section_u15_qsq_b2b .section}

1.  通过 kubectl 连接到 Kubernetes 集群，执行如下命令，确认连接的集群。

    ```
    kubectl cluster-info
     Kubernetes master is running at https://xxxxxx.serverless-1.kubernetes.cn-shanghai.aliyuncs.com:6443
    ```

2.  部署 deployment 和创建 Service。目前仅支持 Intranet Service 和 Headless Service。

    以 Intranet Service 为例。创建示例nginx-deployment-basic.yaml文件。

    ```
    vim nginx-deployment-basic.yaml
    ```

    样例模板如下所示，在yaml文件中复制如下yaml代码，然后执行 `kubectl create -f nginx-deployment-basic.yaml`命令进行创建。

    ```
    apiVersion: apps/v1beta2 # for versions before 1.8.0 use apps/v1beta1
     kind: Deployment
     metadata:
       name: nginx-deployment-basic
       labels:
         app: nginx
     spec:
       replicas: 2
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
             image: nginx:1.7.9               # replace it with your exactly <image_name:tags>
             ports:
             - containerPort: 80
     ---
     apiVersion: v1
     kind: Service
     metadata:
       name: nginx-service-intranet           #可通过服务名作为短域名进行访问
       annotations:                           ##添加注解
         service.beta.kubernetes.io/alicloud-loadbalancer-address-type: intranet
     spec:
       ports:
       - port: 80
         protocol: TCP
       selector:
         app: nginx
       type: LoadBalancer
    ```

    您也可创建 Headless Service 类型的服务，示例模板如下。

    ```
    apiVersion: v1
    kind: Service
    metadata:
      name: nginx-service-headless
    spec:
      ports:
      - port: 80
        protocol: TCP
      selector:
        app: nginx
      clusterIP: None
    ```

3.  执行以下命令，查看应用的运行状况。

    ```
    kubectl get svc,pod,deployment
    ```

4.  登录 [云解析DNS控制台](https://dns.console.aliyun.com/)。
5.  在左侧导航栏中单击**PrivateZone** \> **Zone 列表**，可看到该列表下自动生成一条记录。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16507/153510504310241_zh-CN.png)

    您可在该 VPC 网络环境中通过私有域名访问Service（长域名或者短域名）。

    -   长域名访问：本例中是 `nginx-service-intranet.$NAMESPACE.svc.cluster.local`，其中$NAMESPACE为Serverless集群 ID，在控制台上可以看到，也可以在pod的yaml文件中通过环境变量得到。

        ```
        env:
               - name: NAMESPACE
                 valueFrom:
                   fieldRef:
                     fieldPath: metadata.namespace
        ```

    -   短域名访问：nginx-service-intranet 或 nginx-service-headless，即在yaml编排中定义的服务名。

更多信息可参见[serverless-k8s-examples](https://github.com/AliyunContainerService/serverless-k8s-examples)

