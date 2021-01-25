---
keyword: [create Services, delete Services, update Services]
---

# Manage Services

Each pod in Kubernetes clusters has its own IP address. However, pods are frequently created and deleted. As a result, you cannot expose pods to enable external access. The concept of Services decouples frontends from backends and makes loosely-coupled microservices possible. This topic describes how to create, update, and delete Services by using the Container Service for Kubernetes \(ACK\) console and Kubectl.

## Manage Services in the ACK console

**Create a Service**

1.  Log on to the [Container Service for Kubernetes \(ACK\) console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane, click **Clusters**.

3.  On the Clusters page, find the cluster that you want to manage and click **Details** in the **Actions** column.

4.  In the left-side navigation pane, click **Services**.

5.  On the Services page, click **Create** in the upper-right corner of the page.

6.  In the Create Service dialog box, set the required parameters.

    |Parameter|Description|
    |---------|-----------|
    |**Name**|The name of the Service.|
    |**Type**|The type of the Service. This parameter determines how the Service is accessed. Valid values:    -   Cluster IP: exposes the Service by using the internal IP address of the cluster. This is the default value. If you select this option, the Service is accessible only from within the cluster.

**Note:** The **Headless Service** checkbox is available only when you set Type to **Cluster IP**. If you select this check box, you can use a headless Service to interface with other service discovery mechanisms, without being tied to the implementation of Kubernetes.

    -   Node Port: enables access to the Service by using the IP address and static port on each node. The NodePort field specifies the static port. A NodePort Service can be used to route requests to a ClusterIP Service. The ClusterIP Service is automatically created by the system. You can access a NodePort Service from outside the cluster by sending requests to `<NodeIP>:<NodePort>`.
    -   Server Load Balancer: exposes the Service by using a Server Load Balancer \(SLB\) instance. If you select this option, you can enable internal or external access to the Service. LoadBalancer Services can be used to route requests to NodePort and ClusterIP Services.

        -   Create SLB Instance: You can click **Modify** to change the specification of the SLB instance.
        -   Use Existing SLB Instance: You can select an existing SLB instance.
**Note:** You can create an SLB instance or use an existing SLB instance. You can also associate an SLB instance with more than one Service. However, you must take note of the following limits:

        -   If you use an existing SLB instance, the listeners of the SLB instance are overwritten.
        -   If the SLB instance is created when you create a Service, you cannot use this SLB instance to expose other Services. Otherwise, the SLB instance may be accidentally deleted. The SLB instance must be manually created in the console or by calling the API.
        -   The SLB instance must listen on different Service ports if the SLB instance exposes more than one Service. Otherwise, port conflicts may occur.
        -   When you use the SLB instance, the names of listeners and vServer groups are used as unique identifiers in Kubernetes. Do not modify the names of listeners and vServer groups.
        -   You cannot use an SLB instance to expose Services across clusters. |
    |**Backend**|Select the backend application that you want to associate with the Service. If you do not associate the Service with a backend, no Endpoint object is created. You can manually associate the Service with a backend. For more information, see, see [services-without-selectors](https://kubernetes.io/docs/concepts/services-networking/service/#services-without-selectors).|
    |**External Traffic Policy**|Select a policy to distribute external network traffic.    -   **Local**: routes network traffic to only the node where the Service is deployed.
    -   **Cluster**: routes network traffic to pods on other nodes.
**Note:** The **External Traffic Policy** parameter is available only when you set Type to **Node Port** or **Server Load Balancer**. |
    |**Port Mapping**|Specify a Service port and a container port. The Service port corresponds to `port` in the YAML file and the container port corresponds to `targetPort` in the YAML file. The container port must be the same as the one that is exposed in the backend pod.|
    |**Annotations**|Add one or more annotations to the Service to modify the configuration of the SLB instance. You can select **Custom Annotation** or **Alibaba Cloud Annotation** from the Type drop-down list. For example, the annotation `service.beta.kubernetes.io/alicloud-loadbalancer-bandwidth:2` specifies that the maximum bandwidth of the Service is 2 Mbit/s. This limits the amount of traffic that flows through the Service. For more information, see [Use annotations to configure SLB instances](/intl.en-US/User Guide for Kubernetes Clusters/Network management/Service Management/Use annotations to configure SLB instances.md).|
    |**Label**|Add one or more labels to the Service. The labels are used to identify the Service.|

7.  Click **Create**.

    On the Services page, you can view the created Service in the Service list.

    ![Service list](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/6345359951/p11027.png)


**Update a Service**

1.  In the left-side navigation pane, click **Services**.

2.  On the Services page, find the Service that you want to update and click **Update** in the **Actions** column.

3.  In the **Update Service** dialog box, set the required parameters and click **Update**.

    ![Update the Service](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/8345359951/p11041.png)

4.  In the Service list, find the Service that you updated and click **Details** in the Actions column to view configuration changes.


## Manage Services by using Kubectl

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
|`kind`|Specifies that the resource object is Service.|
|`metadata`|Defines the name, label, and namespace of the Service.|
|`metadata.annotations`|ACK provides rich annotations for you to configure SLB instances. For example, the annotation `service.beta.kubernetes.io/alibaba-cloud-loadbalancer-address-type` specifies that the Service is accessible over the `intranet` \(internal network\) by using an internal-facing SLB instance. For more information, see [Use annotations to configure SLB instances](/intl.en-US/User Guide for Kubernetes Clusters/Network management/Service Management/Use annotations to configure SLB instances.md).|
|`spec.selector`|Defines the selector of the Service. The Service exposes the pods whose labels match the selector.|
|`spec.ports.port`|Specifies the port that is exposed by the Service to the Cluster IP. You can access the Service from within the cluster by sending requests to `clusterIP:port`.|
|`spec.ports.targetPort`|Specifies the port of the backend pod. The traffic that flows through the exposed `port` is forwarded by kube-proxy to the `targetPort` of the backend pod and then transmitted to the containers.|
|`spec.type`|Defines how the Service is accessed.-   `LoadBalancer`: The Service is exposed by using an SLB instance. If you do not associate an existing SLB instance with the Service, the system automatically creates an SLB instance. By default, the network type of the automatically created SLB instance is Internet. You can set `service.beta.kubernetes.io/alibaba-cloud-loadbalancer-address-type` to `intranet` to create an internal-facing SLB instance and a Service that uses the SLB instance. For more information, see [Access services by using SLB instances](/intl.en-US/User Guide for Kubernetes Clusters/Network management/Service Management/Access services by using SLB instances.md) and [t2020446.md\#]().
-   `ClusterIP`: exposes the Service within the cluster. The Service is accessible from within the cluster.
-   `NodePort`: maps the node port to the backend Service. You can access the Service from outside the cluster by using the connect string `IP:NodePort`.
-   `ExternalName`: maps the Service to a DNS server. |

**Create a Service**

1.  Create a YAML file. For more information, see the preceding [YAML template](#p_21p_brv_kge).

    A my-nginx-svc.yaml file is used as an example.

2.  Use Kubectl or Cloud Shell to connect to the cluster. For more information, see [Use kubectl to connect to an ACK cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster management/Access clusters/Use kubectl to connect to an ACK cluster.md) and [Use kubectl on Cloud Shell to manage ACK clusters](/intl.en-US/User Guide for Kubernetes Clusters/Cluster management/Access clusters/Use kubectl on Cloud Shell to manage ACK clusters.md).

3.  Run the following command to create a Service:

    ```
    kubectl apply -f my-nginx-svc.yaml
    ```

4.  Run the following command to check whether the Service is created:

    ```
    kubectl get svc my-nginx-svc
    ```

    Sample response:

    ```
    NAME           TYPE           CLUSTER-IP    EXTERNAL-IP      PORT(S)        AGE
    my-nginx-svc   LoadBalancer   172.21.8.22   192.168.0.60     80:31599/TCP   5m
    ```


**Update a Service**

-   Method 1: Run the following command to update a Service.

    ```
    kubectl edit service my-nginx-svc
    ```

-   Method 2: Manually delete a Service, modify the YAML file, and then recreate the Service.

    ```
    kubectl apply -f my-nginx-svc.yaml
    ```


**Delete a Service**

Run the following command to delete a Service:

```
kubectl delete service my-nginx-svc
```

