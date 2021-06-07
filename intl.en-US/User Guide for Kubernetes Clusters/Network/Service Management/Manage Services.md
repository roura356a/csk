---
keyword: [create Services, delete Services, update Services]
---

# Manage Services

Each pod in Kubernetes clusters has its own IP address. However, pods are frequently created and deleted. Therefore, it is not practical to directly expose pods to external access. Services decouple the frontend from the backend, which provides a loosely-coupled microservice architecture. This topic describes how to create, update, and delete Services by using the Container Service for Kubernetes \(ACK\) console and kubectl.

## Manage Services in the ACK console

**Create a Service**

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane of the ACK console, click **Clusters**.

3.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  In the left-side navigation pane of the details page, choose **Network** \> **Services**.

5.  On the Services page, click **Create** in the upper-right corner of the page.

6.  In the Create Service dialog box, set the parameters.

    |Parameter|Description|
    |---------|-----------|
    |**Name**|Enter a name for the Service.|
    |**Type**|Select the type of the Service. This parameter determines how the Service is accessed. Valid values:    -   Cluster IP: the ClusterIP Service. This type of Service is exposed by using the internal IP address of the cluster. This is the default value. If you select this option, the Service is accessible only from within the cluster.

**Note:** The **Headless Service** checkbox is available only if you set Type to **Cluster IP**. If you select this check box, you can use a headless Service to interface with other service discovery mechanisms, without being tied to the implementation of service discovery in Kubernetes.

    -   Node Port: the NodePort Service. This type of Service is accessed by using the IP address and a static port of each node. The Node Port field specifies the static port. A NodePort Service can be used to route requests to a ClusterIP Service, which is automatically created by the system. You can access a NodePort Service from outside the cluster by sending requests to `<NodeIP>:<NodePort>`.
    -   Server Load Balancer: the LoadBalancer Service. This type of Service is accessed by using Server Load Balancer \(SLB\) instances. If you select this option, you can enable internal or external access to the Service. LoadBalancer Services can be used to route requests to NodePort and ClusterIP Services.

        -   Create SLB Instance: You can click **Modify** to change the specification of the SLB instance.
        -   Use Existing SLB Instance: You can select an existing SLB instance.
