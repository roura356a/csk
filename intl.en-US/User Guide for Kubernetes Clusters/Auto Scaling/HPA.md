# HPA

You can create an application that has Horizontal Pod Autoscaling \(HPA\) enabled in the Container Service for Kubernetes \(ACK\) console. HPA can automatically scale container resources for your application. You can also configure HPA settings for an application by using YAML files.

Before you enable HPA, make sure that you have completed the following steps:

-   [Create a managed kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster management/Create Kubernetes clusters/Create a managed kubernetes cluster.md)
-   [Use kubectl to connect to an ACK cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster management/Access clusters/Use kubectl to connect to an ACK cluster.md)

## Create an application that has HPA enabled in the ACK console

ACK is integrated with HPA. You can create an application that has HPA enabled in the ACK console. You can enable HPA when you create an application or after the application is created.

**Enable HPA when you create an application**

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane, click **Clusters**.

3.  On the Clusters page, click the name of a cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  In the left-side navigation pane, click **Workload**.

5.  On the Deployments tab, click **Create from Image**.

6.  On the Basic Information wizard page, enter the application name, select a cluster and a namespace to deploy the application, and then click **Next**.

    |Parameter|Description|
    |---------|-----------|
    |Name|The name of the application.|
    |Namespace|The namespace in which the application is deployed. If you do not set this field, the system uses the default namespace.|
    |Replicas|The number of pods in an application. Default value: 2.|
    |Type|The type of the application. You can select **Deployments**, **StatefulSets**, **Jobs**, **Cron Jobs**, or **DaemonSets**.|
    |Label|Add one or more labels to the application.|
    |Annotations|Add one or more annotations to the application.|
    |Synchronize Timezone|Specify whether to synchronize the timezone from nodes to containers.|

7.  On the Container wizard page, set the container parameters, select an image, and configure the required computing resources. Click **Next**. For more information, see [Configure containers for an application](/intl.en-US/User Guide for Kubernetes Clusters/Application management/Use an image to create a stateless application.md).

    **Note:** You must configure the required computing resources for the Deployment. Otherwise, you cannot enable HPA.

8.  In the Advanced wizard page, click **Create** on the right side of Services in the Access Control section and set the related parameters. For more information, see [Configure advanced settings for an application](/intl.en-US/User Guide for Kubernetes Clusters/Application management/Use an image to create a stateless application.md).

9.  On the Advanced wizard page, select **Enable** for **HPA** and configure the scaling threshold and related settings.

    ![Application settings](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/4265359951/p10578.png)

    -   **Metric**: Select CPU Usage or Memory Usage. The selected resource type must be the same as the one that you have specified in the Required Resources parameter.
    -   **Condition**: Specify the resource usage threshold. HPA triggers scaling events when the threshold is exceeded.
    -   **Max. Replicas**: Specify the maximum number of pod replicas to which the Deployment can be scaled.
    -   **Min. Replicas**: Specify the minimum number of pod replicas that must run for the Deployment.
10. In the lower-right corner of the Advanced wizard page, click **Create**. The application will be created with HPA enabled.

    **Check the results**

    1.  Click **View Details** or choose **Workload** \> **Deployments**. On the page that appears, click the **name of the created application** or click **Details** in the **Actions** column. Then, click the **Horizontal Pod Autoscaler** tab to view information about the scaling group of the application.

    2.  After the application starts to run, it automatically scales based on the CPU usage. You can also check whether HPA is enabled in the staging environment. After you perform a CPU stress test on the application pods, you can find that the pods automatically complete horizontal scaling within 30 seconds.

**Enable HPA after an application is created**

1.  On the **Deployments** tab, find and click the application for which you want to enable HPA.

2.  Click the **Horizontal Pod Autoscaler** tab and click **Create** on the right side of HPA.

    ![hpa](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/8438767061/p180399.png)

3.  In the **Create** dialog box, configure the HPA settings. For more information about how to set the related parameters, see [HPA settings](#step_wto_syh_7s4) in Step 9.

4.  Click **OK**.


## Run kubectl commands to enable HPA

You can also create a Horizontal Pod Autoscaler by using an orchestration template and bind the Horizontal Pod Autoscaler to the Deployment for which you want to enable HPA. Then, you can run kubectl commands to enable HPA.

An NGINX application is used as an example.

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
              requests:                         ##This parameter is required to run the Horizontal Pod Autoscaler.
                cpu: 500m
    ```

2.  Run the following command to create an NGINX application:

    ```
    kubectl create -f nginx.yml
    ```

3.  Create a Horizontal Pod Autoscaler.

    Use scaleTargetRef to bind the Horizontal Pod Autoscaler to the Deployment named nginx.

    ```
    apiVersion: autoscaling/v2beta1
    kind: HorizontalPodAutoscaler
    metadata:
      name: nginx-hpa
      namespace: default
    spec:
      scaleTargetRef:                             ##Bind the Horizontal Pod Autoscaler to the Deployment named nginx.
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

    **Note:** You must configure the requested resources for the application pods. Otherwise, the Horizontal Pod Autoscaler cannot be launched.

4.  Run the `kubectl describe hpa name` command. The following output is an example of a warning that is returned:

    ```
    Warning  FailedGetResourceMetric       2m (x6 over 4m)  horizontal-pod-autoscaler  missing request for cpu on container nginx in pod default/nginx-deployment-basic-75675f5897-mqzs7
    
    Warning  FailedComputeMetricsReplicas  2m (x6 over 4m)  horizontal-pod-autoscaler  failed to get cpu utilization: missing request for cpu on container nginx in pod default/nginx-deployment-basic-75675f5
    ```

5.  After the Horizontal Pod Autoscaler is created, run the `kubectl describe hpa name` command.

    If the following output is returned, it indicates that the Horizontal Pod Autoscaler is running as expected.

    ```
    Normal SuccessfulRescale 39s horizontal-pod-autoscaler New size: 1; reason: All metrics below target
    ```

    If the pod usage of the NGINX application exceeds 50% as configured in the HPA settings, the Horizontal Pod Autoscaler automatically scales out the pods. If the pod usage of the NGINX application is lower than 50%, the Horizontal Pod Autoscaler automatically scales in the pods.


