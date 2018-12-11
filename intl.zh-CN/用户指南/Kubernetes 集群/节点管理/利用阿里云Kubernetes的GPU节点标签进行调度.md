# 利用阿里云Kubernetes的GPU节点标签进行调度 {#task_ryg_clw_pfb .task}

在使用Kubernetes集群实现GPU计算时，为了有效利用GPU设备，可根据需要将应用调度到具有GPU设备的节点上，为此，您可利用GPU节点标签进行灵活调度。

-   您已成功创建一个拥有GPU节点的Kubernetes集群，参见[Kubernetes GPU集群支持GPU调度](intl.zh-CN/用户指南/Kubernetes 集群/集群管理/Kubernetes GPU集群支持GPU调度.md#)。
-   您已连接到Master节点，方便快速查看节点标签等信息，参见[通过 kubectl 连接 Kubernetes 集群](intl.zh-CN/用户指南/Kubernetes 集群/集群管理/通过 kubectl 连接 Kubernetes 集群.md#)。

阿里云Kubernetes在部署Nvidia GPU节点的时候会发现GPU的属性，并且作为NodeLabel信息暴露给用户，拥有如下优势：

1.  可以快速筛选GPU节点
2.  部署时刻可以作为调度条件使用

1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。 
2.  在Kubernetes菜单下，单击左侧导航栏中的**集群** \> **节点**，查看该集群的节点。 

    **说明：** 本例中，该集群中拥有3个Worker节点，其中有两个Worker节点挂载了GPU设备，请查看节点IP，方便进行验证。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/40309/154451351821050_zh-CN.png)

3.  选择GPU节点，单击操作列的**更多** \> **详情**，进入Kubernetes Dashboard页面，查看GPU节点提供的节点标签。 

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/40309/154451351821059_zh-CN.png)

    您也可登录到Master节点，执行以下命令，查看GPU节点的标签。

    ```
    # kubectl get nodes
    NAME                                STATUS    ROLES     AGE       VERSION
    cn-beijing.i-2ze2dy2h9w97v65uuaft   Ready     master    2d        v1.11.2
    cn-beijing.i-2ze8o1a45qdv5q8a7luz   Ready     <none>    2d        v1.11.2             #可与控制台进行比对，确定GPU节点
    cn-beijing.i-2ze8o1a45qdv5q8a7lv0   Ready     <none>    2d        v1.11.2
    cn-beijing.i-2ze9xylyn11vop7g5bwe   Ready     master    2d        v1.11.2
    cn-beijing.i-2zed5sw8snjniq6mf5e5   Ready     master    2d        v1.11.2
    cn-beijing.i-2zej9s0zijykp9pwf7lu   Ready     <none>    2d        v1.11.2
    
    ```

    选择一个GPU节点，执行以下命令，查看该GPU节点的标签。

    ```
    # kubectl describe node cn-beijing.i-2ze8o1a45qdv5q8a7luz
    Name:               cn-beijing.i-2ze8o1a45qdv5q8a7luz
    Roles:              <none>
    Labels:             aliyun.accelerator/nvidia_count=1                          #注意
                        aliyun.accelerator/nvidia_mem=12209MiB
                        aliyun.accelerator/nvidia_name=Tesla-M40
                        beta.kubernetes.io/arch=amd64
                        beta.kubernetes.io/instance-type=ecs.gn4-c4g1.xlarge
                        beta.kubernetes.io/os=linux
                        failure-domain.beta.kubernetes.io/region=cn-beijing
                        failure-domain.beta.kubernetes.io/zone=cn-beijing-a
                        kubernetes.io/hostname=cn-beijing.i-2ze8o1a45qdv5q8a7luz
     ......
    ```

    本例中，该GPU节点包含如下3个节点标签（NodeLabel）。

    |key|value|
    |---|-----|
    |`aliyun.accelerator/nvidia_count`|GPU核心数量|
    |`aliyun.accelerator/nvidia_mem`|GPU显存，单位为MiB|
    |`aliyun.accelerator/nvidia_name`|nvida设备的GPU计算卡名称|

    同一类型的GPU云服务器的GPU计算卡名称相同，因此，您可通过该标签筛选节点。

    ```
    # kubectl get no -l aliyun.accelerator/nvidia_name=Tesla-M40
    NAME                                STATUS    ROLES     AGE       VERSION
    cn-beijing.i-2ze8o1a45qdv5q8a7luz   Ready     <none>    2d        v1.11.2
    cn-beijing.i-2ze8o1a45qdv5q8a7lv0   Ready     <none>    2d        v1.11.2
    
    ```

4.  返回容器服务控制台主页，单击左侧导航栏**应用** \> **部署**，单击右上角**使用模板创建**。 
    1.  创建一个tensorflow的Deployment，将该应用调度到GPU节点上。 

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/40309/154451351821074_zh-CN.png)

        本例的yaml编排如下所示。

        ```
        ---
        # Define the tensorflow deployment
        apiVersion: apps/v1
        kind: Deployment
        metadata:
          name: tf-notebook
          labels:
            app: tf-notebook
        spec:
          replicas: 1
          selector: # define how the deployment finds the pods it mangages
            matchLabels:
              app: tf-notebook
          template: # define the pods specifications
            metadata:
              labels:
                app: tf-notebook
            spec:
              nodeSelector:                                                  #注意
                aliyun.accelerator/nvidia_name: Tesla-M40
              containers:
              - name: tf-notebook
                image: tensorflow/tensorflow:1.4.1-gpu-py3
                resources:
                  limits:
                    nvidia.com/gpu: 1                                        #注意
                ports:
                - containerPort: 8888
                  hostPort: 8888
                env:
                  - name: PASSWORD
                    value: mypassw0rdv
        ```

    2.  您也可避免将某些应用部署到GPU节点。下面部署一个nginx的Pod，利用节点亲和性的特性进行调度，具体参见[使用镜像创建无状态Deployment应用](intl.zh-CN/用户指南/Kubernetes 集群/应用管理/使用镜像创建无状态Deployment应用.md#)中关于节点亲和性的说明。 

        该示例的yaml编排如下所示：

        ```
        apiVersion: v1
        kind: Pod
        metadata:
          name: not-in-gpu-node
        spec:
          affinity:
            nodeAffinity:
              requiredDuringSchedulingIgnoredDuringExecution:
                nodeSelectorTerms:
                - matchExpressions:
                  - key: aliyun.accelerator/nvidia_name
                    operator: DoesNotExist
          containers:
          - name: not-in-gpu-node
            image: nginx
        ```

5.  单击左侧导航栏**应用** \> **容器组**，选择所需的集群和命名空间，进入容器组列表。 

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/40309/154451351821094_zh-CN.png)


在容器组列表中，您可看到两个示例的Pod（容器组）成功调度到对应的节点上，从而实现基于GPU节点标签的灵活调度。

