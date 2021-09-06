---
keyword: [Horizontal Pod Autoscaling, HPA, auto scaling]
---

# HPA

You can create an application that has Horizontal Pod Autoscaling \(HPA\) enabled in the Container Service for Kubernetes \(ACK\) console. HPA can automatically scale container resources for your application. You can also use a YAML file to describe HPA settings.

Before you enable HPA, make sure that you have completed the following steps:

-   [Create a managed Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Create Kubernetes clusters/Create a managed Kubernetes cluster.md)
-   [t16645.md\#](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Access clusters/Connect to ACK clusters by using kubectl.md)

## Create an application that has HPA enabled in the ACK console

ACK is integrated with HPA. You can create an application that has HPA enabled in the ACK console. You can enable HPA when you create an application or after the application is created.

**Enable HPA when you create an application**

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane of the ACK console, click **Clusters**.

3.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  In the left-side navigation pane of the details page, choose **Workloads** \> **Deployments**.

5.  On the Deployments page, click **Create from Image**.

6.  On the Basic Information wizard page, enter a name for your application, set other required parameters, and then click **Next**.

    |Parameter|Description|
    |---------|-----------|
    |Name|The name of the application.|
    |Replicas|The number of pods that are provisioned for the application. Default value: 2.|
    |Type|The type of workload. You can select **Deployments**, **StatefulSets**, **Jobs**, **CronJobs**, or **DaemonSets**.|
    |Label|Add a label to the application. The label is used to identify the application.|
    |Annotations|Add an annotation to the application.|
    |Synchronize Timezone|Specify whether to synchronize the time zone between nodes and containers.|

7.  On the Container wizard page, set the container parameters, select an image, and then configure the required computing resources. Click **Next**. For more information, see [Configure containers for an application](/intl.en-US/User Guide for Kubernetes Clusters/Application management/Workloads/Create a stateless application by using a Deployment.md).

    **Note:** You must configure the required computing resources for the Deployment. Otherwise, you cannot enable HPA.

8.  On the Advanced wizard page, find the Access Control section, click **Create** on the right side of Services, and then set the parameters. For more information, see [Configure advanced settings for an application](/intl.en-US/User Guide for Kubernetes Clusters/Application management/Workloads/Create a stateless application by using a Deployment.md).

9.  On the Advanced wizard page, select **Enable** for **HPA** and configure the scaling threshold and related settings.

    -   **Metric**: Select CPU Usage or Memory Usage. The selected resource type must be the same as the one that you have specified in the Required Resources field.
    -   **Condition**: Specify the resource usage threshold. HPA triggers scaling activities when the threshold is exceeded.
    -   **Max. Replicas**: Specify the maximum number of pods to which the Deployment can be scaled.
    -   **Min. Replicas**: Specify the minimum number of pods that must run for the Deployment.
10. In the lower-right corner of the Advanced wizard page, click **Create**. The application is created with HPA enabled.


**Verify the result**

1.  Click **View Details** or choose **Workloads** \> **Deployments**. On the page that appears, click the **name of the created application** or click **Details** in the **Actions** column. Then, click the **Pod Scaling** tab to view information about the scaling group of the application.

2.  After the application starts to run, container resources are automatically scaled based on the CPU utilization. You can check whether HPA is enabled in the staging environment by performing a CPU stress test on the pods of the application. Verify that the pods are automatically scaled within 30 seconds.

**Enable HPA after an application is created**

This example describes how to enable HPA for a stateless application.

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane of the ACK console, click **Clusters**.

3.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  In the left-side navigation pane of the details page, choose **Workloads** \> **Deployments**.

5.  On the **Deployments** page, click the name of the application that you want to manage.

6.  Click the **Pod Scaling** tab and click **Create**.

7.  In the **Create** dialog box, configure the HPA settings. For more information about how to set the parameters, see [HPA settings](#step_wto_syh_7s4) in Step 9.

8.  Click **OK**.


## Use kubectl to enable HPA

You can also create a Horizontal Pod Autoscaler by using an orchestration template and associate the Horizontal Pod Autoscaler with the Deployment for which you want to enable HPA. Then, you can use kubectl to enable HPA.

In the following example, HPA is enabled for an NGINX application.

1.  Create a file named nginx.yml and copy the following content into the file.

    The following code block is a YAML template that is used to create a Deployment:

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
              requests:                         ##To enable HPA, you must set this parameter. 
                cpu: 500m
    ```

2.  Run the following command to create an NGINX application:

    ```
    kubectl create -f nginx.yml
    ```

3.  Create a Horizontal Pod Autoscaler.

    Use scaleTargetRef to associate the Horizontal Pod Autoscaler with the Deployment named nginx.

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

    **Note:** You must configure the requested resources for the pods of the application. Otherwise, the Horizontal Pod Autoscaler cannot be started.

4.  Run the `kubectl describe hpa name` command. The following output is an example of a warning that is returned:

    ```
    Warning  FailedGetResourceMetric       2m (x6 over 4m)  horizontal-pod-autoscaler  missing request for cpu on container nginx in pod default/nginx-deployment-basic-75675f5897-mqzs7
    
    Warning  FailedComputeMetricsReplicas  2m (x6 over 4m)  horizontal-pod-autoscaler  failed to get cpu utilization: missing request for cpu on container nginx in pod default/nginx-deployment-basic-75675f5
    ```

5.  After the Horizontal Pod Autoscaler is created, run the `kubectl describe hpa name` command.

    If the following output is returned, it indicates that the Horizontal Pod Autoscaler runs as expected:

    ```
    Normal SuccessfulRescale 39s horizontal-pod-autoscaler New size: 1; reason: All metrics below target
    ```

    If the pod usage of the NGINX application exceeds 50% as specified in the HPA settings, the Horizontal Pod Autoscaler automatically adds pods. If the pod usage of the NGINX application drops below 50%, the Horizontal Pod Autoscaler automatically removes pods.


