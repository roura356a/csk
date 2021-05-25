---
keyword: [pod, pods, smallest deployable unit, pod log]
---

# Manage pods

Pods are the smallest deployable units in Kubernetes. A pod runs an instance of an independent application in Kubernetes. Each pod contains one or more containers that are tightly coupled. You can modify pods, view pods, and manually scale the number of pods for an application in the Container Service for Kubernetes \(ACK\) console.

## View pods

**View pod details**

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane of the ACK console, click **Clusters**.

3.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  In the left-side navigation pane of the details page, choose **Workloads** \> **Pods**.

5.  Find the pod that you want to view and click **View Details** in the Actions column.

    You can view details of pods by using one of the following methods:

    -   Method 1: In the left-side navigation pane of the details page, choose **Workloads** \> **Deployments**. Find the application that you want to manage and click the name of the application. On the **Pods** tab, click the name of the pod to view details.
    -   Method 2: In the left-side navigation pane of the details page, choose **Services and Ingresses** \> **Services**. Click the name of the Service that you want to manage. On the page that appears, find and click the name of the application that you want to manage. On the **Pods** tab, click the name of the pod to view details.
    **Note:** On the Pods page, you can modify and delete pods. For pods that are created by using a Deployment, we recommend that you use the Deployment to manage the pods.


**View pod log**

You can view a pod log by using the following methods:

Navigate to the Pods tab, find the pod that you want to manage, and then click **Logs** on the right side to view the log data.

## Modify pod configurations

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane of the ACK console, click **Clusters**.

3.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  In the left-side navigation pane of the details page, choose **Workloads** \> **Pods**.

5.  On the **Pods** page, find the pod that you want to manage and click **Edit** in the Actions column.

6.  In the dialog box that appears, modify the configuration of the pod and click **Update**.

    ![Update the configuration of the pod](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/8457669161/p10941.png)


## Manually scale the number of pods for an application

After an application is created, you can scale in or out the pods that are provisioned for the application.

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane of the ACK console, click **Clusters**.

3.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  In the left-side navigation pane of the details page, choose **Workloads** \> **Deployments**.

5.  Select the namespace where the service is deployed, find the deployment for the new version, and then click **Scale** in the Actions column.

    ![Deployments](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/4983662061/p9942.png)

6.  In the dialog box that appears, set Desired Number of Pods to 4 and click **OK**.

    **Note:** By default, the resources created by a Deployment are updated based on the rollingUpdate strategy. This ensures that the minimum number of pods are available during the update. You can specify the minimum number of pods that must run in Pod Template of the YAML file.


