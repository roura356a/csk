---
keyword: [virtual node, virtual node add-on, create an ECI]
---

# Deploy ack-virtual-node in an ACK cluster

This topic describes virtual nodes and elastic container instances \(ECIs\). It also describes how to deploy a virtual node add-on in a cluster of Container Service for Kubernetes \(ACK\). You can schedule a pod to a virtual node by using ack-virtual-node.

## Virtual nodes and ECI

[ECI](https://www.alibabacloud.com/zh/products/elastic-container-instance) is a serverless compute service that is provided by Alibaba Cloud for containerized business. You can use ECI to quickly set up an operations and maintenance \(O&M\)-free and isolated runtime environment for your application containers. ECI allows you to focus on container-based applications without the need to purchase or manage Elastic Compute Service \(ECS\) instances. This saves you the hassle of infrastructure maintenance. You can create ECIs based on your business requirements. You are charged for resource usage on a per second basis.

Based on Virtual Kubelet, virtual nodes enable seamless integration between Kubernetes and ECI. This significantly improves the elasticity of Kubernetes clusters and eliminates the constraint of the computing capacity that is provided by cluster nodes. For more information about how Virtual Kubelet works and its architecture, see [Virtual Kubelet](https://virtual-kubelet.io/docs/architecture/).

Virtual nodes allow you to dynamically create pods based on your business requirements and provide increased flexibility without the trouble of cluster capacity sizing. You can use virtual nodes in the following scenarios to significantly reduce computing costs and improve computing efficiency.

-   Online business with periodic traffic patterns, such as online education and e-commerce. Virtual nodes significantly reduce computing costs by optimizing resource pool maintenance.
-   Virtual nodes effectively reduce costs in computing scenarios where Spark or Presto is used to process data.
-   CI/CD Pipeline: Jenkins and Gitlab-Runner.
-   Jobs: Jobs in Artificial Intelligence \(AI\) scenarios and CronJobs.

Based on virtual nodes and ECI, ACK provides multiple serverless container services, such as serverless Kubernetes \(ASK\) and ACK on ECI. These services meet your business requirements in various elastic computing scenarios.

![virtual node](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/9265359951/p85192.png)

## Deploy ack-virtual-node in an ACK cluster

**Note:** In ASK clusters, you can directly create ECIs without the need to deploy ack-virtual-node. In managed or dedicated Kubernetes clusters, you must deploy ack-virtual-node before you can create an ECI.

Prerequisites:

-   A managed Kubernetes cluster or dedicated Kubernetes cluster is created. For more information, see [Create a managed kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster management/Create Kubernetes clusters/Create a managed kubernetes cluster.md).
-   ECI is activated. You can log on to the [ECI console](https://eci.console.aliyun.com/) to activate ECI.
-   The region where the created cluster is deployed must be supported by ECI. To view the supported regions and zones, go to the [ECI console](https://eci.console.aliyun.com/).

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane, choose **Marketplace** \> **App Catalog**.

3.  On the **App Catalog** page, click the **Alibaba Cloud Apps** tab and find and click **ack-virtual-node**.

    In the upper-right corner of the **App Catalog** page, you can enter **ack-virtual-node** into the Name search bar and click the search icon. You can also enter a keyword to perform a fuzzy match.

4.  On the App Catalog - ack-virtual-node page, select the created cluster in the Deploy section to deploy ack-virtual-node.

    **Namespace** is automatically set to kube-system. **Release Name** is automatically set to ack-virtual-node.

5.  On the App Catalog - ack-virtual-node page, click the **Parameters** tab and set the parameters. Then, click **Create** in the Deploy section.

    ![Deploy ack-virtual-node](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/9265359951/p47256.png)

    |Parameter|Description|How to obtain the value|
    |---------|-----------|-----------------------|
    |ALIYUN\_CLUSTERID|The ID of the cluster where you want to deploy ack-virtual-node|On the details page of the cluster, click the Basic Information tab. In the **Basic Information** section, you can obtain the **ID of the cluster**.|
    |ALIYUN\_RESOURCEGROUP\_ID|The ID of the resource group to which the cluster belongs|If you do not specify the parameter, the default resource group is used. To specify a resource group, log on to the [Resource Management console](https://resourcemanager.console.aliyun.com/) to obtain the **ID of the resource group**.|
    |ECI\_REGION|The name of the region where the cluster is deployed|On the details page of the cluster, click the Basic Information tab. In the **Basic Information** section, you can check the **region** where the cluster is deployed.**Note:** For example, cn-hangzhou indicates the China \(Hangzhou\) region. |
    |ECI\_VSWITCH|vSwitches|On the Nodes page, click the ID of a node. On the **instance details** page, you can obtain the value of VSwtich in the **Configuration Information** section. **Note:**

Make sure that the zone of the vSwitch is supported by ECI.

vSwitches support multiple zones. You can specify multiple vSwitches in the format of `ECI_VSWITCH: "vsw-xxxxxxx1, vsw-xxxxxxx2, vsw-xxxxxxx3"`. |
    |ECI\_SECURITY\_GROUP|The ID of the security group to which nodes in the cluster belong|On the Nodes page, click the ID of a node. In the left-side navigation pane, click **Security Groups**. Click the **Security Groups** tab to obtain the ID of the security group.|
    |ECI\_ACCESS\_KEY|The AccessKey ID of your Alibaba Cloud account|For more information, see [Obtain an AccessKey pair]().You must attach the **AliyunECIReadOnlyAccess** policy to your account in the Resource Access Management \(RAM\) console. For more information, see [Grant permissions to a RAM user](). |
    |ECI\_SECRET\_KEY|The AccessKey secret of your Alibaba Cloud account|For more information, see [Obtain an AccessKey pair]().You must attach the **AliyunECIReadOnlyAccess** policy to your account in the RAM console. For more information, see [Grant permissions to a RAM user](). |

6.  After ack-virtual-node is deployed, you can view information about the newly added node **virtual-node-eci** by performing the following steps:

    1.  In the left-side navigation pane, click **Clusters**.

    2.  On the Clusters page, click the name of a cluster or click **Applications** in the **Actions** column.

    3.  In the left-side navigation pane, click **Nodes**. On the Nodes page, you can find the newly added node **virtual-node-eci**.

        ![Add a virtual node](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/0357693061/p146959.png)

7.  Run the following commands to query the deployment result of **virtual-node-controller** and **virtual-node-admission-controller**: For more information, see [Use kubectl on Cloud Shell to manage ACK clusters](/intl.en-US/User Guide for Kubernetes Clusters/Cluster management/Access clusters/Use kubectl on Cloud Shell to manage ACK clusters.md).

    Run the following command:

    ```
    kubectl -n kube-system get statefulset virtual-node-eci
    ```

    The following output is returned:

    ```
    NAME              READY   AGE
    virtual-node-eci  1/1     1m
    ```

    Run the following command:

    ```
    kubectl -n kube-system get deploy ack-virtual-node-affinity-admission-controller
    ```

    The following output is returned:

    ```
    NAME                                             READY   UP-TO-DATE   AVAILABLE   AGE
    ack-virtual-node-affinity-admission-controller   1/1     1            1           1m
    ```

    Run the following command:

    ```
    kubectl -n kube-system get pod|grep virtual-node-eci
    ```

    The following output is returned:

    ```
    virtual-node-eci-0                                                1/1     Running   0          1m
    ```

    Run the following command:

    ```
    kubectl get no|grep virtual-node-eci
    ```

    The following output is returned:

    ```
    virtual-node-eci-0        Ready    agent    1m   v1.11.2-aliyun-1.0.207
    ```


## Schedule a pod to a virtual node

**Note:** The following operations are not applicable to ASK clusters.

If your cluster contains a virtual node, you can schedule a pod to the virtual node. After the pod is scheduled, the virtual node controller automatically creates a pod with the same configurations on the virtual node. You can use the following methods to schedule a pod to a virtual node:

-   **Set node selectors and tolerations for the pod**.

    Virtual nodes have specific taints. You must set node selectors and tolerations for a pod before you can schedule the pod to a virtual node. The following code block is a YAML template that is used to create a pod:

    ```
    apiVersion: v1
    kind: Pod
    metadata:
      name: nginx
    spec:
      containers:
      - image: nginx
        imagePullPolicy: Always
        name: nginx
      nodeSelector:
        type: virtual-kubelet
      tolerations:
      - key: virtual-kubelet.io/provider
        operator: Exists
    ```

-   **Attach labels to the pod**.

    The **virtual-node-affinity-admission-controller** webhook automatically schedules pods with specified labels to a virtual node. The eci=true label is used in the following example. Example:

    Run the following commands:

    ```
    kubectl run nginx --image nginx -l eci=true
    ```

    ```
    kubectl get pod -o wide|grep virtual-node-eci
    ```

    The following output is returned:

    ```
    nginx-7fc9f746b6-r4xgx     0/1     ContainerCreating   0          20s   192.168. *.*   virtual-node-eci-0        <none>           <none>
    ```

-   **Add a label to the namespace where the pod is deployed**.

    The **virtual-node-affinity-admission-controller** webhook automatically schedules pods that are created in a namespace with specified labels to a virtual node. The virtual-node-affinity-injection=enabled label is used in the following example. Example:

    Run the following commands:

    ```
    kubectl create ns vk
    ```

    ```
    kubectl label namespace vk virtual-node-affinity-injection=enabled
    ```

    ```
    kubectl -n vk run nginx --image nginx
    ```

    ```
    kubectl -n vk get pod -o wide|grep virtual-node-eci
    ```

    The following output is returned:

    ```
    nginx-6f489b847d-vgj4d      1/1     Running             0          1m   192.168. *.*   virtual-node-eci-0        <none>           <none>
    ```


## Modify the configurations of the virtual node controller

**Note:** The following operations are not applicable to ASK clusters.

The configurations of the virtual node controller determine how pods are scheduled to a virtual node and specify the pod runtime environment such as vSwitches and security group settings. You can modify the configurations of the controller based on your business requirements. Modified configurations apply to only pods that are scheduled after modifications and do not apply to existing pods that run on the node.

You can run the following command to modify the configurations of the controller:

```
`kubectl -n kube-system edit statefulset virtual-node-eci`
```

The following content describes common modifications:

-   **Upgrade the virtual node controller version**.

    To use the latest features of virtual nodes, you must upgrade the virtual node controller to the latest version. For example, to enable pods to access ClusterIP type Services, the virtual node controller version must be later than v1.0.0.2-aliyun.

-   **Modify security group settings**.

    You can modify the ECI\_SECURITY\_GROUP environment variable to change the security group that is associated with pods that are scheduled to the virtual node.

-   **Modify vSwitch settings**.

    You can modify the ECI\_VSWITCH environment variable to change the vSwitch to which pods that are scheduled to the virtual node belong. We recommend that you configure multiple vSwitches that are deployed in different zones to ensure high availability. When ECI resources are insufficient in a zone, the controller can create pods in another zone.

-   **Modify kube-proxy settings**.

    By default, the ECI\_KUBE\_PROXY environment variable is set to **true**. This indicates that pods can access ClusterIP type Services. If the pods no longer need to access ClusterIP type Services, you can set the environment variable to **false** to disable kube-proxy. In some large-scale scenarios, a cluster may need to start a large number of pods. This significantly increases the number of concurrent connections between kube-proxy and the Kubernetes API server. In this case, you can disable kube-proxy to reduce the heavy loads on the API server. However, you can also configure PrivateZone to enable pods to access Services in the cluster.

-   **Create multiple virtual nodes**.

    We recommend that you deploy up to 3,000 pods on a virtual node. To create more pods, you can create more virtual nodes. You can modify the number of replicas in the StatefulSet configurations to create more virtual nodes. The number of replicas represents the number of virtual nodes in the cluster. Each virtual node is related to a virtual node controller that manages the pods on the virtual node. The controllers do not interfere with each other. Perform the following steps to modify the StatefulSet configurations:

    Run the following command:

    ```
    kubectl -n kube-system scale statefulset virtual-node-eci --replicas=4
    ```

    The following output is returned:

    ```
    statefulset.apps/virtual-node-eci scaled
    ```

    Run the following command:

    ```
    kubectl get no
    ```

    The following output is returned:

    ```
    NAME                            STATUS     ROLES    AGE     VERSION
    cn-hangzhou.192.168.1.1         Ready      <none>   63d     v1.12.6-aliyun.1
    cn-hangzhou.192.168.1.2         Ready      <none>   63d     v1.12.6-aliyun.1
    virtual-node-eci-0              Ready      agent     6m     v1.11.2-aliyun-1.0.207
    virtual-node-eci-1              Ready      agent     1m     v1.11.2-aliyun-1.0.207
    virtual-node-eci-2              Ready      agent     1m     v1.11.2-aliyun-1.0.207
    virtual-node-eci-3              Ready      agent     1m     v1.11.2-aliyun-1.0.207
    ```


## Delete a virtual node

**Note:** The following operations are not applicable to ASK clusters.

In most cases, you do not need to delete virtual nodes because they do not occupy the computing resources of the cluster. To delete a virtual node, we recommend that you manually evict or delete the pods that run on the node before you delete the virtual node controller and the node. If you delete the virtual node controller before pods are deleted, some ECIs may fail to be deleted.

Run the following commands to delete a virtual node:

```
kubectl drain virtual-node-eci-0 ...
```

```
kubectl -n kube-system delete statefulset virtual-node-eci
```

```
kubectl delete no virtual-node-eci-0 ...
```

