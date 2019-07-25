# Use an HPA auto scaling container {#concept_i2v_2h4_k2b .concept}

Alibaba Cloud Container Service supports the rapid creation of HPA-enabled applications on the console interface to achieve auto scaling of container resources. You can also configure it by defining the yaml configuration of Horizontal Pod Autoscaling \(HPA\).

## Prerequisites {#section_dxl_kj4_k2b .section}

-   You have created a Kubernetes cluster. For more information, see [Create a Kubernetes cluster](reseller.en-US/User Guide/Kubernetes cluster/Cluster management/Create a Kubernetes cluster.md#).
-   You have successfully connected to the master node of the Kubernetes cluster.

## Method 1: Create an HPA application in the Container Service console {#section_alx_3h4_k2b .section}

In Alibaba Cloud Container Service, HPA has been integrated. You can easily create it through the Container Service console.

1.  Log on to the [Container Service console](https://partners-intl.console.aliyun.com/#/cs).
2.  In the left-side navigation pane under Container Service-Kubernetes, choose **Applications** \> **Deployments**. Then, click **Create by Image** in the upper-right corner.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16700/156401820010577_en-US.png)

3.  Enter the application name, select the cluster and namespace, and click **Next**.
4.  Configure the application settings. Set the number of replicas, select the **Enable** box for Automatic Scaling, and configure the settings for scaling.

    -   **Metric**: CPU and memory. Configure a resource type as needed.
    -   **Condition**: The percentage value of resource usage. The container begins to expand when the resource usage exceeds this value.
    -   **Maximum Replicas**: The maximum number of replicas that the deployment can expand to.
    -   **Minimum Replicas**: The minimum number of replicas that the deployment can contract to.
    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16700/156401820010578_en-US.png)

5.  Configure the container. Select an image and configure the required resources. Click **Next**.

    **Note:** You must configure the required resources for the deployment. Otherwise, container auto scaling cannot be achieved.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16700/156401820010579_en-US.png)

6.  In the Access Control page, do not configure any settings in this example. Click **Create** directly.

    Now a deployment that supports HPA has been created. You can view the auto scaling group information in the details of your deployment.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16700/156401820110580_en-US.png)

7.  In the actual environment, the application scales according to the CPU load. You can also verify auto scaling in the test environment. By performing a CPU pressure test on the pod, you can find that the pod can complete the horizontal expansion in half a minute.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16700/156401820110581_en-US.png)


## Method 2: Use kubectl commands to configure container auto scaling {#section_ykx_3h4_k2b .section}

You can also manually create an HPA by using an orchestration template and bind it to the deployment object to be scaled. Use the kubectl command to complete the container auto scaling configuration.

The following is an example of an Nginx application. Execute the kubectl create -f xxx.yml command to create an orchestration template for the deployment as follows:

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
          requests:                         ##This parameter must be configured. Otherwise, the HPA cannot operate.
            cpu: 500m
```

Create an HPA. Configure an object to which the current HPA is bound by using scaleTargetRef. In this example, the object is the deployment named nginx.

```
apiVersion: autoscaling/v2beta1
kind: HorizontalPodAutoscaler
metadata:
  name: nginx-hpa
  namespace: default
spec:
  scaleTargetRef:                             ##Bind the HPA to a deployment named nginx
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

**Note:** The HPA needs to configure the request resource for the pod.The HPA does not operate without the request resource.

Warnings similar to the following are displayed when you execute kubectl describe hpa \[name\]:

```
Warning  FailedGetResourceMetric       2m (x6 over 4m)  horizontal-pod-autoscaler  missing request for cpu on container nginx in pod default/nginx-deployment-basic-75675f5897-mqzs7

Warning  FailedComputeMetricsReplicas  2m (x6 over 4m)  horizontal-pod-autoscaler  failed to get cpu utilization: missing request for cpu on container nginx in pod default/nginx-deployment-basic-75675f5
```

After creating the HPA, execute the kubectl describe hpa \[name\] command again. You can see the following message, which indicates that the HPA is running normally.

```
Normal SuccessfulRescale 39s horizontal-pod-autoscaler New size: 1; reason: All metrics below target
```

When the usage of Nginx pod exceeds 50% set in this example, the container expands horizontally. When the usage of Nginx pod drops below 50%, the container contracts.

