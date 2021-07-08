---
keyword: [use an existing SLB instance, Server Load Balancer, LoadBalancer Service, expose an application]
---

# Use an existing SLB instance to expose an application

Container Service for Kubernetes \(ACK\) allows you to use a Server Load Balancer \(SLB\) instance to expose a Service. To access the Service from outside the cluster, you can use the domain name of the SLB instance or the connection string `<IP:Service port>`. To access the Service from within the cluster, you can use the connection string `<Service name:Service port>`. This topic describes how to use an existing SLB instance to expose an application. An NGINX application is used as an example.

An SLB instance is created by using the SLB console. The SLB instance is deployed in the region where the cluster is created. For more information about how to create an SLB instance, see [Create a CLB instance](/intl.en-US/Classic Load Balancer/User Guide/Instance/Create a CLB instance.md).

By default, cloud controller manager \(CCM\) v1.9.3 and later do not automatically configure listeners for existing SLB instances. You can add the annotation `service.beta.kubernetes.io/alibaba-cloud-loadbalancer-force-override-listeners: "true"` to enable CCM to configure listeners. You can also manually configure listeners for an SLB instance.

You can use the following methods to check the CCM version:

-   **Console**: Log on to the ACK console and check the CCM version on the Add-ons page.
    1.  Log on to [the ACK console](https://cs.console.aliyun.com).
    2.  In the left-side navigation pane, click **Clusters**.
    3.  On the Clusters page, find the cluster that you want to manage, and choose **More** \> **Manage System Components** in the Actions column. On the **Add-ons** page, check the CCM version on the Core Components tab.
-   **kubectl**: Run the following command to check the CCM version. This method applies to only dedicated Kubernetes clusters.

    ```
    kubectl get pod -n kube-system -o yaml|grep image:|grep cloud-con|uniq
    ```


## Precautions

-   When you use an existing SLB instance to expose an application, take note of the following limits:
    -   The SLB instance must be created by using the SLB console. You cannot reuse SLB instances that are automatically created by CCM.
    -   To reuse an internal-facing SLB instance in a cluster, the SLB instance and the cluster must be deployed in the same virtual private cloud \(VPC\).
    -   The network type of the SLB instance must be consistent with the connection method of the Service. If the Service supports **public access** \(`service.beta.kubernetes.io/alibaba-cloud-loadbalancer-address-type: "internet"`\), the **network type** of the SLB instance must be **Internet-facing**. If the Service supports **internal access** \(`service.beta.kubernetes.io/alibaba-cloud-loadbalancer-address-type: "intranet"`\), the **network type** of the SLB instance must be **internal-facing**.
    -   The SLB instance must listen on different Service ports if the SLB instance exposes more than one Service.
-   CCM configures SLB instances only for `Type=LoadBalancer` Services. CCM does not configure SLB instances for other types of Services.

    **Note:** When a Service is changed from `Type=LoadBalancer` to another type, CCM deletes the configurations that are added to the SLB instance of the Service. As a result, you can no longer use the SLB instance to access the Service.

-   CCM uses a declarative API. CCM automatically updates the configurations of an SLB instance to match the configurations of the exposed Service when specific conditions are met. If you set `service.beta.kubernetes.io/alibaba-cloud-loadbalancer-force-override-listeners:` to `true`, the configuration modifications that you add in the SLB console may be overwritten.

    **Note:** Do not use the SLB console to modify the configurations of the SLB instance that is created and managed by CCM. Otherwise, the modifications may be overwritten and the Service may become inaccessible.


## SLB resource quotas

-   CCM creates SLB instances for `Type=LoadBalancer` Services. By default, you can have a maximum of 60 SLB instances within each Alibaba Cloud account. To create more than 60 SLB instances, [Submit a ticket](https://workorder-intl.console.aliyun.com/console.htm).

    **Note:** In the ticket, specify that you want to modify the `slb_quota_instances_num` parameter to create more SLB instances.

-   CCM automatically creates SLB listeners that use Service ports. By default, each SLB instance supports a maximum of 50 listeners. To create more than 50 listeners for an SLB instance, submit a ticket.

    **Note:** In the ticket, specify that you want to modify the `slb_quota_listeners_num` parameter to create more listeners for each SLB instance.

-   CCM automatically adds Elastic Compute Service \(ECS\) instances to backend server groups of an SLB instance based on the Service configurations.

    -   By default, an ECS instance can be added to up to 50 backend server groups. To add an ECS instance to more than 50 server groups, [Submit a ticket](https://workorder-intl.console.aliyun.com/console.htm).

        **Note:** In the ticket, specify that you want to modify the `slb_quota_backendserver_attached_num` parameter to add an ECS instance to more server groups.

    -   By default, you can add up to 200 backend servers to an SLB instance. To add more backend servers to an SLB instance, submit a ticket.

        **Note:** In the ticket, specify that you want to modify the `slb_quota_backendservers_num` parameter to add more backend servers to an SLB instance.

    For more information about SLB resource quotas, see [Limits](/intl.en-US/Classic Load Balancer/User Guide/Limits/Limits.md). To query SLB resource quotas, go to the [Quota Management page in the SLB console](https://slbnew.console.aliyun.com/slb/quota).


## Step 1: Deploy a sample application

The following section describes how to use the kubectl command-line tool to deploy an application. For more information about how to deploy an application by using the ACK console, see [Create a stateless application by using a Deployment](/intl.en-US/User Guide for Kubernetes Clusters/Application management/Workloads/Create a stateless application by using a Deployment.md).

1.  Use the following YAML template to create a my-nginx.yaml file:

    ```
    apiVersion: apps/v1 # for versions before 1.8.0 use apps/v1beta1
    kind: Deployment
    metadata:
      name: my-nginx    #The name of the sample application. 
      labels:
        app: nginx
    spec:
      replicas: 3       #The number of replicas. 
      selector:
        matchLabels:
          app: nginx     #You must specify the same value in the selector of the Service that is used to expose the application. 
      template:
        metadata:
          labels:
            app: nginx
        spec:
        #  nodeSelector:
        #    env: test-team
          containers:
          - name: nginx
            image: registry.aliyuncs.com/acs/netdia:latest     #Replace the value with the image address. Format: <image_name:tags>. 
            ports:
            - containerPort: 80                                #The port must be exposed in the Service. 
    ```

2.  Run the following command to deploy the my-nginx application:

    ```
    kubectl apply -f my-nginx.yaml
    ```

3.  Run the following command to check the state of the application:

    ```
    kubectl get deployment my-nginx
    ```

    Sample response:

    ```
    NAME       READY   UP-TO-DATE   AVAILABLE   AGE
    my-nginx   3/3     3            3           50s
    ```


## Step 2: Use an existing SLB instance to expose the application

You can use the ACK console or kubectl to create a LoadBalancer Service. After the Service is created, you can use the Service to expose the application.

**Use the ACK console**

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane of the ACK console, click **Clusters**.

3.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  In the left-side navigation pane of the details page, choose **Network** \> **Services**.

5.  On the **Services** page, click **Create** in the upper-right corner of the page.

6.  In the Create Service dialog box, set the required parameters.

    ![service](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/9173121161/p206514.png)

    |Parameter|Description|
    |---------|-----------|
    |**Name**|Enter a name for the Service. my-nginx-svc is used in this example.|
    |**Type**|Select the type of the Service. This parameter determines how the Service is accessed.     -   Choose **Server Load Balancer** -\> **Public Access** -\> **Use Existing SLB Instance** and select an SLB instance from the drop-down list.
    -   **Overwrite Existing Listeners**: Specify whether to overwrite the listeners of the selected SLB instance. If you select this check box but the SLB instance does not have listeners, the system automatically creates listeners for the SLB instance. In this example, the SLB instance is newly created and therefore you must select this check box to create listeners for the SLB instance.

**Note:**

        -   If the listeners of the SLB instance are associated with applications, service interruptions may occur after the configurations of the listeners are overwritten.
        -   CCM supports limited backend configurations and cannot handle complex configurations. If you require complex backend configurations, you can manually modify the listeners in the SLB console without overwriting the existing configurations of the listeners.
In both cases, we recommend that you do not overwrite the configurations of the listeners. However, you can overwrite the configuration of a listener if the port of the listener is no longer used. |
    |**Backend**|Select the application that you want to associate with the Service. The my-nginx application is selected in this example. If you do not associate the Service with a backend, no Endpoint object is created. You can manually associate the Service with a backend. For more information, see [services-without-selectors](https://kubernetes.io/docs/concepts/services-networking/service/#services-without-selectors).|
    |**External Traffic Policy**|Select a policy to distribute external network traffic. **Local** is selected in this example.     -   **Local**: routes network traffic to only pods on the node where the Service is deployed.
    -   **Cluster**: routes network traffic to pods on other nodes in the cluster.
**Note:** The **External Traffic Policy** parameter is available only if you set Type to **Node Port** or **Server Load Balancer**. |
    |**Port Mapping**|Specify a Service port and a container port. The Service port corresponds to the `port` field in the YAML file and the container port corresponds to the `targetPort` field in the YAML file. The container port must be the same as the one that is exposed in the backend pod. Both ports are set to 80 in this example.|
    |**Annotations**|Add one or more annotations to the Service to modify the configuration of the SLB instance. You can select **Custom Annotation** or **Alibaba Cloud Annotation** from the Type drop-down list. In this example, the billing method is set to pay-by-bandwidth and the maximum bandwidth is set to 2 Mbit/s to limit the amount of traffic that flows through the Service. For more information, see [Use annotations to configure load balancing](/intl.en-US/User Guide for Kubernetes Clusters/Network/Service Management/Use annotations to configure load balancing.md).

    -   **Type**: Alibaba Cloud Annotation
    -   **Name**: In this example, two annotations are created with the following names: `service.beta.kubernetes.io/alibaba-cloud-loadbalancer-charge-type` and `service.beta.kubernetes.io/alicloud-loadbalancer-bandwidth`.
    -   **Value**: In this example, the values of the annotations are set to paybybandwidth and 2. |
    |**Label**|Add one or more labels to the Service. Labels are used to identify the Service.|

7.  Click **Create**.

    On the **Services** page, you can view the created Service.

    ![endpoint](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/9173121161/p206515.png)

8.  Click **39.106.XX.XX:80** in the **External Endpoint** column to access the sample application.


**Use kubectl**

1.  Use the following YAML template to create a my-nginx-svc.yaml file.

    -   Replace the value $\{YOUR\_LB\_ID\} of `service.beta.kubernetes.io/alibaba-cloud-loadbalancer-id` with the ID of the SLB instance that is created in the [SLB console](https://slb.console.aliyun.com/slb).
    -   If you use an existing SLB instance, CCM does not create listeners for the SLB instance or overwrite the listeners of the SLB instance by default. If you want CCM to create new listeners or overwrite existing listeners, you can set `service.beta.kubernetes.io/alibaba-cloud-loadbalancer-force-override-listeners` to `true`. In this example, the SLB instance is newly created and therefore you must set this annotation to `true` to create listeners for the SLB instance. For more information, see [Use annotations to configure load balancing](/intl.en-US/User Guide for Kubernetes Clusters/Network/Service Management/Use annotations to configure load balancing.md).
    -   To associate the Service with the backend application, set selector to the value of matchLabels in the my-nginx.yaml file. The value is `app: nginx` in this example.
    ```
    apiVersion: v1
    kind: Service
    metadata:
      annotations:
        service.beta.kubernetes.io/alibaba-cloud-loadbalancer-id: ${YOUR_LB_ID}
        service.beta.kubernetes.io/alicloud-loadbalancer-force-override-listeners: 'true'
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

2.  Run the following command to create a Service named my-nginx-svc and use the Service to expose the application:

    ```
    kubectl apply -f my-nginx-svc.yaml
    ```

3.  Run the following command to verify that the LoadBalancer Service is created:

    ```
    kubectl get svc my-nginx-svc
    ```

    Sample response:

    ```
    NAME           TYPE           CLUSTER-IP    EXTERNAL-IP      PORT(S)        AGE
    my-nginx-svc   LoadBalancer   172.21.XX.XX   39.106.XX.XX     80:30471/TCP   5m
    ```

4.  Run the curl <YOUR-External-IP\> command to access the sample application. Replace <YOUR-External-IP\> with the IP address displayed in the `EXTERNAL-IP` column.

    ```
    curl 39.106.XX.XX
    ```

    Sample response:

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


**Related topics**  


[Use annotations to configure load balancing](/intl.en-US/User Guide for Kubernetes Clusters/Network/Service Management/Use annotations to configure load balancing.md)

[Use an automatically created SLB instance to expose an application](/intl.en-US/User Guide for Kubernetes Clusters/Network/Service Management/Use an automatically created SLB instance to expose an application.md)

