---
keyword: [create ASK clusters, create an application from an image, ASK]
---

# Create an application from an image

This topic describes how to create an application from an image in the Container Service for Kubernetes \(ACK\) console.

A serverless Kubernetes \(ASK\) cluster is created. For more information, see [Create an ASK cluster](/intl.en-US/User Guide for Serverless Kubernetes Clusters/Quick start/Create an ASK cluster.md).

## Step 1: Configure the basic information of the application

1.  Log on to the [Container Service for Kubernetes \(ACK\) console](https://cs.console.aliyun.com)[ACK console](https://partners-intl.console.aliyun.com/#/cs).

2.  In the left-side navigation pane, choose **Applications** \> **Deployments**. In the upper-right corner of the Deployments page, click **Create from Image**.

3.  In the left-side navigation pane, click **Serverless Clusters**.

4.  On the Clusters page, click the name of a cluster or click **Details** in the **Actions** column.

5.  In the left-side navigation pane of the details page, choose **Workloads** \> **Deployments**.

6.  On the **Deployments** tab, click **Create from Image**.

7.  On the Basic Information wizard page, configure the basic settings.

    |Parameter|Description|
    |---------|-----------|
    |Name|The name of the application.|
    |Replicas|The number of replicas equals the number of replicated pods that are provisioned for the application.|
    |Type|The type of application, for example, **Deployment** or **StatefulSet**.|
    |Labels|The labels attached to the application.|
    |Annotations|The annotations added to the application.|

8.  Click **Next**. Proceed to the **Container** wizard page.


## Step 2: Configure containers for the application

On the **Container** wizard page, set the configurations of the containers for the application. The configurations include the container image, computing resources, container port, environment variables, health checks, lifecycle, and volumes.

**Note:** At the top of the **Container** Wizard page, you can click **Add Container** to add containers to the pod.

1.  In the **General** section, configure the basic settings of containers.

    |Parameter|Description|
    |---------|-----------|
    |Image Name|To select a Docker image or an image from Container Registry \(ACR\), click **Select Image**. In the dialog box that appears, select an image and click **OK**.You can also enter the address of a private image registry. The registry address must be in the following format: `domainname/namespace/imagename:tag`. |
    |Image Version|Click **Select Image Version** and select an image version. If you do not specify an image version, the latest image version is used.|
    |**Always Pull Images**: If you do not select this check box, ACK caches the pulled image. This improves the efficiency of deploying applications. If the specified image version is the same as the cached image version, ACK creates the application from the cached image. Therefore, when you update the application code, if you do not change the image version for reasons such as to support the upper-layer workloads, the previously cached image is used. If you select this check box, ACK pulls the image from the repository each time the application is deployed. This ensures that the latest image and code are used.|
    |**Set Image Pull Secret**: Click **Set Image Pull Secret** to set a secret for pulling images. You must set the secret if you need to pull the image from a private repository. For more information, see [Use an image secret](/intl.en-US/User Guide for Kubernetes Clusters/Application management/Use an image secret.md).|
    |Required Resources|The amount of CPU and memory resources that are reserved for this application. These resources are exclusive to the container. This prevents the application from becoming unavailable when other services or processes compete for resources.|
    |Container Start Parameter|    -   stdin: specifies that start parameters defined in the console are imported to the Linux system.
    -   tty: specifies that start parameters defined in a virtual terminal are imported to the console. |
    |Privileged Container|    -   If you select Privileged Container, privileged=true is set for the container and the privilege mode is enabled.
    -   If you do not select Privileged Container, privileged=false is set for the container and the privilege mode is disabled. |
    |Init Container|If you select Init Container, an init container is created. An init container provides tools for managing pods. For more information, see [init containers](https://kubernetes.io/docs/concepts/workloads/pods/init-containers/?spm=a2c4g.11186623.2.13.3fdd30dfnyevPx).|

2.  In the **Ports** section, click **Add** to open the container port.

    -   Name: Specify the name of the port.
    -   Port: Specify the container port that you want to open. Enter a port number from 1 to 65535.
    -   Protocol: Select TCP or UDP.
3.  In the **Environments** section, click **Add** to add environment variables.

    You can use key-value pairs to set environment variables for pods. Environment variables are used to pass pod configurations to containers. For more information, see [Pod variables](https://kubernetes.io/docs/tasks/inject-data-application/environment-variable-expose-pod-information/?spm=0.0.0.0.8VJbrE).

    -   Type: Select the type of environment variable, for example, **Custom** or **ConfigMaps**. If you select ConfigMaps or Secret as the type of environment variable, all values in the selected ConfigMap or Secret are passed to the container environment variables. In this example, Secret is selected.

        Select **Secret** from the Name drop-down list and select a Secret from the **Value/ValueFrom** drop-down list. All values in the selected Secret are passed to the environment variable.

        In this case, the YAML file that is used to deploy the application contains the settings that reference all values in the specified Secret.

    -   Variable Key: Specify the key of the environment variable.
    -   Value/ValueFrom: Specify a resource as the reference to define the environment variable.
4.  In the **Health Check** section, enable **liveness probe** or **readiness probe** based on your business requirements.

    For more information about health checks, see [Configure Liveness, Readiness and Startup Probes](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-startup-probes/).

    -   Liveness probe: determines when to restart a container.
    -   Readiness probe: determines whether a container is ready to accept traffic.
    |Request type|Description|
    |------------|-----------|
    |HTTP request|Sends an HTTP GET request to the container. You can set the following parameters:    -   Protocol: HTTP or HTTPS.
    -   Path: the requested path on the server.
    -   Port: the container port that you want to open. Enter a port number from 1 to 65535.
    -   HTTP Header: the custom headers in the HTTP request. Duplicate headers are allowed. Key-value pairs are supported.
    -   Initial Delay \(s\): the initialDelaySeconds field in the YAML file. This field specifies the time \(in seconds\) to wait before the first probe is performed after the container is started. Default value: 3.
    -   Period \(s\): the periodSeconds field in the YAML file. This field specifies how often \(in seconds\) the probe is performed. Default value: 10. Minimum value: 1.
    -   Timeout \(s\): the timeoutSeconds field in the YAML file. This field specifies the time \(in seconds\) after which the probe times out. Default value: 1. Minimum value: 1.
    -   Healthy Threshold: the minimum number of consecutive successes that must be achieved before the probe is considered successful after a failure. Default value: 1. Minimum value: 1. For liveness probes, this parameter must be set to 1.
    -   Unhealthy Threshold: the minimum number of consecutive failures that must be achieved before the probe is considered failed after a success. Default value: 3. Minimum value: 1. |
    |TCP connection|Sends a TCP socket to the container. The kubelet attempts to open the socket on the specified port. If the connection can be established, the container is considered healthy. Otherwise, the container is considered unhealthy. You can set the following parameters:    -   Port: the container port that you want to open. Enter a port number from 1 to 65535.
    -   Initial Delay \(s\): the initialDelaySeconds field in the YAML file. This field specifies the time \(in seconds\) to wait before the first probe is performed after the container is started. Default value: 15.
    -   Period \(s\): the periodSeconds field in the YAML file. This field specifies how often \(in seconds\) the probe is performed. Default value: 10. Minimum value: 1.
    -   Timeout \(s\): the timeoutSeconds field in the YAML file. This field specifies the time \(in seconds\) after which the probe times out. Default value: 1. Minimum value: 1.
    -   Healthy Threshold: the minimum number of consecutive successes that must be achieved before the probe is considered successful after a failure. Default value: 1. Minimum value: 1. For liveness probes, this parameter must be set to 1.
    -   Unhealthy Threshold: The minimum number of consecutive failures that must be achieved before the probe is considered failed after a success. Default value: 3. Minimum value: 1. |
    |Command|Runs a probe command in the container to check the health status. You can set the following parameters:    -   Command: the probe command that is run to check the health status of the container.
    -   Initial Delay \(s\): the initialDelaySeconds field in the YAML file. This field specifies the time \(in seconds\) to wait before the first probe is performed after the container is started. Default value: 5.
    -   Period \(s\): the periodSeconds field in the YAML file. This field specifies how often \(in seconds\) the probe is performed. Default value: 10. Minimum value: 1.
    -   Timeout \(s\): the timeoutSeconds field in the YAML file. This field specifies the time \(in seconds\) after which the probe times out. Default value: 1. Minimum value: 1.
    -   Healthy Threshold: the minimum number of consecutive successes that must be achieved before the probe is considered successful after a failure. Default value: 1. Minimum value: 1. For liveness probes, this parameter must be set to 1.
    -   Unhealthy Threshold: The minimum number of consecutive failures that must be achieved before the probe is considered failed after a success. Default value: 3. Minimum value: 1. |

5.  In the **Lifecycle** section, configure the lifecycle of the container.

    You can set the following parameters to configure the lifecycle of the container: Start, Post Start, and Pre Stop. For more information, see [Configure the lifecycle of a container](https://kubernetes.io/docs/tasks/configure-pod-container/attach-handler-lifecycle-event/).

    -   **Start**: Specify the pre-start command and parameter.
    -   **Post Start**: Specify the post-start command.
    -   **Pre Stop**: Specify the pre-stop command.
6.  In the **Volume** section, add on-premises storage volumes or persistent volume claims \(PVCs\).

    The following storage volumes are supported:

    1.  On-premises storage volumes
    2.  PVCs
    3.  NAS
    4.  Cloud disks
7.  Click **Next** to configure advanced settings.


## Step 3: Configure advanced settings

On the **Advanced** wizard page, configure access control, scaling configurations, labels, and annotations.

1.  In the **Access Control** section, configure the access control settings to expose pods that run the application.

    **Note:**

    You can configure access control settings based on your business requirements:

    -   Internal applications: For applications that run inside the cluster, you can create a **ClusterIP** or **NodePort** type Service to enable internal communication.
    -   External applications: For applications that are open to the Internet, you can configure access control by using one of the following methods:
        -   Create LoadBalancer type Services: When you create a Service, set the Service type to **Server Load Balancer**. This allows you to use Server Load Balancer \(SLB\) instances to enable access to the application over the Internet.
        -   Create Ingresses: exposes Services to the Internet by using Ingresses. For more information, see [Ingresses](https://kubernetes.io/docs/concepts/services-networking/ingress/?spm=a2c4g.11186623.2.23.3fdd30dfnyevPx).
    Configure the access control settings to expose pods that run the application. In this example, a ClusterIP type Service and an Ingress are created to enable Internet access to the NGINX application.

    -   Create a ClusterIP type Service: On the right side of **Services**, click **Create** and configure the Service.

        |Parameter|Description|
        |---------|-----------|
        |Name|Enter a name for the Service. In this example, enter nginx-svc.|
        |Type|Select the type of Service. This parameter specifies how the Service is accessed. In this example, select **Cluster IP**.        -   **Cluster IP**: exposes the Service through an internal IP address in the cluster. This type of Service is equivalent to a ClusterIP type Service. If you select Cluster IP, the Service is accessible from only within the cluster. This is the default setting.

**Note:** The **Headless Service** option is available only when you select **Cluster IP**.

        -   **Server Load Balancer**: exposes the Service through a Server Load Balancer \(SLB\) instance. This type of Service is equivalent to a LoadBalancer type Service. If you select Server Load Balancer, you can enable internal access or external access to the Service. An SLB instance can route access to NodePort and ClusterIP type Services.

            -   Create an SLB instance: If you choose to create an SLB instance, you can click **Modify** to change the SLB instance specifications.
            -   Use an Existing SLB Instance: If you choose to use an existing SLB instance, you can select an existing SLB instance from the drop-down list.
**Note:** You can create a new SLB instance or use an existing SLB instance. Multiple Services can share the same SLB instance. However, you must note the following limits:

            -   If you use an existing SLB instance, the listeners of the SLB instance will be overwritten.
            -   If an SLB instance is created when you create a Service, you cannot reuse this SLB instance when you create other Services. Otherwise, the SLB instance may be deleted. You can reuse only SLB instances that are manually created in the console or by calling the API.
            -   Services that use the same SLB instance cannot use the same listener port. Otherwise, port conflicts may occur.
            -   When you reuse an SLB instance, the names of listeners and VServer groups are deemed as the unique identifiers of these resources in Kubernetes. Do not modify the names of these resources.
            -   You cannot share SLB instances across clusters. |
        |Port Mapping|Add the Service port and container port. The container port must be the same as the one that is exposed by the backend pod.|
        |External Traffic Policy|        -   Local: Access is directed to only pods on the node where the Service is deployed.
        -   Cluster: Access can be directed to pods on other nodes.
**Note:** The **External Traffic Policy** option is available only when you set the service type to **Node Port** or **Server Load Balancer**. |
        |Annotations|Add annotations to the Service and configure parameters for the SLB instance. For example, `service.beta.kubernetes.io/alicloud-loadbalancer-bandwidth:20` specifies that the Service bandwidth is set to 20 Mbit/s. This allows you to regulate data transfer to the Service. For more information, see [Use annotations to configure SLB instances](/intl.en-US/User Guide for Kubernetes Clusters/Network/Service Management/Use annotations to configure SLB instances.md).|
        |Labels|Add labels to the Service.|

    -   Create an Ingress: On the right side of **Ingress**, click **Create** and configure the Ingress.

        For more information about the parameters that are required to create an Ingress, see [Ingress configurations](/intl.en-US/User Guide for Kubernetes Clusters/Network/Ingress management/Basic operations of an Ingress.md).

        **Note:** When you deploy an application from an image, you can create an Ingress for only one Service. In this example, a virtual hostname is specified as the testing domain. You must add a mapping to the hosts file for this domain in the following format: External endpoint of the Ingress + domain name of the Ingress. In actual scenarios, use a domain name that has obtained an ICP number.

        ```
        101.37.xx.xx   foo.bar.com    # The IP address of the Ingress.
        ```

        |Parameter|Description|
        |---------|-----------|
        |Name|Enter a name for the Ingress. In this example, enter nginx-ingress.|
        |Rules|Ingress rules are used to manage inbound traffic to Services in a cluster. For more information, see [Ingress configurations](/intl.en-US/User Guide for Kubernetes Clusters/Network/Ingress management/Basic operations of an Ingress.md).        -   **Domain**: Enter the domain name of the Ingress. In this example, testing domain name `foo.bar.com` is used.
        -   **Path**: Enter the URL for accessing the Service. The default path is the root path /. In this example, the default path is used. Each path is associated with a backend Service. SLB instances forward traffic to backend Services only when inbound requests match the domain name and path.
        -   **Service**: Select a Service and corresponding port. In this example, nginx-svc is selected.
        -   Select **EnableTLS** to enable Transport Layer Security \(TLS\) and configure secure routing. For more information, see [Configure an Ingress](/intl.en-US/User Guide for Kubernetes Clusters/Network/Ingress management/Configure an Ingress.md). |
        |Weight|Set a weight for each Service in the path. The weight is a percentage value. The default value is 100.|
        |Annotations|        -   Click **Rewrite Annotation** to add a rewrite annotation for the Ingress. For example, `nginx.ingress.kubernetes.io/rewrite-target: /` specifies that /path is redirected to the root path / that can be identified by the backend Services.
        -   You can also click **Add** to add annotations for the Ingresses. Enter keys and values. For more information about annotations of Ingresses, see [Annotations](https://kubernetes.github.io/ingress-nginx/user-guide/nginx-configuration/annotations/). |
        |Labels|Add labels to describe the features of the Ingress.|

    After the Service and Ingress are created, you can find the newly created Service and Ingress in the **Access Control** section. Click **Update** or **Delete** to modify the settings.

2.  In the **Scaling** section, specify whether to enable **Horizontal Pod Autoscaler \(HPA\)**. HPA enables the application to run smoothly at different load levels.

    After you enable HPA, ACK automatically scales the number of pods based on the CPU and memory usage.

    **Note:** To enable HPA, you must configure resource objects that can be scaled for the container. Otherwise, HPA cannot work.

    |Parameter|Description|
    |---------|-----------|
    |Metrics|Metric: Select CPU Usage or Memory Usage. The selected resource type must be the same as the one you have specified in the Required Resources field.|
    |Condition|Condition: Specify the resource usage threshold. HPA triggers scaling events when the threshold is exceeded.|
    |Max. Replica|Specify the maximum number of replicated pods to which the application can be scaled.|
    |Min. Replica|Specify the minimum number of replicated pods that must run.|

3.  In the **Labels,Annocations** section, click **Add** to configure labels and annotations for the pod.

    -   Pod Labels: Add labels to the pod.
    -   Pod Annotations: Add annotations to the pod.
4.  Click **Create**.


## Step 4:

On the **Complete** wizard page, you can view the newly created application.

1.  On the **Complete** wizard page, click **View Details**. On the Deployments tab, you can find the newly created application named serverless-app-svc.

    ![Deployment list](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/1163182061/p10257.png)

2.  In the left-side navigation pane of the details page of the cluster, click **Services**

    ![Service list](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/1163182061/p10258.png)

3.  To visit the NGINX welcome page, you can use your browser to access the external endpoint of the Service.


