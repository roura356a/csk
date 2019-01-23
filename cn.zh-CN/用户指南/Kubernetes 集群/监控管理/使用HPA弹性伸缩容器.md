# 使用HPA弹性伸缩容器 {#concept_i2v_2h4_k2b .concept}

阿里云容器服务支持在控制台界面上快速创建支持HPA的应用，实现容器资源的弹性伸缩。您也可通过定义HPA\(Horizontal Pod Autoscaling\)的yaml配置来进行配置。

## 前提条件 {#section_dxl_kj4_k2b .section}

-   您已成功创建一个Kubernetes集群，参见[创建Kubernetes集群](cn.zh-CN/用户指南/Kubernetes 集群/集群管理/创建Kubernetes集群.md#)。
-   您已成功连接到Kubernetes集群的Master节点。

## 方法1 通过容器服务控制台创建HPA应用 {#section_alx_3h4_k2b .section}

在阿里云容器服务中，已经集成了HPA，开发者可以非常简单地通过容器服务控制台进行创建。

1.  登录 [容器服务管理控制台](https://cs.console.aliyun.com)。
2.  在Kubernetes菜单下，单击左侧导航栏中的**应用** \> **部署**，单击右上角的**使用镜像创建**。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16700/154821671310577_zh-CN.png)

3.  填写应用的名称，设置应用部署集群和命名空间，单击**下一步**。
4.  首先进行应用设置，设置副本数量，然后勾选**开启**自动伸缩，设置伸缩的条件和配置。

    -   **指标**：支持CPU和内存，需要和设置的所需资源类型相同。
    -   **触发条件**：资源使用率的百分比，超过该使用量，容器开始扩容。
    -   **最大容器数量**：该Deployment可扩容的容器数量上限。
    -   **最小容器数量**：该Deployment可缩容的容器数量下限。
    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16700/154821671310578_zh-CN.png)

5.  进行容器设置，选择镜像，并设置所需的资源。然后单击**下一步**

    **说明：** 您必须为Deployment设置所需资源，否则无法进行容器自动伸缩。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16700/154821671310579_zh-CN.png)

6.  进入访问设置页面，本例中不进行访问设置，单击**创建**。

    此时一个支持HPA的Deployment就已经创建完毕，您可在部署的详情中查看伸缩组信息。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16700/154821671310580_zh-CN.png)

7.  在实际使用环境中，应用会根据CPU负载进行伸缩。您也可在测试环境中验证弹性伸缩，通过给Pod进行CPU压测，可以发现Pod在半分钟内即可完成水平的扩展。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16700/154821671310581_zh-CN.png)


## 方法2 通过kubectl命令进行使用 {#section_ykx_3h4_k2b .section}

您也可通过编排模板来手动创建HPA，并将其绑定到要伸缩的Deployment对象上，通过kubectl命令实现容器自动伸缩配置。

下面针对一个Nginx应用进行举例，Deployment的编排模板如下，执行kubectl create -f xxx.yml命令进行创建。

```
apiVersion: apps/v1beta2 # for versions before 1.8.0 use apps/v1beta1
kind: Deployment
metadata:
  name: nginx
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
        image: nginx:1.7.9 # replace it with your exactly <image_name:tags>
        ports:
        - containerPort: 80
        resources:
          requests:                         ##必须设置，不然HPA无法运行
            cpu: 500m
```

然后创建HPA，通过scaleTargetRef设置当前HPA绑定的对象，在本例中绑定是名叫nginx的Deployment。

```
apiVersion: autoscaling/v2beta1
kind: HorizontalPodAutoscaler
metadata:
  name: nginx-hpa
  namespace: default
spec:
  scaleTargetRef:                             ##绑定名为nginx的Deployment
    apiVersion: apps/v1beta2
    kind: Deployment
    name: nginx
  minReplicas: 1
  maxReplicas: 10
  metrics:
  - type: Resource
    resource:
      name: cpu
      targetAverageUtilization: 50
```

**说明：** HPA需要给Pod设置request资源，如果没有request资源，HPA不会运行。

执行kubectl describe hpa \[name\]会发现有类似如下的warnning。

```
Warning  FailedGetResourceMetric       2m (x6 over 4m)  horizontal-pod-autoscaler  missing request for cpu on container nginx in pod default/nginx-deployment-basic-75675f5897-mqzs7

Warning  FailedComputeMetricsReplicas  2m (x6 over 4m)  horizontal-pod-autoscaler  failed to get cpu utilization: missing request for cpu on container nginx in pod default/nginx-deployment-basic-75675f5
```

创建好HPA后，再次执行kubectl describe hpa \[name\]命令，可以看到如下信息，则表示HPA已经正常运行。

```
Normal SuccessfulRescale 39s horizontal-pod-autoscaler New size: 1; reason: All metrics below target
```

此时当Nginx的Pod的利用率超过本例中设置的50%利用率时，则会进行水平扩容，低于50%的时候会进行缩容。

