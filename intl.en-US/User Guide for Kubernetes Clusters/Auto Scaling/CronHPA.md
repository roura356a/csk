---
keyword: [scheduled scaling, CronHPA, Kubernetes, compatibility with HPA]
---

# CronHPA

To avoid resource wasting in some scenarios, Container Service for Kubernetes \(ACK\) provides the kubernetes-cronhpa-controller component to automatically scale resources based on predefined schedules. This topic describes how to use Cron Horizontal Pod Autoscaler \(CronHPA\) to scale your workloads based on a schedule. This topic also describes how to enable CronHPA and Horizontal Pod Autoscaler \(HPA\) to interact without conflicts.

-   An ACK cluster is created. For more information, see [Create a managed Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Create Kubernetes clusters/Create a managed Kubernetes cluster.md).
-   Helm 2.11.0 or later is installed on your on-premises machine. For more information, see [Install Helm](https://github.com/helm/helm?spm=a2c4g.11186623.2.28.36d61fa8fxvUaY).
-   You are connected to the cluster by using kubectl before you run commands to perform the following operations. For more information, see [t16645.md\#](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Access clusters/Connect to Kubernetes clusters by using kubectl.md).

kubernetes-cronhpa-controller is a Kubernetes HPA controller that can scale a Kubernetes cluster based on a schedule that is similar to a crontab. You can use CronHPA with Kubernetes objects whose subresources can be scaled. The objects include Deployments and StatefulSets. In addition, the subresources must be open source projects on GitHub. For more information, see [kubernetes-cronhpa-controller](https://github.com/AliyunContainerService/kubernetes-cronhpa-controller).

The following table describes the parameters in the CronHPA configuration.

```
apiVersion: autoscaling.alibabacloud.com/v1beta1
kind: CronHorizontalPodAutoscaler
metadata:
  labels:
    controller-tools.k8s.io: "1.0"
  name: cronhpa-sample
  namespace: default 
spec:
   scaleTargetRef:
      apiVersion: apps/v1
      kind: Deployment
      name: nginx-deployment-basic
   excludeDates:
   # exclude November 15th
   - "* * * 15 11 *"
   # exclude every Friday 
   - "* * * * * 5"
   jobs:
   - name: "scale-down"
     schedule: "30 */1 * * * *"
     targetSize: 1
   - name: "scale-up"
     schedule: "0 */1 * * * *"
     targetSize: 3
     runOnce: true
```

|Parameter|Description|
|---------|-----------|
|scaleTargetRef|scaleTargetRef specifies the object that you want to scale. If the subresources of the object can be scaled, you can enable CronHPA for the object.|
|excludeDates|The value of excludeDates must be an array of dates. Scaling jobs are not executed on dates that are specified in excludeDates. **Note:** The minimum time period is **one day**.

The value is in the `- "* * * * * *"` format, which indicates -"<seconds\> <minutes\> <hours\> <days of month\> <months\>" format.

For example, if you do not want to execute scaling jobs on November 15, set excludeDates to the following value: ```
excludeDates:
  - "* * * 15 11 *"
``` |
|jobs|You can set multiple CronHPA jobs in the spec section. You can set the following parameters for each CronHPA job: -   name: Names are used to distinguish CronHPA jobs. Therefore, the name of each CronHPA job must be unique in the CronHPA configurations.
-   schedule: the scaling schedule, which is similar to a crontab. kubernetes-cronhpa-controller uses a Golang library to support a variety of rules. For more information, see [go-cron](https://github.com/ringtail/go-cron). The format of cron expressions must conform to the following rules. Otherwise, you cannot create cron expressions.

Create cron expressions based on the following rules:

    ```
Field name   | Mandatory? | Allowed values  | Allowed special characters
  ----------   | ---------- | --------------  | --------------------------
  Seconds      | Yes        | 0-59            | * / , -
  Minutes      | Yes        | 0-59            | * / , -
  Hours        | Yes        | 0-23            | * / , -
  Day of month | Yes        | 1-31            | * / , - ?
  Month        | Yes        | 1-12 or JAN-DEC | * / , -
  Day of week  | Yes        | 0-6 or SUN-SAT  | * / , - ?
    ```

-   targetSize: the number of pods to which you want to scale at the scheduled time.
-   runOnce: If you set runOnce to true, the job is executed only once. The job exits after one run. |

## Install the CronHPA controller

You can install the CronHPA controller ack-kubernetes-cronhpa-controller by using the following methods.

**Method 1: Install the CronHPA controller on the Add-ons page in the ACK console**

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane of the ACK console, click **Clusters**.

3.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  In the left-side navigation pane of the details page, choose **Operations** \> **Add-ons**.

5.  On the Add-ons page, click the **Others** tab, find **ack-kubernetes-cronhpa-controller**, and then click **Install**.


**Method 2: Install the CronHPA controller from the Manage System Components menu**

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane of the ACK console, click **Clusters**.

3.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  On the Clusters page, find the cluster that you want to manage and choose **More** \> **Manage System Components** in the **Actions** column.

5.  On the Add-ons page, click the **Others** tab, find **ack-kubernetes-cronhpa-controller**, and then click **Install**.


**Method 3: Install the CronHPA controller from App Catalog**

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane of the ACK console, choose **Marketplace** \> **App Catalog**.

3.  You can search for the component on the **Alibaba Cloud Apps** tab. In the upper-left corner of the page, enter kubernetes-cronhpa-controller into the **Name** search box and click the search icon. Find and click **ack-kubernetes-cronhpa-controller**.

4.  On the right side of the **App Catalog - ack-kubernetes-cronhpa-controller** page, select a cluster to deploy the controller in the **Deploy** section and click **Create**.


You can uninstall the CronHPA controller if CronHPA is no longer used. For more information about how to uninstall ack-kubernetes-cronhpa-controller, see [Manage system components](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Upgrade cluster/Manage system components.md) or [Delete a release](/intl.en-US/User Guide for Kubernetes Clusters/Release management/Manage releases by using Helm.md).

## Create CronHPA jobs

Before you create and run CronHPA jobs for your application, make sure that the CronHPA controller runs as normal in your cluster and only one HPA task is created for your application. For more information about how to enable CronHPA and HPA to interact without conflicts, see [Enable CronHPA and HPA to interact without conflicts](#section_ah0_3o3_h28). You can create CronHPA jobs in the following scenarios.

**Scenario 1: Create CronHPA jobs when you create an application**

In the **Scaling** section on the **Advanced** wizard page, select **Enable** on the right side of **CronHPA** to create CronHPA jobs for the application. For more information about how to create an application, see [Use a Deployment to create a stateless application](/intl.en-US/User Guide for Kubernetes Clusters/Application management/Workloads/Use a Deployment to create a stateless application.md) or [Use a StatefulSet to create a stateful application](/intl.en-US/User Guide for Kubernetes Clusters/Application management/Workloads/Use a StatefulSet to create a stateful application.md).

![cronhpa](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/2431471161/p206611.png)

The ACK console automatically checks whether the CronHPA controller is installed in the cluster. If the CronHPA controller is not installed, the **Install** button appears on the page. After the CronHPA controller is installed, the CronHPA parameters appear on the page. The following table describes the parameters.

|Parameter|Description|
|---------|-----------|
|Job Name|Enter a name for the CronHPA job. The name of each CronHPA job must be unique.|
|Desired Number of Replicas|Pod replicas are scaled to the desired number at the scheduled time.|
|Scaling Schedule|Set the scaling schedule. For more information about how to set the scaling schedule for a CronHPA job, see [AliyunContainerService/kubernetes-cronhpa-controller](https://github.com/AliyunContainerService/kubernetes-cronhpa-controller). |

**Scenario 2: Create CronHPA jobs for an existing application**

The following example demonstrates how to create CronHPA jobs for an existing application. A stateless application is used in this example.

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane of the ACK console, click **Clusters**.

3.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  In the left-side navigation pane of the details page, choose **Workloads** \> **Deployments**.

5.  On the Deployments page, find the application that you want to manage and click **Details** in the **Actions** column.

6.  Click the **Pod Scaling** tab and configure CronHPA jobs.

    -   If the CronHPA controller is not installed, the **Install** button appears on the page. Click **Install** and perform the following steps.
    -   If the CronHPA controller is installed, perform the following steps.
7.  Click **Create** on the right side of **CronHPA**. In the **Create** dialog box, configure CronHPA jobs.

    ![create](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/2431471161/p206630.png)

    For more information about how to configure CronHPA jobs, see [Table 1](#table_3xu_x42_mfb).


## Create or modify CronHPA jobs

1.  Go to the **Pod Scaling** tab by performing the steps described in the preceding [Create CronHPA jobs for an existing application](#p_cfx_lt5_l3v) section.

2.  On the **Pod Scaling** tab, find the created CronHPA job in the **CronHPA** section and click **Add or Modify Job** in the **Actions** column.

3.  In the **Edit** dialog box, click **Add Job** to create CronHPA jobs. You can also modify existing CronHPA jobs. Click **OK**.

    ![modify](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/1012471161/p206651.png)

4.  To delete CronHPA jobs, perform the following steps:
5.  In the **Edit** dialog box, click the delete icon in the upper-right corner of the job that you want to delete. Click **OK**.

    ![delete](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/1012471161/p206655.png)


## Enable CronHPA and HPA to interact without conflicts

CronHPA triggers horizontal scaling for containers based on schedules. HPA is used to scale pods to ensure the availability of your applications when network traffic spikes. If ACK detects that both CronHPA and HPA are deployed, ACK sets HPA as the scaling object of CronHPA. CronHPA triggers HPA to scale pods at the scheduled time.

The following YAML template shows the configurations of CronHPA and HPA:



After you compare the configurations of CronHPA and HPA, you can find that:

-   The scaleTargetRef field is used in the configurations of both CronHPA and HPA to specify the object to be scaled.
-   The crontab rules in the jobs section of the CronHPA configuration specify the number to which pods are scaled at the scheduled time.
-   HPA triggers scaling activities based on resource usage.

If both CronHPA and HPA are deployed, CronHPA and HPA may scale pods for the same application that is specified by scaleTargetRef. CronHPA and HPA are independent and unaware of each other. As a result, the CronHPA controller and the HPA controller scale pods for the application separately. The later scaling activity overwrites the earlier one.

**Solution**

The reason for the conflict between CronHPA and HPA is that the CronHPA controller and the HPA controller are unaware of each other. To resolve the conflict, CronHPA needs only to detect the state of HPA. In the HPA configuration, scaleTargetRef specifies the Deployment that is managed by HPA. The Deployment is related to a ReplicaSet. When HPA scales the Deployment, the ReplicaSet scales pods to the desired number. ACK modifies the CronHPA configuration by setting scaleTargetRef to HPA. CronHPA can find the application that is specified by scaleTargetRef in the HPA configuration. This enables CronHPA to detect the state of HPA.

![cronhpa](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/0532397061/p188680.png)

The following YAML template shows the configurations that enable CronHPA and HPA to interact without conflicts:

```
apiVersion: autoscaling.alibabacloud.com/v1beta1
kind: CronHorizontalPodAutoscaler
metadata:
  labels:
    controller-tools.k8s.io: "1.0"
  name: cronhpa-sample
spec:
   scaleTargetRef:
      apiVersion: autoscaling/v1
      kind: HorizontalPodAutoscaler
      name:  nginx-deployment-basic-hpa
   jobs:
   - name: "scale-down"
     schedule: "30 */1 * * * *"
     targetSize: 1
     runOnce: true
   - name: "scale-up"
     schedule: "0 */1 * * * *"
     targetSize: 3
     runOnce: true
```

After you deploy the preceding YAML template, CronHPA is aware of the values of minReplicas, maxReplicas, and desiredReplicas in the HPA configuration. CronHPA is also aware of the current number of pods provisioned for the application that is specified by scaleTargetRef. CronHPA can detect the state of HPA by modifying the HPA configurations. CronHPA compares the desired number of pods with the current number of pods and then determines whether to trigger scaling activities and change the maximum number of pods in the HPA configuration. CronHPA also compares the desired number of pods with the maximum and minimum numbers of pods that are specified in the HPA configuration and then determines whether to change the minimum numbers of pods in the HPA configuration.

The following table describes the rules that enable CronHPA and HPA to interact without conflicts.

|HPA \(min/max\)|Cronhpa|Deployment|Scaling result|Description|
|---------------|-------|----------|--------------|-----------|
|1/10|5|5|-   HPA \(min/max\): 1/10
-   Deployment: 5

|If the number of pods desired by CronHPA equals the current number of pods, CronHPA does not change the maximum and minimum numbers of pods in the HPA configuration. In addition, no scaling activity is triggered.|
|1/10|4|5|-   HPA \(min/max\): 1/10
-   Deployment: 5

|If the number of pods desired by CronHPA is smaller than the current number of pods, no scaling activity is triggered.|
|1/10|6|5|-   HPA \(min/max\): 6/10
-   Deployment: 6

|-   If the number of pods desired by CronHPA is greater than the current number of pods, CronHPA adds pods to reach the desired number.
-   If the number of pods desired by CronHPA is greater than the value of minReplicas in the HPA configuration, CronHPA changes the value of minReplicas. |
|5/10|4|5|-   HPA \(min/max\): 4/10
-   Deployment: 5

|-   If the number of pods desired by CronHPA is smaller than the current number of pods, no scaling activity is triggered.
-   If the number of pods desired by CronHPA is smaller than the value of minReplicas in the HPA configuration, CronHPA changes the value of minReplicas. |
|5/10|11|5|-   HPA \(min/max\): 11/11
-   Deployment: 11

|-   If the number of pods desired by CronHPA is greater than the current number of pods, CronHPA adds pods to reach the desired number.
-   If the number of pods desired by CronHPA is greater than the value of maxReplicas in the HPA configuration, CronHPA changes the value of maxReplicas. |

The following list describes the parameters in the table:

-   HPA \(min/max\): the minimum and maximum numbers of pods that are specified in the HPA configuration.
-   CronHPA: the desired number of pods.
-   Deployment: the current number of pods that are provisioned for the application.

CronHPA does not directly change the number of pods for the Deployment. It triggers HPA to scale the pods. This resolves the conflict between CronHPA and HPA.

**Related topics**  


[HPA](/intl.en-US/User Guide for Kubernetes Clusters/Auto Scaling/HPA.md)

