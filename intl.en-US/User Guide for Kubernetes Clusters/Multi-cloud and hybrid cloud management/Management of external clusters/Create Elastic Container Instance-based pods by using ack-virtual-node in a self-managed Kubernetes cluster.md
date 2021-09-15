---
keyword: [self-managed Kubernetes cluster, virtual nodes, elastic workloads, ack-virtual-node]
---

# Create Elastic Container Instance-based pods by using ack-virtual-node in a self-managed Kubernetes cluster

Virtual nodes enable seamless integration between Kubernetes and Elastic Container Instance. Virtual nodes empower Kubernetes clusters with high elasticity. This way, Kubernetes clusters are no longer limited by the computing capacity of cluster nodes. You can dynamically create Elastic Container Instance-based pods to meet your business requirements. This saves the trouble of cluster sizing. This topic describes virtual nodes and Elastic Container Instance. This topic also describes how to create Elastic Container Instance-based pods by using ack-virtual-node.

-   A cluster registration proxy is created and a self-managed cluster whose Kubernetes version is later than 1.14 is connected to the cluster registration proxy. For more information, see [Create a cluster registration proxy and register an on-premises cluster](/intl.en-US/User Guide for Kubernetes Clusters/Multi-cloud and hybrid cloud management/Management of external clusters/Create a cluster registration proxy and register an on-premises cluster.md).
-   Elastic Container Instance is activated. You can log on to the [Elastic Container Instance console](https://eci.console.aliyun.com/) to activate the service.
-   The region where the cluster is deployed is supported by Elastic Container Instance. To view the supported regions and zones, log on to the [Elastic Container Instance console](https://eci.console.aliyun.com/).

## Virtual nodes and elastic container instances

[Elastic Container Instance](https://www.alibabacloud.com/zh/products/elastic-container-instance) is a serverless compute service that is provided by Alibaba Cloud for containerization. You can use elastic container instances to set up an O&M-free and isolated runtime environment for your containers. Elastic container instances allow you to focus on containerized applications without the need to purchase or manage Elastic Compute Service \(ECS\) instances. This way, you do not need to perform infrastructure maintenance. You can create elastic container instances to meet your business requirements. You are charged for resource usage on a per second basis.

Virtual nodes can drastically reduce computing costs and improve scaling efficiency. Virtual nodes are suitable in the following scenarios:

-   Online business that requires elastic scaling to withstand traffic fluctuations, such as online education and e-commerce. Virtual nodes optimize the maintenance of resource pools. This helps you reduce computing costs.
-   Virtual nodes can reduce costs in computing scenarios where Spark or Presto is used to process data.
-   Continuous integration and continuous delivery \(CI/CD\) pipeline: Jenkins and GitLab Runner.
-   Jobs: Jobs in AI computing scenarios and CronJobs.

Container Service for Kubernetes \(ACK\) provides multiple serverless container services based on virtual nodes and elastic container instances. These services include serverless Kubernetes \(ASK\) and ACK on Elastic Container Instance. You can use these services to deploy elastic and maintenance-free workloads.

![virtual node](https://help-static-aliyun-doc.aliyuncs.com/assets/img/en-US/9265359951/p85192.png)

## Step 1: Grant a RAM user the permissions to access ack-virtual-node

In the **Agent for Connecting to the Cluster** section of the cluster registration proxy, if you find that the registered Kubernetes cluster is connected to the proxy through an internal network, you can skip this step. ack-virtual-node is managed by the registered Kubernetes cluster. For more information, see [Connect an external Kubernetes cluster to the cluster registration proxy](/intl.en-US/User Guide for Kubernetes Clusters/Multi-cloud and hybrid cloud management/Management of external clusters/Create a cluster registration proxy and register an on-premises cluster.mdstep_dl9_6s6_2vx).

Before you can install the component in a registered cluster, you must specify the AccessKey pair to authenticate requests that are sent to cloud resources. Before you set the AccessKey pair, create a Resource Access Management \(RAM\) user and grant the RAM user the permissions to access Alibaba Cloud resources.

1.  Create a RAM user. For more information, see [Create a RAM user](/intl.en-US/RAM User Management/Basic operations/Create a RAM user.md).

2.  Create a custom permission policy.

    For more information about how to create a custom permission policy, see [Create a custom policy](/intl.en-US/Policy Management/Custom policies/Create a custom policy.md).

    Use the following template to create a custom permission policy:

    ```
    {
        "Version": "1",
        "Statement": [
            {
                "Action": [
                    "eci:CreateContainerGroup",
                    "eci:DeleteContainerGroup",
                    "eci:DescribeContainerGroups",
                    "eci:DescribeContainerLog",
                    "eci:UpdateContainerGroup",
                    "eci:UpdateContainerGroupByTemplate",
                    "eci:CreateContainerGroupFromTemplate",
                    "eci:RestartContainerGroup",
                    "eci:ExportContainerGroupTemplate",
                    "eci:DescribeContainerGroupMetric",
                    "eci:DescribeMultiContainerGroupMetric",
                    "eci:ExecContainerCommand",
                    "eci:CreateImageCache",
                    "eci:DescribeImageCaches",
                    "eci:DeleteImageCache"
                ],
                "Resource": [
                    "*"
                ],
                "Effect": "Allow"
            }
        ]
    }
    ```

3.  Grant permissions to the RAM user For more information, see [Grant permissions to a RAM user](/intl.en-US/RAM User Management/Authorization management/Grant permissions to a RAM user.md).

    You can create a custom permission policy or select the AliyunECIFullAccess permission policy to grant the permissions to the RAM user.

4.  Create an AccessKey pair for the RAM user. For more information, see [Obtain an AccessKey pair]().

5.  Use the AccessKey pair to create a Secret named alibaba-addon-secret in the registered cluster.

    When you install ack-virtual-node in the registered cluster, the AccessKey pair is automatically referenced to access Alibaba Cloud resources.

    ```
    kubectl -n kube-system create secret generic alibaba-addon-secret --from-literal='access-key-id=<your access key id>' --from-literal='access-key-secret=<your access key secret>'
    ```

    **Note:** Replace `<your access key id>` and `<your access key secret>` with the AccessKey pair that you obtained.


## Step 2: Install ack-virtual-node in the registered cluster

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane of the ACK console, click **Clusters**.

3.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  In the left-side navigation pane of the details page, choose **Operations** \> **Add-ons**.

5.  Click the **Others** tab, find ack-virtual-node, and then click **Install**.

    In the Note message, click **OK**. The default vSwitch and security group of the cluster are used for elastic container instances that are deployed by ack-virtual-node. If you want to modify these settings, see [What to do next](#section_d8s_1d3_sui).


## Step 3: Create Elastic Container Instance-based pods

You can use one of the following methods to create an Elastic Container Instance-based pod in the registered cluster:

-   **Method 1: Add labels to pods**.

    Add the `alibabacloud.com/eci=true` label to the pod that you want to create. Then, an Elastic Container Instance-based pod is created and scheduled to a virtual node. Examples:

    1.  Run the following command to add a label to the pod:

        ```
        kubectl run nginx --image nginx -l alibabacloud.com/eci=true
        ```

    2.  Run the following command to query the pod:

        ```
        kubectl get pod -o wide|grep virtual-kubelet
        ```

        Expected output:

        ```
        nginx-7fc9f746b6-r4xgx     0/1     ContainerCreating   0          20s   192.168.XX.XX   virtual-kubelet        <none>           <none>
        ```

-   **Method 2: Add labels to namespaces**.

    Add the `alibabacloud.com/eci=true` label to the namespace to which the pod belongs. Then, an Elastic Container Instance-based pod is created and scheduled to the virtual node. Example:

    1.  Run the following command to create a virtual node:

        ```
        kubectl create ns vk
        ```

    2.  Run the following command to add a label to the namespace to which the pod belongs:

        ```
        kubectl label namespace vk alibabacloud.com/eci=true
        ```

    3.  Run the following command to schedule the pod to the virtual node:

        ```
        kubectl -n vk run nginx --image nginx
        ```

    4.  Run the following command to query the pod:

        ```
        kubectl -n vk get pod -o wide|grep virtual-kubelet
        ```

        Expected output:

        ```
        nginx-6f489b847d-vgj4d      1/1     Running             0          1m   192.168.XX.XX   virtual-kubelet        <none>           <none>
        ```


## What to do next

**Modify the configurations of the virtual node**

The configurations of the virtual node controller determine how Elastic Container Instance-based pods are scheduled to a virtual node and specify the runtime environment of the pod, such as vSwitches and security group settings. You can modify the configurations of the virtual node controller to meet your business requirements. Modified configurations apply only to pods that are scheduled after modifications and do not apply to existing pods that run on the node.

Run the following command to modify the configurations of the virtual node controller:

```
kubectl -n kube-system edit deployment ack-virtual-node-controller
```

The following list describes some operations that are commonly performed to modify the configurations of the virtual node controller:

-   **Upgrade the version of the virtual node controller**

    To use the latest features of virtual nodes, you must upgrade the virtual node controller to the latest version.

-   **Modify security group settings \(ECI\_SECURITY\_GROUP\)**

    You can modify the ECI\_SECURITY\_GROUP environment variable to change the security group of the pods that are scheduled to the virtual node.

-   **Modify vSwitch settings \(ECI\_VSWITCH\)**

    You can modify the ECI\_VSWITCH environment variable to change the vSwitch of the pods that are scheduled to the virtual node. We recommend that you configure multiple vSwitches that are deployed in different zones to ensure high availability. When elastic container instances in the current zone are out of stock, the virtual node controller creates pods in another zone.

-   **Modify kube-proxy settings \(ECI\_KUBE\_PROXY\)**.

    By default, the ECI\_KUBE\_PROXY environment variable is set to true. This indicates that pods can access ClusterIP Services. If the pods no longer need to access ClusterIP Services, you can set the environment variable to **false** to disable kube-proxy. In large-scale deployment scenarios, a cluster may need to start a large number of pods. This significantly increases the number of concurrent connections between kube-proxy and the Kubernetes API server. In this case, you can disable kube-proxy to reduce the heavy loads on the API server.

-   **Modify the kube-system/eci-profile ConfigMap**.

    You can modify the kube-system/eci-profile ConfigMap to specify more parameters for elastic container instances, such as vSwitches and security groups.


**Delete a virtual node**

1.  Uninstall ack-virtual-node.

    After you delete all the pods in a registered cluster, you can uninstall ack-virtual-node on the Add-ons page.

2.  Run the `kubectl delete no` command to delete related virtual nodes.

    **Note:** If you do not delete the Elastic Container Instance-based pods in the cluster before you uninstall ack-virtual-node, the elastic container instances are retained in the cluster.


**Related topics**  


[Overview of registered clusters](/intl.en-US/User Guide for Kubernetes Clusters/Multi-cloud and hybrid cloud management/Management of external clusters/Overview of registered clusters.md)

[Deploy the virtual node controller and use it to create Elastic Container Instance-based pods](/intl.en-US/User Guide for Kubernetes Clusters/Virtual nodes and ECI/Deploy the virtual node controller and use it to create Elastic Container Instance-based pods.md)

[Use an elastic container instance to run a Job](/intl.en-US/User Guide for Kubernetes Clusters/Virtual nodes and ECI/Use an elastic container instance to run a Job.md)

