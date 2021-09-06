---
keyword: [容器水平伸缩, HPA, 弹性伸缩]
---

# 容器水平伸缩（HPA）

ACK支持在控制台界面上快速创建支持HPA的应用，实现容器资源的弹性伸缩。您也可通过定义HPA（Horizontal Pod Autoscaling）的YAML来进行配置。

## 体验实验室

为了帮助您快速上手本教程，ACK为您提供了可以动手操作的交互式实验环境。如您需要体验，请访问[通过HPA进行Pod水平弹性伸缩](https://start.aliyun.com/handson/Tn0HcdCZ/how_to_use_hpa)。

## 通过容器服务控制台创建HPA应用

阿里云容器服务已经集成了HPA，您可以简便地通过容器服务控制台进行创建。您可以在创建应用的时候创建HPA，也可以在已有应用的基础上开启HPA。

**方式一：在创建应用过程中，开启HPA**

1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。

2.  在控制台左侧导航栏中，单击**集群**。

3.  在集群列表页面中，单击目标集群名称或者目标集群右侧**操作**列下的**详情**。

4.  在集群管理页左侧导航栏中，选择**工作负载** \> **无状态**。

5.  在无状态页面单击**使用镜像创建**。

6.  在应用基本信息页填写应用的名称等信息，然后单击**下一步**。

    |配置项|描述|
    |---|--|
    |应用名称|设置应用的名称。|
    |副本数量|即应用包含的Pod数量，默认数量为2。|
    |类型|定义资源对象的类型，可选择**无状态**、**有状态**、**任务**、**定时任务**、**守护进程集**。|
    |标签|为该应用添加一个标签，标识该应用。|
    |注解|为该应用添加一个注解（annotation）。|
    |时区同步|容器与节点是否使用相同的时区。|

7.  在容器配置页进行容器设置，选择镜像，并设置所需的资源。然后单击**下一步**。详情请参见[配置容器](/cn.zh-CN/Kubernetes集群用户指南/应用/工作负载/创建无状态工作负载Deployment.md)。

    **说明：** 您必须为Deployment设置所需资源，否则无法进行容器自动伸缩。

8.  在高级设置页的访问设置区域，单击服务的**创建**，设置服务的相关信息，详情请参见[高级设置](/cn.zh-CN/Kubernetes集群用户指南/应用/工作负载/创建无状态工作负载Deployment.md)。

9.  在高级设置页选中**指标伸缩**的**开启**，设置伸缩的条件和配置。

    -   **指标**：支持CPU和内存，需要和设置的所需资源类型相同。
    -   **触发条件**：资源使用率的百分比，超过该使用量，容器开始扩容。有关Pod水平自动扩缩的算法，请参见[算法细节](https://kubernetes.io/zh/docs/tasks/run-application/horizontal-pod-autoscale/#algorithm-details)。
    -   **最大副本数**：该Deployment可扩容的容器数量上限。
    -   **最小副本数**：该Deployment可缩容的容器数量下限。
10. 单击右下角的**创建**，一个支持HPA的Deployment就已经创建完毕。


**结果验证**

1.  单击**查看应用详情**或者在**工作负载** \> **无状态**中单击**应用名称**或**操作**列的**详情**，单击**容器伸缩**，您可在部署的详情中查看伸缩组信息。

2.  在实际使用环境中，应用会根据CPU负载进行伸缩。您也可在测试环境中验证弹性伸缩，通过给Pod进行CPU压测，可以发现Pod在半分钟内即可完成水平的扩展。

**方式二：为已有应用开启HPA**

以下以无状态应用为例，演示为已有应用开启HPA。

1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。

2.  在控制台左侧导航栏中，单击**集群**。

3.  在集群列表页面中，单击目标集群名称或者目标集群右侧**操作**列下的**详情**。

4.  在集群管理页左侧导航栏中，选择**工作负载** \> **无状态**。

5.  在**无状态**页面中单击目标应用名称。

6.  单击**容器伸缩**页签，然后单击**创建**。

7.  在**创建**对话框中，设置伸缩的配置。有关伸缩的参数配置，请参见上文步骤9的[水平伸缩配置](#step_wto_syh_7s4)。

8.  单击**确定**。


## 通过kubectl命令创建HPA应用

您也可通过编排模板来手动创建HPA，并将其绑定到要伸缩的Deployment对象上，通过kubectl命令实现容器自动伸缩配置。

下面针对一个Nginx应用进行举例。

1.  创建并复制以下内容到nginx.yml中。

    Deployment的编排模板如下。

    ```
    apiVersion: apps/v1 
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
              requests:                         ##必须设置，不然HPA无法运行。
                cpu: 500m
    ```

2.  执行以下命令，创建Nginx应用。

    ```
    kubectl create -f nginx.yml
    ```

3.  创建HPA。

    通过scaleTargetRef设置当前HPA绑定的对象，在本例中绑定名叫nginx的Deployment。

    ```
    apiVersion: autoscaling/v2beta2
    kind: HorizontalPodAutoscaler
    metadata:
      name: nginx-hpa
    spec:
      scaleTargetRef:
        apiVersion: apps/v1
        kind: Deployment
        name: nginx
      minReplicas: 1
      maxReplicas: 10
      metrics:
      - type: Resource
        resource:
          name: cpu
          target:
            type: Utilization
            averageUtilization: 50
                            
    ```

    **说明：** HPA需要给Pod设置request资源，如果没有request资源，HPA不会运行。

4.  执行`kubectl describe hpa name`会发现有类似以下的警告信息。

    ```
    Warning  FailedGetResourceMetric       2m (x6 over 4m)  horizontal-pod-autoscaler  missing request for cpu on container nginx in pod default/nginx-deployment-basic-75675f5897-mqzs7
    
    Warning  FailedComputeMetricsReplicas  2m (x6 over 4m)  horizontal-pod-autoscaler  failed to get cpu utilization: missing request for cpu on container nginx in pod default/nginx-deployment-basic-75675f5
    ```

5.  创建好HPA后，再次执行`kubectl describe hpa name`命令。

    可以看到以下信息，则表示HPA已经正常运行。

    ```
    Normal SuccessfulRescale 39s horizontal-pod-autoscaler New size: 1; reason: All metrics below target
    ```

    此时当Nginx的Pod的利用率超过本例中设置的50%利用率时，则会进行水平扩容，低于50%的时候会进行缩容。


