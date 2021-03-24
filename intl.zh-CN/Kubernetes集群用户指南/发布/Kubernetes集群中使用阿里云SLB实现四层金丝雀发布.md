# Kubernetes集群中使用阿里云SLB实现四层金丝雀发布

在Kubernetes集群中，对于通过TCP/UDP访问的服务来说，七层的Ingress不能很好地实现灰度发布的需求。本文介绍如何使用SLB来进行四层的金丝雀发布。

## 前提条件

-   [创建Kubernetes托管版集群](/intl.zh-CN/Kubernetes集群用户指南/集群/创建集群/创建Kubernetes托管版集群.md)
-   [通过SSH访问Kubernetes集群](/intl.zh-CN/Kubernetes集群用户指南/集群/连接集群/通过SSH访问Kubernetes集群.md)

## 步骤1 部署旧版本服务

1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。

2.  在控制台左侧导航栏中，单击**集群**。

3.  在集群列表页面中，单击目标集群名称或者目标集群右侧**操作**列下的**应用管理**，选择**无状态**页签。

4.  在**无状态**页签，单击右上角的**使用模板创建。**

5.  选择**示例模板**，然后单击**创建**。

    ![示例模板](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/1395659951/p148094.png)

    本例是一个Nginx的编排，通过SLB对外暴露的服务。

    ```
     apiVersion: extensions/v1beta1
     kind: Deployment
     metadata:
       labels:
         run: old-nginx
       name: old-nginx
     spec:
       replicas: 1
       selector:
         matchLabels:
           run: old-nginx
       template:
         metadata:
           labels:
             run: old-nginx
             app: nginx
         spec:
           containers:
           - image: registry.cn-hangzhou.aliyuncs.com/xianlu/old-nginx
             imagePullPolicy: Always
             name: old-nginx
             ports:
             - containerPort: 80
               protocol: TCP
           restartPolicy: Always
     ---
     apiVersion: v1
     kind: Service
     metadata:
       labels:
         run: nginx
       name: nginx
     spec:
       ports:
       - port: 80
         protocol: TCP
         targetPort: 80
       selector:
         app: nginx
       sessionAffinity: None
       type: LoadBalancer                       ##通过阿里云SLB对外暴露服务
    ```

6.  单击**无状态**页签，可以看到创建的应用。

    ![无状态应用列表](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/2395659951/p148100.png)

    您也可以在集群管理左侧导航栏中，单击**服务**进行查看。

7.  在**服务**页面，您可单击服务右侧的外部端点，进入Nginx默认欢迎页面，本示例中的Nginx欢迎页面会显示**old**，表示当前访问的服务对应后端old-nginx容器。

    ![old](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/2395659951/p9939.png)

    为了方便展示多次发布的情况，建议登录Master节点，后面都将通过`curl`命令查看部署的效果。

    ```
    bash  
    for x in {1..10} ; do curl EXTERNAL-IP; done                    ##EXTERNAL-IP即是服务的外部端点
    old
    old
    old
    old
    old
    old
    old
    old
    old
    old
    ```


## 步骤2 上线新版本Deployment

1.  在控制台左侧导航栏中，单击**集群**。

2.  在集群列表页面中，单击目标集群名称或者目标集群右侧**操作**列下的**应用管理**，选择**无状态**页签。

3.  在**无状态**页签，单击右上角的**使用模板创建**

4.  选择**示例模板**，然后单击**创建**。

    本示例是一个Nginx的新版本的Deployment，其中的labels，也是含有`app:nginx`标签。这个标签就是为了使用与旧版本Deployment相同的Nginx服务，这样就可以将对应的流量导入进来。

    本示例的编排模板如下。

    ```
     apiVersion: extensions/v1beta1
     kind: Deployment
     metadata:
       labels:
         run: new-nginx
       name: new-nginx
     spec:
       replicas: 1
       selector:
         matchLabels:
           run: new-nginx
       template:
         metadata:
           labels:
             run: new-nginx
             app: nginx
         spec:
           containers:
           - image: registry.cn-hangzhou.aliyuncs.com/xianlu/new-nginx
             imagePullPolicy: Always
             name: new-nginx
             ports:
             - containerPort: 80
               protocol: TCP
           restartPolicy: Always
    ```

5.  单击**无状态**页签，您可看到名为new-nginx的Deployment。

    ![部署列表](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/2395659951/p9941.png)

6.  登录Master节点，执行curl命名，查看服务访问的情况。

    ```
    bash  
    for x in {1..10} ; do curl EXTERNAL-IP; done                    ##EXTERNAL-IP即是服务的外部端点
    new
    new
    new
    old
    new
    old
    new
    new
    old
    old
    ```

    可看到五次访问到旧的服务，五次访问到新的服务。主要原因是Service对于流量请求是平均的负载均衡策略，而且新老Deployment均为一个Pod，因此它们的流量百分比为1：1。


## 步骤3 调整流量权重

基于SLB的金丝雀发布需要通过调整后端的pod数量来调整对应的权重。例如我们这里希望新的服务权重更大一些，假如将新的Pod数量调整到4个。

**说明：** 新旧版本应用同时存在时，某个样本的curl命令返回的结果并非严格按照设置的权重，本例中执行10次curl命令，您可通过观察更大的样本获取接近的效果。

1.  在控制台左侧导航栏中，单击**集群**。

2.  在集群列表页面中，单击目标集群名称或者目标集群右侧**操作**列下的**应用管理**，选择**无状态**页签。

3.  选择命名空间，选择所需的Deployment，单击右侧的**伸缩**。

    ![部署列表](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/2395659951/p9942.png)

4.  在弹出的对话框中，设置所需容器组的数量，将其调整为4。

    **说明：** Kubernetes的Deployment资源默认的更新方式就是rollingUpdate，所以在更新过程中，会保证最小可服务的容器个数，该个数也可以在模板里面调整。

    ![模板](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/2395659951/p9943.png)

5.  待部署完成后，登录Master节点，执行curl命名，查看效果。

    ```
    bash  
     for x in {1..10} ; do curl EXTERNAL-IP; done                    ##EXTERNAL-IP即是服务的外部端点
     new
     new
     new
     new
     new
     old
     new
     new
     new
     old
    ```

    可以看到，10个请求里面，有8个请求到新的服务，2个到老的服务。

    您可通过动态地调整Pod的数量来调整新老服务的权重，实现金丝雀发布。


