---
keyword: [create ASK cluster, use image, ASK]
---

# Create an application from an image

This topic describes how to create an application from an image in the Container Service for Kubernetes \(ACK\) console.

A serverless Kubernetes \(ASK\) cluster is created. For more information, see [Create an ASK cluster](/intl.en-US/User Guide for Serverless Kubernetes Clusters/Quick start/Create an ASK cluster.md).

## Step 1: Configure the basic information of the application

1.  Log on to the [Container Service for Kubernetes \(ACK\) console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane, click **Serverless Clusters**.

3.  On the Clusters page, click the name of a cluster or click **Details** in the **Actions** column.

4.  In the left-side navigation pane of the details page, choose **Workloads** \> **Deployments**.

5.  In the upper-right corner of the **Deployments** page, click **Create from Image**.

6.  On the Basic Information wizard page, configure the basic settings.

    |Parameter|Description|
    |---------|-----------|
    |Name|Specify a name for the application.|
    |Replicas|Specify the number of pods that are provisioned for the application.|
    |Type|Specify the type of application. For example, **Deployment** or **StatefulSet**.|
    |Label|Add labels to the application. The labels are used to identify the application.|
    |Annotations|Add annotations to the application.|

7.  Click **Next**. Proceed to the **Container** wizard page.


## Step 2: Configure containers for the application

On the **Container** wizard page, set the configurations of the application containers. The configurations include the container image, computing resources, container port, environment variable, health check, lifecycle, and volume.

**Note:** At the top of the **Container** wizard page, you can click **Add Container** to add containers to the pod.

1.  In the **General** section, configure the basic settings of containers.

    |Parameter|Description|
    |---------|-----------|
    |Image Name|To select a Docker image or an image from Container Registry, click **Select Image**. In the dialog box that appears, select an image and click **OK**.You can also enter the address of a private registry. The registry address must be in the following format: `domainname/namespace/imagename:tag`. |
    |Image Version|Click **Select Image Version** and select an image version. If you do not specify an image version, the latest image version is used.|
    |**Always Pull Images**: If you do not select this check box, ACK caches the pulled image. This improves the efficiency of application deployments. If the specified image version is the same as the cached image version, ACK creates the application from the cached image. Therefore, when you update the application code, if you do not change the image version for reasons such as to support the upper-layer workloads, the previously cached image is used. If you select this check box, ACK pulls the image from the repository each time the application is deployed. This ensures that the latest image and code are used.|
    |**Set Image Pull Secret**: Click **Set Image Pull Secret** to set a Secret that is used to pull images. You must set the Secret if you need to pull images from a private repository. For more information, see [Use an image secret](/intl.en-US/User Guide for Kubernetes Clusters/Application management/Use an image secret.md).|
    |Required Resources|The amount of CPU and memory resources that are reserved for this application. These resources are exclusive to the container. This prevents the application from becoming unavailable when other applications or processes occupy these resources.|
    |Container Start Parameter|    -   stdin: specifies that start parameters defined in the console are sent to the container.
    -   tty: specifies that start parameters defined in a virtual terminal are sent to the console. |
    |Privileged Container|    -   If you select Privileged Container, privileged=true is set for the container and the privilege mode is enabled.
    -   If you do not select Privileged Container, privileged=false is set for the container and the privilege mode is disabled. |
    |Init Container|If you select Init Container, an init container is created. An init container provides tools for managing pods. For more information, see [init containers](https://kubernetes.io/docs/concepts/workloads/pods/init-containers/?spm=a2c4g.11186623.2.13.3fdd30dfnyevPx).|

2.  In the **Ports** section, click **Add** to open one or more container ports.

    -   Name: Enter a name for a container port.
    -   Container Port: the container ports that you want to open. Enter a port number from 1 to 65535.
    -   Protocol: Select TCP or UDP.
3.  In the **Environments** section, click **Add** to set one or more environment variables.

    You can set environment variables for pods in key-value pairs. Environment variables are used to pass pod configurations to containers. For more information, see [Pod variables](https://kubernetes.io/docs/tasks/inject-data-application/environment-variable-expose-pod-information/?spm=0.0.0.0.8VJbrE).

    -   Type: Select the type of environment variable. For example, you can set a **custom** environment variable or an environment variable of the **ConfigMap** type. If you select ConfigMap or Secret as the type of environment variable, all data in the selected ConfigMap or Secret is passed to the container environment variable. In this example, a Secret is selected.

        Select **Secret** from the Type drop-down list and select a Secret from the **Value/ValueFrom** drop-down list. All data in the selected Secret is passed to the environment variable.

        In this case, the YAML file that is used to deploy the application contains the settings that reference all data in the selected Secret.

        ![yaml](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/9007846161/p130413.jpg)

    -   Variable Key: Specify the name of the environment variable.
    -   Value/ValueFrom: Specify a reference that is used to define the environment variable.
4.  In the **Health Check** section, you can enable **liveness** and **readiness** probes as required.

    For more information about health checks, see [Configure Liveness, Readiness and Startup Probes](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-startup-probes/).

    -   Liveness probes are used to detect when to restart the container.
    -   Readiness probes are used to detect whether the container is ready to accept traffic.
    |Request type|Description|
    |------------|-----------|
    |HTTP|Sends an HTTP GET request to the container. You can set the following parameters:    -   Protocol: HTTP or HTTPS.
    -   Path: the requested path on the server.
    -   Port: the container port that you want to open. Enter a port number from 1 to 65535.
    -   HTTP Header: the custom headers in the HTTP request. Duplicate headers are allowed. You can specify the HTTP headers in key-value pairs.
    -   Initial Delay \(s\): the initialDelaySeconds field in the YAML file. This field specifies the time period \(in seconds\) that the system must wait before it can send the first probe to the container after it is launched. Default value: 3.
    -   Period \(s\): the periodSeconds field in the YAML file. This field specifies the interval \(in seconds\) at which probes are sent. Default value: 10. Minimum value: 1.
    -   Timeout \(s\): the timeoutSeconds field in the YAML file. This field specifies the time period \(in seconds\) after which a probe times out. Default value: 1. Minimum value: 1.
    -   Healthy Threshold: the minimum number of times that an unhealthy container must consecutively pass health checks before it is considered healthy. Default value: 1. Minimum value: 1. For liveness probes, this parameter must be set to 1.
    -   Unhealthy Threshold: the minimum number of times that a healthy container must consecutively fail health checks before it is considered unhealthy. Default value: 3. Minimum value: 1. |
    |TCP|Sends a TCP socket to the container. kubelet attempts to open the socket on the specified port. If the connection can be established, the container is considered healthy. Otherwise, the container is considered unhealthy. You can set the following parameters:    -   Port: the container port that you want to open. Enter a port number from 1 to 65535.
    -   Initial Delay \(s\): the initialDelaySeconds field in the YAML file. This field specifies the time period \(in seconds\) that the system must wait before it can send the first probe to the container after it is launched. Default value: 15.
    -   Period \(s\): the periodSeconds field in the YAML file. This field specifies the interval \(in seconds\) at which probes are sent. Default value: 10. Minimum value: 1.
    -   Timeout \(s\): the timeoutSeconds field in the YAML file. This field specifies the time period \(in seconds\) after which a probe times out. Default value: 1. Minimum value: 1.
    -   Healthy Threshold: the minimum number of times that an unhealthy container must consecutively pass health checks before it is considered healthy. Default value: 1. Minimum value: 1. For liveness probes, this parameter must be set to 1.
    -   Unhealthy Threshold: the minimum number of times that a healthy container must consecutively fail health checks before it is considered unhealthy. Default value: 3. Minimum value: 1. |
    |Command|Runs a probe command in the container to check the health status of the container. You can set the following parameters:    -   Command: the probe command that is run to check the health status of the container.
    -   Initial Delay \(s\): the initialDelaySeconds field in the YAML file. This field specifies the time period \(in seconds\) that the system must wait before it can send the first probe to the container after it is launched. Default value: 5.
    -   Period \(s\): the periodSeconds field in the YAML file. This field specifies the interval \(in seconds\) at which probes are sent. Default value: 10. Minimum value: 1.
    -   Timeout \(s\): the timeoutSeconds field in the YAML file. This field specifies the time period \(in seconds\) after which a probe times out. Default value: 1. Minimum value: 1.
    -   Healthy Threshold: the minimum number of times that an unhealthy container must consecutively pass health checks before it is considered healthy. Default value: 1. Minimum value: 1. For liveness probes, this parameter must be set to 1.
    -   Unhealthy Threshold: the minimum number of times that a healthy container must consecutively fail health checks before it is considered unhealthy. Default value: 3. Minimum value: 1. |

5.  In the **Lifecycle** section, set the lifecycle of the container.

    You can set the following parameters to configure the lifecycle of the container: Start, Post Start, and Pre Stop. For more information, see [Configure the lifecycle of a container](https://kubernetes.io/docs/tasks/configure-pod-container/attach-handler-lifecycle-event/).

    -   **Start**: Specify the prestart command and parameter.
    -   **Post Start**: Set the command that takes effect after the container is launched.
    -   **Pre Stop**: Specify the prestop command.
6.  In the **Volume** section, you can add on-premises volumes or persistent volume claims \(PVCs\).

    The following types of storage volumes are supported:

    1.  On-premises volume
    2.  PVC
    3.  NAS file system
    4.  Cloud disk
7.  Click **Next** to configure advanced settings.


## Step 3: Configure advanced settings

On the **Advanced** wizard page, configure access control, scaling configurations, labels, and annotations.

1.  In the **Access Control** section, you can configure access control settings to expose backend pods.

    **Note:**

    You can configure the following access control settings based on your business requirements:

    -   Internal applications: For applications that run inside the cluster, you can create a Service of the **ClusterIP** or **NodePort** type to enable internal communication.
    -   External applications: For applications that are open to the Internet, you can configure access control by using one of the following methods:
        -   Create a LoadBalancer Service. When you create a Service, set Type to **Server Load Balancer**. You can select or create a Server Load Balancer \(SLB\) instance for the Service and use the Service instance to expose your application to the Internet.
        -   Create an Ingress and use the Ingress to expose your application to the Internet. For more information, see [Ingress](https://kubernetes.io/docs/concepts/services-networking/ingress/?spm=a2c4g.11186623.2.23.3fdd30dfnyevPx).
    You can also specify how the backend pods are exposed to the Internet. In this example, a ClusterIP Service and an Ingress are created to expose the NGINX application to the Internet.

    -   To create a Service, click **Create** on the right side of **Services**. In the Create Service dialog box, set the parameters.

        |Parameter|Description|
        |---------|-----------|
        |Name|Enter a name for the Service. In this example, nginx-svc is used.|
        |Type|Select the type of Service. This parameter determines how the application is accessed. **Cluster IP** is selected in this example.         -   **Cluster IP**: exposes the Service through an internal IP address of the cluster. If you select this option, the Service is accessible only from within the cluster. This is the default value.

**Note:** The **Headless Service** checkbox is available only if you set Type to **Cluster IP**.

        -   **Server Load Balancer**: exposes the Service by using an SLB instance. If you select this option, you can select between internal or external access. You can also use the SLB instance to route requests to NodePort and ClusterIP Services.

            -   Create SLB Instance: You can click **Modify** to change the specification of the SLB instance.
            -   Use Existing SLB Instance: You can select an existing SLB instance.
**Note:** You can create a new SLB instance or use an existing SLB instance for a LoadBalancer Service. Multiple Services can share the same SLB instance. However, take note of the following limits:

            -   If you use an existing SLB instance, the listeners of the SLB instance overwrite those of the Service.
            -   If an SLB instance is created for a Service, you cannot reuse this SLB instance to expose other Services. Otherwise, the SLB instance may be accidentally deleted. Only SLB instances that are created in the console or by calling the API can be reused.
            -   Services that use the same SLB instance cannot use the same listener port. Otherwise, port conflicts may occur.
            -   When you reuse an SLB instance, the names of listeners and vServer groups are used as unique identifiers in Kubernetes. Do not modify the names of listeners or vServer groups.
            -   You cannot use an SLB instance to expose Services across clusters. |
        |Port Mapping|Set the Service port and container port. The container port must be the same as the one that is exposed in the backend pod.|
        |External Traffic Policy|        -   Local: routes network traffic to only pods on the node where the Service is created.
        -   Cluster: routes network traffic to pods on other nodes.
**Note:** **External Traffic Policy** is available only if you set Type to **Node Port** or **Server Load Balancer**. |
        |Annotations|Add an annotation to the Service to modify the configurations of the SLB instance. For example, the annotation `service.beta.kubernetes.io/alicloud-loadbalancer-bandwidth:20` specifies that the maximum bandwidth of the Service is 20 Mbit/s. This limits the traffic that flows through the Service. For more information, see [Use annotations to configure load balancing](/intl.en-US/User Guide for Kubernetes Clusters/Network/Service Management/Use annotations to configure load balancing.md).|
        |Label|Add labels to the Service.|

    -   To create an Ingress, click **Create** on the right side of **Ingresses**. In the Create dialog box, set the parameters.

        For more information about the parameters that are required to create an Ingress, see [Ingress configurations]().

        **Note:** When you deploy an application from an image, you can create an Ingress for only one Service. In this example, the name of a virtual host is specified as the test domain name. You must add a mapping to the hosts file in the following format: <External endpoint of the Ingress\> + <domain name of the Ingress\>. In practical scenarios, use a domain name that has obtained an Internet Content Provider \(ICP\) number.

        ```
        101.37.xx.xx   foo.bar.com    # The IP address of the Ingress.
        ```

        |Parameter|Description|
        |---------|-----------|
        |Name|Enter a name for the Ingress. In this example, nginx-ingress is used.|
        |Rules|Ingress rules are used to enable access to Services in a Kubernetes cluster. For more information, see [Ingress configurations]().         -   **Domain**: Enter a domain name for the Ingress. In this example, the test domain name `foo.bar.com` is used.
        -   **Path**: Enter the Service URL. The default path is the root path /. The default path is used in this example. Each path is associated with a backend Service. SLB forwards traffic to a backend Service only if inbound requests match the domain name and path.
        -   **Services**: Select a Service and port. In this example, the nginx-svc Service is selected.
        -   Select **EnableTLS** to enable TLS. For more information, see [Advanced Ingress configurations](/intl.en-US/User Guide for Kubernetes Clusters/Network/Ingress management/Configure an Ingress.md). |
        |Weight|Set a weight for each Service in the path. The weight is a percentage value. The default value is 100.|
        |Annotations|        -   You can click **Rewrite Annotation** to add a rewrite annotation for the Ingress. For example, the annotation `nginx.ingress.kubernetes.io/rewrite-target:/` specifies that /path is redirected to the root path /. The root path can be recognized by the backend Services.
        -   You can also click **Add** to enter the name and value of an annotation. For more information, see [Annotations](https://kubernetes.github.io/ingress-nginx/user-guide/nginx-configuration/annotations/). |
        |Labels|Add labels to describe the features of the Ingress.|

    After the Service and Ingress are created, you can find them in the **Access Control** section. Click **Update** or **Delete** to modify the settings.

2.  In the **Scaling** section, specify whether to enable **Horizontal Pod Autoscaler \(HPA\)** to automatically scale the application pods based on the CPU or memory usage.

    This enables the application to run as expected at different load levels.

    **Note:** To enable HPA, you must configure sufficient CPU and memory resources for the scaling of the application containers. Otherwise, HPA cannot work.

    |Parameter|Description|
    |---------|-----------|
    |Metric|Select CPU Usage or Memory Usage. The selected resource type must be the same as that specified in the Required Resources field.|
    |Condition|Specify the resource usage threshold. HPA triggers scale-out activities when the threshold is exceeded.|
    |Max. Replicas|Specify the maximum number of pod replicas to which the application can be scaled.|
    |Min. Replicas|Specify the minimum number of pod replicas that must run.|

3.  In the **Labels,Annotations** section, click **Add** to configure labels and annotations for the pod.

    -   Pod Labels: Add labels to the pod. The labels are used to identify the application.
    -   Pod Annotations: Add annotations to the pod.
4.  Click **Create**.


## Step 4: Check the application

After the application is created, you are redirected to the **Complete** wizard page.

1.  On the **Complete** wizard page, click **View Details**. On the Deployments tab, you can find the newly created application named serverless-app-svc.

    ![Deployments](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/1163182061/p10257.png)

2.  In the left-side navigation pane of the details page of the cluster, click **Services**. On the Services page, you can find the newly created Service named serverless-app-svc.

    ![Services](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/1163182061/p10258.png)

3.  To visit the NGINX welcome page, you can use your browser to access the external endpoint of the Service.

    ![nginx](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/9399397161/p146837.png)


