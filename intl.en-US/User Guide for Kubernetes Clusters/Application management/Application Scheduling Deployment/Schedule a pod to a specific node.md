---
keyword: [schedule a pod, node label, configure a template]
---

# Schedule a pod to a specific node

You can deploy a management service on a master node to meet your business requirements. Otherwise, we recommend that you deploy specific services on nodes that use standard SSDs. This topic describes how to schedule a pod to a specific node in the Container Service for Kubernetes \(ACK\) console. You can configure node labels or templates to schedule pods.

[Create a managed Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster management/Create Kubernetes clusters/Create a managed Kubernetes cluster.md).

You can configure node labels and set `nodeSelector` to schedule a pod to a specified node. For more information about how to use nodeSelector, see [nodeSelector](https://kubernetes.io/docs/concepts/configuration/assign-pod-node/#nodeselector).

## Step 1: Configure a node label

1.  Log on to the [Container Service for Kubernetes \(ACK\) console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane of the ACK console, click **Clusters**.

3.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  Select the cluster that you want to manage, and click **Manage Labels and Taints** in the upper-right corner of the page to go to the Manage Labels and Taints page.

5.  Select one or more nodes and click **Add Label**.

6.  In the dialog box that appears, set the label name and value of the nodes, and click **OK**.

    -   **Name**: The name of the label. The name can contain letters, digits, hyphens \(-\), underscores \(\_\), and periods \(.\). It must start and end with a letter or a digit.
    -   **Value**: The value of the label. The value can be an empty string or contain letters, digits, hyphens \(-\), underscores \(\_\), and periods \(.\). It must start and end with a letter or a digit.

## Step 2. Schedule a pod to a specified node

1.  In the left-side navigation pane of the ACK console, click **Clusters**.

2.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

3.  In the left-side navigation pane of the details page, choose **Workloads** \> **Deployments**.

4.  On the **Deployments** tab, click **Create from Template**.

5.  Configure the template to deploy a pod.

    -   **Cluster**: Select the cluster where the pod is deployed.
    -   **Namespace**: Select the namespace to which the pod belongs. In this example, the default namespace is selected.
    -   **Sample Template**: In this example, Custom is selected.
    Enter the following template content:

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

6.  Click **Create**. A message appears to indicate the deployment status.


## Verify the scheduling result

You can use one of the following methods to check whether the pod has been deployed to the specified node.

1.  After the pod is deployed, click the pod name that appears in the message to check the pod details.

    You can view the information about the pod labels and the ID of the node to which the pod is scheduled. The following figure indicates that the pod is scheduled to the node with the `group:worker` label.

    ![Check pod details](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/4031073061/p10934.png)

2.  In the left-side navigation pane of the ACK console, click **Clusters**.

3.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  In the left-side navigation pane of the details page, choose **Workloads** \> **Deployments**.

5.  The **Deployments** tab appears. Click the **Pods** tab, find the pod that you want to check, and then click the name of the pod to go to the pod details page.


