---
keyword: [schedule a pod, node label, configure a pod template]
---

# Schedule pods to specific nodes

In some cases, you may need to deploy pods on a specified node to meet your business requirements. You may also need to deploy pods on nodes that use standard SSDs. This topic describes how to schedule a pod to a specific node in the Container Service for Kubernetes \(ACK\) console. You can configure node labels or pod templates to schedule pods to specific nodes.

[Create a managed Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Create Kubernetes clusters/Create a managed Kubernetes cluster.md).

You can configure node labels and set `nodeSelector` to schedule a pod to a specific node. For more information about how to set nodeSelector, see [nodeSelector](https://kubernetes.io/docs/concepts/configuration/assign-pod-node/#nodeselector).

## Step 1: Configure node labels

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane of the ACK console, click **Clusters**.

3.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  In the left-side navigation pane of the details page, choose **Nodes** \> **Nodes**.

5.  On the Nodes page, click **Manage Labels and Taints** in the upper-right corner to go to the Manage Labels and Taints page.

6.  On the Labels tab, select the nodes that you want to manage and click **Add Label**.

7.  In the Add dialog box, specify **Name** and **Value**, and then click **OK**.

    -   **Name**: the name of the label. The name can contain letters, digits, hyphens \(-\), underscores \(\_\), and periods \(.\).The name must start and end with a letter or a digit.
    -   **Value**: the value of the label. The value can contain letters, digits, hyphens \(-\), underscores \(\_\), and periods \(.\).The label value must start and end with a letter or a digit. You can leave this parameter empty.

## Step 2: Schedule a pod to a specific node

1.  In the left-side navigation pane of the ACK console, click **Clusters**.

2.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

3.  In the left-side navigation pane of the details page, choose **Workloads** \> **Deployments**.

4.  On the **Deployments** page, click **Create from Template** in the upper-right corner.

5.  Configure the template and use the template to deploy a pod.

    -   **Namespace**: Select the namespace to which the pod belongs. In this example, the **default** namespace is selected.
    -   **Sample Template**: In this example, Custom is selected.
    The following YAML template is used to deploy the pod:

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
         group: worker                           ##This value must be the same as the node label that is added in Step 1. 
       status: {}
    ```

6.  Click **Create**. A message that indicates the deployment status appears.


## Verify the result

You can use one of the following methods to check whether the pod is deployed on the specified node:

**Method 1: Click the pod name after the pod is deployed**

1.  After the pod is deployed, click the pod name in the notification at the bottom of the page to go to the Pods page.

    ![Create a pod](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/1197159161/p245822.png)

2.  On the Pods page, click the pod name to go to the details page of the pod.

    You can view the labels of the pod and the ID of the node to which the pod is scheduled. The following figure indicates that the pod is scheduled to the node with the `group:worker` label.

    ![Check pod details](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/4031073061/p10934.png)


**Method 2: Check the containers on the Pods page**

1.  In the left-side navigation pane of the ACK console, click **Clusters**.

2.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

3.  In the left-side navigation pane of the details page, choose **Workloads** \> **Pods**.

4.  On the Pods page, click the pod name to go to the details page of the pod.

    You can view the labels of the pod and the ID of the node to which the pod is scheduled. The following figure indicates that the pod is scheduled to the node with the `group:worker` label.


