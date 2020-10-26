# Create a service

Each pod has its own IP address. Pods are automatically created and deleted. If you use pods to provide services, high availability may not be ensured. The service abstraction allows you to decouple the frontend from the backend by using services. The frontend does not depend on the specific implementation of the backend. This ensures the design of loosely-coupled microservices. You can create a service in Container Service for Kubernetes \(ACK\). This topic describes how to create a service in the ACK console.

A Kubernetes cluster is created. For more information, see [Create a cluster of ACK Proprietary Edition](/intl.en-US/User Guide for Kubernetes Clusters/Cluster management/Create Kubernetes clusters/Create a cluster of ACK Proprietary Edition.md).

A Kubernetes service is known as a microservice. It is an abstraction that defines a logical set of pods and specifies a policy that is used to access the pods. A label selector determines whether the set of pods can be accessed by the service.

For more information, see [Service](https://kubernetes.io/docs/concepts/services-networking/service).

## Step 1: Create a deployment

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane, click **Clusters**.

3.  On the Clusters page, click the name of a cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  On the **Deployments** tab, click **Create from Template**.

5.  Set Sample Template to Custom or select a sample template from the Sample Template drop-down list, and click **Create**.

    In this example, the template of an NGINX deployment is used.

    ```
    apiVersion: apps/v1 
    kind: Deployment
    metadata:
       name: nginx-deployment-basic
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
             image: nginx:1.7.9                # replace it with your exactly <image_name:tags>
             ports.
             - containerPort: 80               ##Open this port in the service
    ```

6.  On the Deployments tab, find the specified deployment, and click the name or click **Details** in the Actions column of the deployment. On the Basic Information page, you can check the status of the deployment.

    ![Check the application running status](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/6345359951/p11024.png)


## Step 1: Create a custom service

1.  In the left-side navigation pane, click **Services**.

2.  Go to the Cluster Information page. In the left-side navigation pane, click Services. On the Services page, click **Create**.

3.  In the Create Service dialog box, set the following parameters.

    |Parameter|Description|
    |---------|-----------|
    |**Name**|The name of the service. In this example, nginx-svc is used.|
    |**Type**|The type of service. This parameter determines the method that is used to access the service. Valid values:    -   Cluster IP: provides the service by using the internal IP address of the cluster. If you select this option, the service is accessible only within the cluster.

**Note:** The **Headless Service** checkbox is available only when you set the service type to **Cluster IP**. If you select this check box, the service can be discovered and accessed by other types of services, instead of only by Kubernetes services.

    -   Node Port: enables access to the service by using the IP address and static port on each node. The NodePort field specifies the static port. A NodePort service can be used to route requests to a Cluster IP service. The system automatically creates the Cluster IP service. You can access a Node Port service from outside the cluster by requesting `<NodeIP>:<NodePort>`.
    -   Server Load Balancer: provides the service by using an SLB instance This supports Internet access or internal access. An SLB service can route requests to Node Port and Cluster IP services.

        -   Create SLB Instance: You can click **Modify** to change the SLB instance type.
        -   Use Existing SLB Instance: You can select an existing SLB instance.
**Note:** You can create an SLB instance or use an existing SLB instance. Multiple services can reuse the same SLB instance. However, the following limits apply:

        -   If you use an existing SLB instance, the existing listeners of the SLB instance are overwritten.
        -   If an SLB instance is created when you create a service, you cannot reuse this SLB instance when you create other services. Otherwise, the SLB instance may be deleted. You can reuse only SLB instances that are manually created in the console or by calling API operations.
        -   Services that reuse the same SLB instance cannot have the same frontend listening port. Otherwise, port conflicts may occur.
        -   When you reuse an SLB instance, you must use the listener name and the VServer group name as unique identifiers in Kubernetes. Do not modify the listener name or VServer group name.
        -   You cannot reuse SLB instances across clusters. |
    |**Backend**|The backend object that you want to associate with the service. In this example, nginx-deployment-basic that is created in the previous step is selected. If you do not specify a deployment, no Endpoint object will be created. You can manually associate the service with an Endpoint object. For more information, see [services-without-selectors](https://kubernetes.io/docs/concepts/services-networking/service/#services-without-selectors).|
    |**External Traffic Policy**|Valid values: Local and Cluster.**Note:** The **External Traffic Policy** parameter is available only when you set the service type to **Node Port** or **Server Load Balancer**. |
    |**Port Mapping**|Set the service port and container port. The container port must be the same as the one opened by the backend pod.|
    |**Annotations**|Add annotations to the service and configure SLB parameters. For example, `service.beta.kubernetes.io/alicloud-loadbalancer-bandwidth:20` specifies that the service bandwidth is set to 20 Mbit/s. For more information, see [t16677.md\#](/intl.en-US/User Guide for Kubernetes Clusters/Network management/Service Management/Use annotations to configure SLB instances.md).|
    |**Label**|Add labels to the service.|

4.  Click **Create**. The nginx-svc service appears on the Services page.

    ![Services](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/6345359951/p11027.png)

5.  Enter the external endpoint of the nginx-svc service in your browser to access the service.

    ![View the external endpoint](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/6345359951/p11028.png)

    You have created the service and associated the service with a backend deployment. You can visit the NGINX welcome page.


