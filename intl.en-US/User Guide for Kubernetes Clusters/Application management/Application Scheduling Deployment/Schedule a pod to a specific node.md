---
keyword: [schedule a pod, node label, configure a template]
---

# Schedule a pod to a specific node

You can deploy a management service on a master node to meet your business requirements. You can also deploy specific services on nodes to which standard SSDs are mounted. This topic describes how to schedule a pod to a specific node in the Container Service for Kubernetes \(ACK\) console. You can configure node labels or templates to schedule pods.

[Create a managed Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Create Kubernetes clusters/Create a managed Kubernetes cluster.md).

You can configure node labels and set `nodeSelector` to schedule a pod to a specific node. For more information about how to use node selectors, see [nodeSelector](https://kubernetes.io/docs/concepts/configuration/assign-pod-node/#nodeselector).

## Step 1: Configure a node label

1.  Log on to the [Container Service for Kubernetes \(ACK\) console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane, click **Clusters**.

3.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  In the left-side navigation pane of the details page, choose **Nodes** \> **Node**.

5.  On the Nodes page, click **Manage Labels and Taints** in the upper-right corner of the page. Then, you are redirected to the Manage Labels and Taints page.

6.  On the Labels tab, select one or more nodes and click **Add Label**.

7.  In the Add dialog box, specify **Name** and **Value**, and then click **OK**.

    -   **Name**: the key of the label. The name can contain letters, digits, hyphens \(-\), underscores \(\_\), and periods \(.\).It must start and end with a letter or digit.
    -   **Value**: the value of the label. The value can contain letters, digits, hyphens \(-\), underscores \(\_\), and periods \(.\). You can leave this parameter empty.It must start and end with a letter or digit.

## Step 2. Schedule a pod to a specific node

1.  In the left-side navigation pane, click **Clusters**.

2.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

3.  In the left-side navigation pane of the details page, choose **Workloads** \> **Deployments**.

4.  On the **Deployments** page, click **Create from Template** in the upper-right corner of the page.

5.  Configure the template to deploy a pod.

    -   **Cluster**: Select the cluster where the pod is deployed.
    -   **Namespace**: Select the namespace to which the pod belongs. In this example, the default namespace is selected.
    -   **Sample Template**: In this example, Custom is selected.
    The following template is used as an example:

    ```
     apiVersion: v1
     kind: Pod
     metadata:
       labels:
         name: hello-pod
       name: hello-pod
     spec:
       containers:
         - image: nginx
           imagePullPolicy: IfNotPresent
           name: hello-pod
           ports:
             - containerPort: 8080
               protocol: TCP
           resources: {}
           securityContext:
             capabilities: {}
             privileged: false
           terminationMessagePath: /dev/termination-log
       dnsPolicy: ClusterFirst
       restartPolicy: Always
       nodeSelector:                    
         group: worker                           ##This value must be the same as the node label that is created in Step 1.
       status: {}
    ```

6.  Click **Create**. A message that indicates the deployment status appears.


## Verify the scheduling result

You can use one of the following methods to check whether the pod is deployed on the specified node:

1.  After the pod is deployed, click the pod name that appears in the message and go to the details page of the pod to view detailed information.

    You can view the labels of the pod and the ID of the node to which the pod is scheduled. The following figure indicates that the pod is scheduled to the node with the `group:worker` label.

    ![Check pod details](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/4031073061/p10934.png)

2.  In the left-side navigation pane, click **Clusters**.

3.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  In the left-side navigation pane of the details page, choose **Workloads** \> **Deployments**.

5.  On the **Deployments** page, click the **Pods** tab, find the pod that you want to check, and then click the name of the pod to go to the details page.


