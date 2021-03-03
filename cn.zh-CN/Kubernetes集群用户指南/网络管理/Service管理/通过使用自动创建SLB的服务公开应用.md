---
keyword: [自动创建SLB, 创建服务, 负载均衡型服务, loadbalancer service, 公开应用]
---

# 通过使用自动创建SLB的服务公开应用

当您没有可用的SLB时，Cloud Controller Manager（CCM）组件可以为LoadBalancer类型服务自动创建SLB，并对其进行管理。本文以Nginx应用为例，介绍如何通过使用自动创建SLB的服务来公开应用。



## 注意事项

-   CCM只为`Type=LoadBalancer`类型的服务配置SLB。对于非LoadBalancer类型的服务，则不会为其配置负载均衡。

    **说明：** 当`Type=LoadBalancer`的服务变更为其他类型时，CCM会删除为该SLB添加的配置，从而造成无法通过该SLB访问服务。

-   CCM使用声明式API，会在一定条件下自动根据服务的配置刷新SLB配置。您自行在SLB控制台上修改的配置均存在被覆盖的风险。

    **说明：** 请勿在SLB控制台上手动修改Kubernetes创建并维护的SLB的任何配置，否则有配置丢失的风险，造成服务不可访问。


## SLB配额限制

-   CCM会为`Type=LoadBalancer`类型的Service创建SLB。默认情况下一个用户可以保留60个SLB实例。如果需要创建的SLB数量大于60，请[提交工单](https://selfservice.console.aliyun.com/ticket/createIndex)给SLB产品。

    **说明：** 您可以在提交工单时，说明需要修改`slb_quota_instances_num`参数，用于提高您可保有的SLB实例个数。

-   CCM会根据Service中定义的端口创建SLB监听。默认情况下一个SLB实例可以添加50个监听，如需添加更多监听，请提交工单给SLB产品。

    **说明：** 您可以在提交工单时，说明需要修改`slb_quota_listeners_num`参数，用于提高每个实例可以保有的监听数量。

-   CCM会根据Service的配置将ECS挂载到SLB后端服务器组中。

    -   默认情况下一个ECS实例可挂载的后端服务器组的数量为50个，如果一台ECS需要挂载到更多的后端服务器组中，请[提交工单](https://selfservice.console.aliyun.com/ticket/createIndex)给SLB产品。

        **说明：** 您可以在提交工单时，说明需要修改`slb_quota_backendserver_attached_num`参数，用于提高同一台服务器可以重复添加为SLB后端服务器的次数。

    -   默认情况下一个SLB实例可以挂载200个后端服务器，如果需要挂载更多的后端服务器，请提交工单给SLB产品。

        **说明：** 您可以在提交工单时，说明需要修改`slb_quota_backendservers_num`参数，提高每个SLB实例可以挂载的服务器数量。

    更多SLB使用限制请参见[使用限制](/cn.zh-CN/传统型负载均衡CLB/CLB用户指南/产品限制/使用限制.md)。查询负载均衡SLB配额，请参见[负载均衡SLB配额管理](https://slbnew.console.aliyun.com/slb/quota)。


## 步骤一：部署示例应用

以下应用部署通过kubectl命令行方式进行。如果您需要通过控制台部署应用，请参见[使用镜像创建无状态Deployment应用](/cn.zh-CN/Kubernetes集群用户指南/应用管理/使用镜像创建无状态Deployment应用.md)。

1.  使用以下示例应用的YAML内容，创建名为my-nginx.yaml文件。

    ```
    apiVersion: apps/v1 # for versions before 1.8.0 use apps/v1beta1
    kind: Deployment
    metadata:
      name: my-nginx    #示例应用的名称。
      labels:
        app: nginx
    spec:
      replicas: 3       #设置副本数量。
      selector:
        matchLabels:
          app: nginx     #对应服务中Selector的值需要与其一致，才可以通过服务公开此应用。
      template:
        metadata:
          labels:
            app: nginx
        spec:
        #  nodeSelector:
        #    env: test-team
          containers:
          - name: nginx
            image: registry.aliyuncs.com/acs/netdia:latest     #替换为您实际的镜像地址，格式为：<image_name:tags>。
            ports:
            - containerPort: 80                                #需要在服务中暴露该端口。
    ```

2.  执行以下命令，部署示例应用my-nginx。

    ```
    kubectl apply -f my-nginx.yaml
    ```

3.  执行以下命令，确认示例应用状态正常。

    ```
    kubectl get deployment my-nginx
    ```

    返回结果示例：

    ```
    NAME       READY   UP-TO-DATE   AVAILABLE   AGE
    my-nginx   3/3     3            3           50s
    ```


## 步骤二：通过使用自动创建SLB的服务公开应用

您可以通过控制台和kubectl两种方式来创建LoadBalancer类型的服务，并通过其公开应用。

**控制台方式**

1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。

2.  在控制台左侧导航栏中，单击**集群**。

3.  在集群列表页面中，单击目标集群名称或者目标集群右侧**操作**列下的**详情**。

4.  在集群管理页左侧导航栏中，选择**服务与路由** \> **服务**。

5.  在**服务**页面，单击右上角的**创建**。

6.  在创建服务对话框中，设置服务的相关参数。

    ![service-auto](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/3353329061/p206553.png)

    |参数|描述|
    |--|--|
    |**名称**|输入服务的名称，本示例为my-nginx-svc。|
    |**类型**|选择服务类型，即服务访问的方式。依次选择：**负载均衡** -\> **公网访问** -\> **新建SLB** -\> 单击**修改**选择所需的SLB规格。本示例使用默认规格：简约型I（slb.s1.small）。|
    |**关联**|选择关联该服务要绑定的后端应用，本示例为my-nginx。若不进行关联部署，则不会创建相关的Endpoints对象，您也可自己进行绑定，请参见[services-without-selectors](https://kubernetes.io/docs/concepts/services-networking/service/#services-without-selectors)。|
    |**外部流量策略**|设置外部流量策略，本示例为**Local**。    -   **Local**：流量只发给本机的Pod。
    -   **Cluster**：流量可以转发到集群中其他节点上的Pod。
**说明：** 您的服务类型为**节点端口**或**负载均衡**时，才能设置**外部流量策略**。 |
    |**端口映射**|添加服务端口（对应Service YAML文件中的`port`）和容器端口（对应Service YAML文件中的`targetPort`），容器端口需要与后端的Pod中暴露的容器端口一致。本示例皆为80。|
    |**注解**|为该服务添加一个注解（Annotation），配置负载均衡的参数。您可以选择**自定义注解**或**阿里云注解**。本示例中，将该服务的收费方式设置为按带宽收费，带宽峰值设置为2 Mbit/s，从而控制服务的流量。更多注解请参见[通过Annotation配置负载均衡](/cn.zh-CN/Kubernetes集群用户指南/网络管理/Service管理/通过Annotation配置负载均衡.md)。

    -   **类型**：阿里云注解
    -   **名称**：`service.beta.kubernetes.io/alibaba-cloud-loadbalancer-charge-type`、`service.beta.kubernetes.io/alicloud-loadbalancer-bandwidth`、
    -   **值**：paybybandwidth、2 |
    |**标签**|为该服务添加一个标签，标识该服务。|

7.  单击**创建**。

    在**服务**页面，可以看到新创建的服务。

    ![endpoint](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/4699309061/p206515.png)

8.  单击该服务在**外部端点**列的**39.106.xx.xx:80**，访问示例应用。


**Kubectl方式**

1.  使用以下示例服务的YAML内容，创建名为my-nginx-svc.yaml的文件。

    将selector修改为my-nginx.yaml示例应用文件中matchLabels的值（本示例为`app: nginx`），从而将该服务关联至后端应用。

    ```
    apiVersion: v1
    kind: Service
    metadata:
      labels:
        app: nignx
      name: my-nginx-svc
      namespace: default
    spec:
      ports:
      - port: 80
        protocol: TCP
        targetPort: 80
      selector:
        app: nginx
      type: LoadBalancer
    ```

2.  执行以下命令创建名为my-nginx-svc的服务，并通过其公开应用。

    ```
    kubectl apply -f my-nginx-svc.yaml
    ```

3.  执行以下命令确认LoadBalancer类型的服务创建成功。

    ```
    kubectl get svc my-nginx-svc
    ```

    返回结果示例：

    ```
    NAME           TYPE           CLUSTER-IP    EXTERNAL-IP      PORT(S)        AGE
    my-nginx-svc   LoadBalancer   172.21.5.82   39.106.xx.xx     80:30471/TCP   5m
    ```

4.  执行curl <YOUR-External-IP\>命令访问示例应用，请将<YOUR-External-IP\>替换为上面获取到的`EXTERNAL-IP`地址。

    ```
    curl 39.106.xx.xx
    ```

    返回结果示例：

    ```
    <!DOCTYPE html>
    <html>
    <head>
    <title>Welcome to nginx!</title>
    <style>
        body {
            width: 35em;
            margin: 0 auto;
            font-family: Tahoma, Verdana, Arial, sans-serif;
        }
    </style>
    </head>
    <body>
    <h1>Welcome to nginx!</h1>
    <p>If you see this page, the nginx web server is successfully installed and
    working. Further configuration is required.</p>
    
    <p>For online documentation and support please refer to
    <a href="http://nginx.org/">nginx.org</a>.<br/>
    Commercial support is available at
    <a href="http://nginx.com/">nginx.com</a>.</p>
    
    <p><em>Thank you for using nginx.</em></p>
    </body>
    </html>
    ```


**相关文档**  


[通过Annotation配置负载均衡](/cn.zh-CN/Kubernetes集群用户指南/网络管理/Service管理/通过Annotation配置负载均衡.md)

[通过使用已有SLB的服务公开应用](/cn.zh-CN/Kubernetes集群用户指南/网络管理/Service管理/通过使用已有SLB的服务公开应用.md)

