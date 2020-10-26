# HPA

Container Service for Kubernetes \(ACK\) supports the Horizontal Pod Autoscaler \(HPA\) that enables auto scaling of pod resources. You can create an application that has an HPA in the ACK console. You can also configure an HPA by defining the YAML configuration.

Before you enable auto scaling, make sure that the following operations are complete:

-   [Create a cluster of ACK Managed Edition](/intl.en-US/User Guide for Kubernetes Clusters/Cluster management/Create Kubernetes clusters/Create a cluster of ACK Managed Edition.md)
-   [Use kubectl to connect to a cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster management/Access clusters/Use kubectl to connect to a cluster.md)

## Create an application that has an HPA in the ACK console

ACK has integrated the HPA. Therefore, you can create an application that has an HPA in the ACK console.

1.  Log on to the [Container Service for Kubernetes \(ACK\) console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane, click **Clusters**.

3.  On the Clusters page, click the name of a cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  In the left-side navigation pane, click **Workload**.

5.  On the Deployments tab, click **Create from Image**.

6.  Select the cluster and the namespace where you want to deploy the application. In the Basic Information step, enter the application name and click **Next**.

7.  In the Container step, configure the container parameters. Select an image and configure the required resources. Click **Next**. For more information, see [Create deployments by using images](/intl.en-US/User Guide for Kubernetes Clusters/Application management/Create deployments by using images.md).

    **Note:** You must configure required resources for the application. Otherwise, you cannot enable auto scaling.

8.  In the Advanced step, click **Create** next to Services in the Access Control section and configure the parameters. For more information, see [Configure advanced settings](/intl.en-US/User Guide for Kubernetes Clusters/Application management/Create deployments by using images.md).

9.  In the Advanced step, select the **Enable** check box next to **HPA** and configure the parameters.

    ![Application settings](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/4265359951/p10578.png)

    -   **Metric**: Select CPU Usage or Memory Usage. The selected resource type must be the same as the one that you have specified in the Required Resources field.
    -   **Condition**: Specify the resource usage threshold. The HPA triggers scaling events when the threshold is exceeded.
    -   **Max. Replicas**: the maximum number of pod replicas.
    -   **Min. Replicas**: the minimum number of pod replicas.
10. Click **Create** in the lower-right corner. The application that supports auto scaling is created.

    **Expected results**

    1.  Click **View Details** or choose **Workload** \> **Deployments**. On the page that appears, click the name of the application or click **Details** in the **Actions** column for the application. Then, click the **Horizontal Pod Autoscaler** tab to view the scaling information of the application.****

    2.  In the actual environment, the application scales based on the CPU utilization. You can also verify auto scaling in the test environment. After you perform a CPU pressure test on the pods, you can find that the pods can complete horizontal scaling in half a minute.

## Use kubectl commands to configure auto scaling

You can also create an HPA by using an orchestration template and bind the HPA to the deployment for which you want to enable auto scaling. You can use kubectl commands to configure auto scaling.

The following example is based on an NGINX application.

1.  Create the nginx.yml file and copy the following code block to the file.

    The following code block shows the orchestration template for the deployment:

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
              requests:                         ##This parameter is required to run the HPA.
                cpu: 500m
    ```

2.  Run the following command to create an NGINX application:

    ```
    kubectl create -f nginx.yml
    ```

3.  Create an HPA.

    Use scaleTargetRef to bind the HPA to the nginx deployment.

    ```
    apiVersion: autoscaling/v2beta1
    kind: HorizontalPodAutoscaler
    metadata:
      name: nginx-hpa
      namespace: default
    spec:
      scaleTargetRef:                             ##Bind the HPA to the nginx deployment.
        apiVersion: apps/v1
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

    **Note:** You must configure the requested resources for the pods. Otherwise, the HPA cannot run.

4.  Run the `kubectl describe hpa name` command. A warning similar to the following output appears.

    ```
    Warning  FailedGetResourceMetric       2m (x6 over 4m)  horizontal-pod-autoscaler  missing request for cpu on container nginx in pod default/nginx-deployment-basic-75675f5897-mqzs7
    
    Warning  FailedComputeMetricsReplicas  2m (x6 over 4m)  horizontal-pod-autoscaler  failed to get cpu utilization: missing request for cpu on container nginx in pod default/nginx-deployment-basic-75675f5
    ```

5.  After you create an HPA, run the `kubectl describe hpa name` command again.

    If the following information appears, the HPA is running properly.

    ```
    Normal SuccessfulRescale 39s horizontal-pod-autoscaler New size: 1; reason: All metrics below target
    ```

    If the pod usage of the NGINX application exceeds 50%, the HPA scales out pods. If the pod usage of the NGINX application is lower than 50%, the HPA scales in pods.