**Note:** You can create an SLB instance or use an existing SLB instance. You can also associate an SLB instance with more than one Service. However, you must take note of the following limits:

        -   If you use an existing SLB instance, the listeners of the SLB instance overwrite those of the Service.
        -   If an SLB instance is created along with a Service, you cannot reuse this SLB instance when you create other Services. Otherwise, the SLB instance may be deleted. You can reuse only SLB instances that are manually created in the console or by calling the API.
        -   An SLB instance must listen on different Service ports if the SLB instance exposes more than one Service. Otherwise, port conflicts may occur.
        -   When you use an SLB instance, the names of listeners and vServer groups are used as unique identifiers in Kubernetes. Do not modify the names of listeners and vServer groups.
        -   You cannot use an SLB instance to expose Services across clusters. |
    |**Backend**|Select the backend application that you want to associate with the Service. If you do not associate the Service with a backend, no Endpoint object is created. You can also manually associate the Service with a backend. For more information, see, see [services-without-selectors](https://kubernetes.io/docs/concepts/services-networking/service/#services-without-selectors).|
    |**External Traffic Policy**|Select a policy to distribute external traffic.     -   **Local**: This policy routes external traffic to only pods on the node where the Service is deployed.
    -   **Cluster**: This policy routes traffic to pods where the Service is deployed and also to pods on other nodes.
**Note:** The **External Traffic Policy** parameter is available only if you set Type to **Node Port** or **Server Load Balancer**. |
    |**Port Mapping**|Specify a Service port and a container port. The Service port corresponds to the `port` field in the YAML file and the container port corresponds to the `targetPort` field in the YAML file. The container port must be the same as the one that is exposed in the backend pod.|
    |**Annotations**|Add annotations for the Service to modify the configurations of the SLB instance. You can select **Custom Annotation** or **Alibaba Cloud Annotation** from the Type drop-down list. For example, the annotation `service.beta.kubernetes.io/alicloud-loadbalancer-bandwidth:2` specifies that the maximum bandwidth of the Service is 2 Mbit/s. This limits the amount of traffic that flows through the Service. For more information, see [Use annotations to configure load balancing](/intl.en-US/User Guide for Kubernetes Clusters/Network/Service Management/Use annotations to configure load balancing.md).|
    |**Label**|Add one or more labels to the Service. The labels are used to identify the Service.|

7.  Click **Create**.

    On the Services page, you can view the created Service in the Service list.

    ![The Service list](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/6345359951/p11027.png)


**Update a Service**

1.  In the left-side navigation pane of the details page, choose **Network** \> **Services**.

2.  On the Services page, find the Service that you want to update and click **Update** in the **Actions** column.

3.  In the **Update Service** dialog box, set the parameters and click **Update**.

    ![Update the Service](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/8345359951/p11041.png)

4.  In the Service list, find the Service that you updated and click **Details** in the Actions column to view configuration changes.


**View a Service**

1.  In the left-side navigation pane of the details page, choose **Network** \> **Services**.

2.  Select a cluster and a namespace, find the Service that you want to view, and then click **Details** in the Actions column.

    You can view information about the Service, such as the name, type, creation time, cluster IP address, and external endpoint. In this example, you can view the external endpoint \(the IP address and port\) of the Service, as shown in the following figure. To access the NGINX application, click this IP address.

    ![Service details](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/9357159161/p246164.png)


## Manage Services by using kubectl

**YAML template** of a Service

```
apiVersion: v1
kind: Service
metadata:
  annotations:
    service.beta.kubernetes.io/alibaba-cloud-loadbalancer-address-type: "intranet"
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

|Field|Description|
|-----|-----------|
|`kind`|Specifies that the resource object is a Service.|
|`metadata`|Defines the basic information about the Service, such the name, labels, and namespace.|
|`metadata.annotations`|ACK provides rich annotations for you to configure load balancing. In this example, the annotation `service.beta.kubernetes.io/alibaba-cloud-loadbalancer-address-type` is set to `intranet`, which specifies that the Service is accessible over the internal network. For more information, see [Use annotations to configure load balancing](/intl.en-US/User Guide for Kubernetes Clusters/Network/Service Management/Use annotations to configure load balancing.md).|
|`spec.selector`|Defines the label selector of the Service. The Service exposes the pods with labels that match the label selector.|
|`spec.ports.port`|Specifies the Service port that is exposed to the cluster IP address. You can access the Service from within the cluster by sending requests to `clusterIP:port`.|
|`spec.ports.targetPort`|Specifies the port of the backend pod to receive traffic. The traffic that flows through the Service `port` is forwarded by kube-proxy to the port \(specified by `targetPort`\) of the backend pod and then transmitted to the containers.|
|`spec.type`|Defines how the Service is accessed. -   `LoadBalancer`: The Service is exposed by using an SLB instance. If you do not associate an existing SLB instance with the Service, the system automatically creates one. By default, the automatically created SLB instance is Internet-facing. You can set `service.beta.kubernetes.io/alibaba-cloud-loadbalancer-address-type` to `intranet` to create a Service for internal access and an internal-facing SLB instance for this Service. For more information, see [Use an existing SLB instance to expose an application](/intl.en-US/User Guide for Kubernetes Clusters/Network/Service Management/Use an existing SLB instance to expose an application.md) and [Use an automatically created SLB instance to expose an application](/intl.en-US/User Guide for Kubernetes Clusters/Network/Service Management/Use an automatically created SLB instance to expose an application.md).
-   `ClusterIP`: exposes the Service within the cluster. A ClusterIP Service is accessible from within the cluster.
-   `NodePort`: maps a node port to the backend Service. You can access the Service from outside the cluster by sending requests to `NodeIP:NodePort`.
-   `ExternalName`: maps the Service to a DNS server. |

**Create a Service**

1.  Create a YAML file. For more information, see the preceding [YAML template](#p_21p_brv_kge).

    In the following example, a YAML file named my-nginx-svc.yaml is created.

2.  Connect to the cluster by using kubectl or Cloud Shell. For more information, see [t16645.md\#](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Access clusters/Connect to Kubernetes clusters by using kubectl.md) and [Use kubectl on Cloud Shell to manage ACK clusters](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Access clusters/Use kubectl on Cloud Shell to manage ACK clusters.md).

3.  Run the following command to create a Service:

    ```
    kubectl apply -f my-nginx-svc.yaml
    ```

4.  Run the following command to check whether the Service is created:

    ```
    kubectl get svc my-nginx-svc
    ```

    Sample output:

    ```
    NAME           TYPE           CLUSTER-IP    EXTERNAL-IP      PORT(S)        AGE
    my-nginx-svc   LoadBalancer   172.21.XX.XX   192.168.XX.XX     80:31599/TCP   5m
    ```


**Update a Service**

-   Method 1: Run the following command to update a Service:

    ```
    kubectl edit service my-nginx-svc
    ```

-   Method 2: Manually delete a Service, modify the YAML file, and then recreate the Service.

    ```
    kubectl apply -f my-nginx-svc.yaml
    ```


**View a Service**

Run the following command to view a Service:

```
kubectl get service my-nginx-svc
```

Sample output:

```
NAME           TYPE           CLUSTER-IP    EXTERNAL-IP      PORT(S)        AGE
my-nginx-svc   LoadBalancer   172.21.XX.XX   192.168.XX.XX     80:31599/TCP   5m
```

**Delete a Service**

Run the following command to delete a Service:

```
kubectl delete service my-nginx-svc
```

