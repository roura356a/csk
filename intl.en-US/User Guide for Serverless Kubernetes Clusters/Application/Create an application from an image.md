---
keyword: [create serverless Kubernetes clusters, use an image, create an application]
---

# Create an application from an image

This topic describes how to create an application from an image in the Container Service for Kubernetes \(ACK\) console.

A serverless Kubernetes \(ASK\) cluster is created. For more information, see [Create an ASK cluster](/intl.en-US/User Guide for Serverless Kubernetes Clusters/Quick start/Create an ASK cluster.md).

## Step 1: Configure basic settings

1.  Log on to the [Container Service for Kubernetes \(ACK\) console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane, click **Clusters**.

3.  On the Clusters page, click the name of a cluster or click **Details** in the **Actions** column.

4.  In the left-side navigation pane of the details page, choose **Workloads** \> **Deployments**.

5.  In the upper-right corner of the **Deployments** page, click **Create from Image**.

6.  On the Basic Information wizard page, configure the basic settings.

    |Parameter|Description|
    |---------|-----------|
    |Name|The name of the application.|
    |Replicas|Specify the number of pods that are provisioned for the application.|
    |Type|The type of application, for example, **Deployment** or **StatefulSet**.|
    |Label|Add a label to the application. A label is used to identify the application.|
    |Annotations|Add an annotation to the application.|

7.  Click **Next**. Proceed to the **Container** wizard page.


## Step 2: Configure the containers

On the **Container** wizard page, set the configurations of the containers for the application. The configurations include the container image, computing resources, container ports, environment variables, health checks, lifecycle, and volumes.

**Note:** At the top of the **Container** wizard page, you can click **Add Container** to add containers to the pod.

1.  In the **General** section, configure the basic settings of the container.

    |Parameter|Description|
    |---------|-----------|
    |Image Name|You can click **Select Image**. In the dialog box that appears, select an image and click **OK**.You can also enter the address of a private registry. The registry address must be in the following format: `domainname/namespace/imagename:tag`. |
    |Image Version|Click **Select Image Version** and select an image version. If you do not specify an image version, the latest image version is used.|
    |**Always Pull Images**: If you do not select this check box, ACK caches the pulled image. This improves the efficiency of application deployments. If the specified image version is the same as the cached image version, ACK creates the application from the cached image. Therefore, when you update the application code, if you do not change the image version for reasons such as to support the upper-layer workloads, the previously cached image is used. If you select this check box, ACK pulls the image from the repository each time the application is deployed. This ensures that the latest image and code are used.|
    |**Set Image Pull Secret**: Click **Set Image Pull Secret** to configure a Secret that is used to pull images. You must configure a Secret if you want to pull images from a private repository. For more information, see [Create an application by using an image pull secret](/intl.en-US/User Guide for Kubernetes Clusters/Application management/Workloads/Use a Deployment to create a stateless application.md).|
    |Required Resources|The amount of CPU and memory resources that are reserved for this application. These resources are exclusive to the container. This prevents the application from becoming unavailable when other services or processes compete for computing resources.|
    |Container Start Parameter|    -   stdin: Pass stdin to the container.
    -   tty: Stdin is a TeleTYpewriter \(TTY\). |
    |Privileged Container|    -   If you select Privileged Container, privileged=true is set for the container and the privilege mode is enabled.
    -   If you do not select Privileged Container, privileged=false is set for the container and the privilege mode is disabled. |
    |Init Container|If you select Init Container, an init container is created. An init container provides tools for pod management. For more information, see [init containers](https://kubernetes.io/docs/concepts/workloads/pods/init-containers/?spm=a2c4g.11186623.2.13.3fdd30dfnyevPx).|

2.  In the **Ports** section, click **Add** to configure one or more container ports.

    -   Name: Enter a name for the container port.
    -   Container Port: Enter the container port that you want to open. Enter a port number from 1 to 65535.
    -   Protocol: Select TCP or UDP.
3.  In the **Environments** section, click **Add** to configure one or more environment variables.

    You can configure environment variables for pods in key-value pairs. Environment variables are used to pass pod configurations to containers. For more information, see [Pod variables](https://kubernetes.io/docs/tasks/inject-data-application/environment-variable-expose-pod-information/?spm=0.0.0.0.8VJbrE).

    -   Type: Select the type of environment variable, for example, **Custom** or **ConfigMaps**. If you select ConfigMaps or Secrets, you can pass all data in the selected ConfigMap or Secret to the container environment variables. In this example, a Secret is selected.

        Select **Secrets** from the Type drop-down list and select a Secret from the **Value/ValueFrom** drop-down list. By default, all data in the selected Secret is passed to the environment variable.

        In this case, the YAML file that is used to deploy the application contains the settings that reference all data in the specified Secret.

        ![yaml](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/9007846161/p130413.jpg)

    -   Variable Key: Specify the name of the environment variable.
    -   Value/ValueFrom: Specify a reference that is used to define the environment variable.
4.  In the **Health Check** section, you can enable **liveness** and **readiness** probes as needed.

    For more information about health checks, see [Configure Liveness, Readiness and Startup Probes](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-startup-probes/).

    -   Liveness probes are used to detect when to restart the container.
    -   Readiness probes are used to determine whether the container is ready to accept traffic.
    |Request type|Parameter and description|
    |------------|-------------------------|
    |HTTP|Sends an HTTP GET request to the container. You can set the following parameters:    -   Protocol: Select HTTP or HTTPS.
    -   Path: the requested path on the server.
    -   Port: the container port that you want to open. Enter a port number from 1 to 65535.
    -   HTTP Header: the custom headers in the HTTP request. Duplicate headers are allowed. Key-value pairs are supported.
    -   Initial Delay \(s\): the initialDelaySeconds field in the YAML file. This field specifies the time period \(in seconds\) that the system must wait before it can send a probe to the container after the container is started. Default value: 3.
    -   Period \(s\): the periodSeconds field in the YAML file. This field specifies the interval \(in seconds\) at which probes are performed. Default value: 10. Minimum value: 1.
    -   Timeout \(s\): the timeoutSeconds field in the YAML file. This field specifies the time \(in seconds\) after which a probe times out. Default value: 1. Minimum value: 1.
    -   Healthy Threshold: the minimum number of times that an unhealthy container must consecutively pass health checks before it is considered healthy. Default value: 1. Minimum value: 1. For liveness probes, this parameter must be set to 1.
    -   Unhealthy Threshold: the minimum number of times that a healthy container must consecutively fail health checks before it is considered unhealthy. Default value: 3. Minimum value: 1. |
    |TCP|Sends a TCP socket to the container. kubelet attempts to open the socket on the specified port. If the connection can be established, the container is considered healthy. Otherwise, the container is considered unhealthy. You can set the following parameters:    -   Port: the container port that you want to open. Enter a port number from 1 to 65535.
    -   Initial Delay \(s\): the initialDelaySeconds field in the YAML file. This field specifies the time period \(in seconds\) that the system must wait before it can send a probe to the container after the container is started. Default value: 15.
    -   Period \(s\): the periodSeconds field in the YAML file. This field specifies the interval \(in seconds\) at which probes are performed. Default value: 10. Minimum value: 1.
    -   Timeout \(s\): the timeoutSeconds field in the YAML file. This field specifies the time \(in seconds\) after which a probe times out. Default value: 1. Minimum value: 1.
    -   Healthy Threshold: the minimum number of times that an unhealthy container must consecutively pass health checks before it is considered healthy. Default value: 1. Minimum value: 1. For liveness probes, this parameter must be set to 1.
    -   Unhealthy Threshold: the minimum number of times that a healthy container must consecutively fail health checks before it is considered unhealthy. Default value: 3. Minimum value: 1. |
    |Command|Runs a probe command in the container to check the health status of the container. You can set the following parameters:    -   Command: Enter the probe command that is run to check the health status of the container.
    -   Initial Delay \(s\): the initialDelaySeconds field in the YAML file. This field specifies the time period \(in seconds\) that the system must wait before it can send a probe to the container after the container is started. Default value: 5.
    -   Period \(s\): the periodSeconds field in the YAML file. This field specifies the time interval \(in seconds\) at which probles are performed. Default value: 10. Minimum value: 1.
    -   Timeout \(s\): the timeoutSeconds field in the YAML file. This field specifies the time \(in seconds\) after which a probe times out. Default value: 1. Minimum value: 1.
    -   Healthy Threshold: the minimum number of times that an unhealthy container must consecutively pass health checks before it is considered healthy. Default value: 1. Minimum value: 1. For liveness probes, this parameter must be set to 1.
    -   Unhealthy Threshold: the minimum number of times that a healthy container must consecutively fail health checks before it is considered unhealthy. Default value: 3. Minimum value: 1. |

5.  In the **Lifecycle** section, set the lifecycle of the container.

    You can set the following parameters to configure the lifecycle of the container: Start, Post Start, and Pre Stop. For more information, see [Configure the lifecycle of a container](https://kubernetes.io/docs/tasks/configure-pod-container/attach-handler-lifecycle-event/).

    -   **Start**: Set the command and parameter that take effect before the container starts.
    -   **Post Start**: Set the command that takes effect after the container starts.
    -   **Pre Stop**: Set the command that takes effect before the container stops.
6.  In the **Volume** section, add on-premises storage volumes or persistent volume claims \(PVCs\).

    The following types of storage volumes are supported:

    1.  On-premises storage volume
    2.  PVC
    3.  NAS
    4.  Disk
7.  Click **Next** to configure advanced settings.


## Step 3: Configure advanced settings

On the **Advanced** wizard page, configure access control, scaling configurations, labels, and annotations.

1.  In the **Access Control** section, configure the access control settings to expose the backend pods.

    **Note:**

    You can configure access control settings based on your business requirements:

    -   Internal applications: For applications that run inside the cluster, you can create a Service of the **ClusterIP** or **NodePort** type to enable internal communication.
    -   External applications: For applications that are exposed to the Internet, you can configure access control by using one of the following methods:
        -   Create a LoadBalancer Service. When you create a Service, set Type to **Server Load Balancer**. You can select or create a Server Load Balancer \(SLB\) instance for the Service and use the Service to expose your application to the Internet.
        -   Create an Ingress and use the Ingress to expose your application to the Internet. For more information, see [Ingress](https://kubernetes.io/docs/concepts/services-networking/ingress/?spm=a2c4g.11186623.2.23.3fdd30dfnyevPx).
    You can specify how the backend pods are exposed to the Internet. In this example, a ClusterIP Service and an Ingress are created to expose the NGINX application to the Internet.

    -   To create a Service, click **Create** on the right side of **Services**. In the Create Service dialog box, set the parameters.

        |Parameter|Description|
        |---------|-----------|
        |Name|The name of the Service. In this example, nginx-svc is used.|
        |Type|The type of Service. This parameter determines how the Service is accessed. In this example, **Cluster IP** is selected.         -   **Cluster IP**: exposes the Service by using an internal IP address of the cluster. If you select this type, the Service is accessible only within the cluster. This is the default type.

**Note:** The **Headless Service** check box is available only when you set Type to **Cluster IP**.

        -   **Server Load Balancer**: exposes the Service by using an SLB instance. If you select this type, you can enable internal or external access to the Service. SLB instances can be used to route requests to NodePort and ClusterIP Services.

            -   Create SLB Instance: You can click **Modify** to change the specification of the SLB instance.
            -   Use Existing SLB Instance: You can select an existing SLB instance from the drop-down list.
**Note:** You can create an SLB instance or use an existing SLB instance. You can also associate an SLB instance with more than one Service. However, you must take note of the following limits:

            -   If you use an existing SLB instance, the listeners of the SLB instance overwrite the listeners of the Service.
            -   If an SLB instance is created along with a Service, you cannot use this SLB instance to expose other Services. Otherwise, the SLB instance may be deleted. You can reuse only SLB instances that are manually created in the console or by calling the API.
            -   An SLB instance must listen on different Service ports if the SLB instance exposes more than one Service. Otherwise, port conflicts may occur.
            -   When an SLB instance exposes multiple Services, the names of listeners and vServer groups are used as unique identifiers in Kubernetes. Do not modify the names of listeners and vServer groups.
            -   You cannot use an SLB instance to expose Services across clusters. |
        |Port Mapping|Specify a Service port and a container port. The container port must be the same as the one that is exposed in the backend pod.|
        |External Traffic Policy|        -   Local: routes network traffic to only the node where the Service is deployed.
        -   Cluster: routes network traffic to pods on other nodes.
**Note:** The **External Traffic Policy** parameter is available only when you set Type to **Node Port** or **Server Load Balancer**. |
        |Annotations|Add one or more annotations to the Service to modify the configuration of the SLB instance. For example, `service.beta.kubernetes.io/alicloud-loadbalancer-bandwidth:20` specifies that the maximum bandwidth of the Service is 20 Mbit/s. This limits the amount of traffic that flows through the Service. For more information, see [Use annotations to configure load balancing](/intl.en-US/User Guide for Kubernetes Clusters/Network/Service Management/Use annotations to configure load balancing.md).|
        |Label|Add one or more labels to the Service. Labels are used to identify the Service.|

    -   To create an Ingress, click **Create** on the right side of **Ingresses**. In the Create dialog box, set the parameters.

        For more information, see [Create an Ingress](/intl.en-US/User Guide for Kubernetes Clusters/Network/Ingress management/Basic operations of an Ingress.md).

        **Note:** When you deploy an application from an image, you can create an Ingress for only one Service. In this example, the name of a virtual host is used as the test domain name. You must add the following mapping rule to the hosts file to map the domain name to the IP address of the Ingress. The entry is in the format of <Ingress external endpoint\> + <Ingress domain name\>. In actual scenarios, use a domain name that has obtained an Internet Content Provider \(ICP\) number.

        ```
        101.37.XX.XX   foo.bar.com    # The IP address of the Ingress.
        ```

        |Parameter|Description|
        |---------|-----------|
        |Name|Enter a name for the Ingress. In this example, nginx-ingress is used.|
        |Rules|Ingress rules are used to enable access to a specified Service in a cluster. For more information, see [Create an Ingress](/intl.en-US/User Guide for Kubernetes Clusters/Network/Ingress management/Basic operations of an Ingress.md).         -   **Domain**: Enter the domain name of the Ingress. In this example, the test domain name `foo.bar.com` is used.
        -   **Path**: Enter the Service URL. The default path is the root path /. The default path is used in this example. Each path is associated with a backend Service. SLB forwards traffic to a backend Service only when inbound requests match the domain name and path.
        -   **Service**: Set the Service name and port. In this example, nginx-svc is used.
        -   Select **EnableTLS** to enable TLS. For more information, see [Advanced Ingress configurations](/intl.en-US/User Guide for Kubernetes Clusters/Network/Ingress management/Configure an Ingress.md). |
        |Weight|Set the weight for each Service in the path. Each weight is calculated as a percentage value. Default value: 100.|
        |Annotations|        -   Click **Rewrite Annotation** to add a rewrite annotation for the Ingress. For example, `nginx.ingress.kubernetes.io/rewrite-target:/` specifies that /path is redirected to the root path /. The root path can be recognized by the backend Service.
        -   You can also click **Add**, and enter the name and value of an annotation. For more information, see [Annotations](https://kubernetes.github.io/ingress-nginx/user-guide/nginx-configuration/annotations/). |
        |Labels|Add labels to describe the characteristics of the Ingress.|

    After the Service and Ingress are created, you can find the newly created Service and Ingress in the **Access Control** section. Click **Update** or **Delete** to modify the Ingress configurations.

2.  In the **Scaling** section, specify whether to enable **HPA** to automatically scale the number of pods based on the CPU and memory usage.

    ACK supports the auto scaling of pods. This allows you to automatically adjust the number of pods based on the CPU and memory usage.

    **Note:** To enable HPA, you must configure the required resources for the container. Otherwise, HPA does not take effect.

    |Parameter|Description|
    |---------|-----------|
    |Metric|Select CPU Usage or Memory Usage. The selected resource type must be the same as that specified in the Required Resources field.|
    |Condition|Specify the resource usage threshold. HPA triggers scale-out activities when the threshold is exceeded.|
    |Max. Replicas|Specify the maximum number of pod replicas to which the application can be scaled.|
    |Min. Replicas|Specify the minimum number of pod replicas that must run.|

3.  In the **Labels,Annotations** section, click **Add** to configure labels and annotations for the pod.

    -   Pod Labels: Add a label to the pod. A label is used to identify the application.
    -   Pod Annotations: Add an annotation to the pod.
4.  Click **Create**.


## Step 4: Check the application

On the **Complete** wizard page, you can view the created application.

1.  On the **Complete** wizard page, click **View Details**. On the Deployments page, you can find the newly created application named serverless-app-svc.

    ![Deployment list](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/1163182061/p10257.png)

2.  In the left-side navigation pane of the details page of the cluster, choose **Services and Ingresses** \> **Services**. On the Services page, you can find the newly created Service named serverless-app-svc.

    ![Service list](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/1163182061/p10258.png)

3.  To visit the NGINX welcome page, you can use your browser to access the external endpoint of the Service.

    ![nginx](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/9399397161/p146837.png)


