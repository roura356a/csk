---
keyword: [scheduled scaling, CronHPA, Kubernetes, compatibility with HPA]
---

# CronHPA

To reduce resource wasting in some scenarios, Container Service for Kubernetes \(ACK\) provides the kubernetes-cronhpa-controller component to automatically scale resources based on predefined schedules. This topic describes how to use Cron Horizontal Pod Autoscaler \(CronHPA\) to scale your workload based on a schedule. It also describes how to enable CronHPA and Horizontal Pod Autoscaler \(HPA\) to interact without conflicts.

-   An ACK cluster is created. For more information, see [Create a managed Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster management/Create Kubernetes clusters/Create a managed Kubernetes cluster.md).
-   Helm 2.11.0 or later is installed on your on-premises machine. For more information, see [Install Helm](https://github.com/helm/helm?spm=a2c4g.11186623.2.28.36d61fa8fxvUaY).
-   You are connected to the cluster by using kubectl before you run commands to perform the following operations. For more information, see [Use kubectl to connect to an ACK cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster management/Access clusters/Use kubectl to connect to an ACK cluster.md).

kubernetes-cronhpa-controller is a Kubernetes HPA controller that scales a Kubernetes cluster based on a schedule that is similar to a crontab. You can use CronHPA with any Kubernetes object \(such as Deployment and StatefulSet\) whose subresources can be scaled. kubernetes-cronhpa-controller is an open source project on GitHub. For more information, see [kubernetes-cronhpa-controller](https://github.com/AliyunContainerService/kubernetes-cronhpa-controller).

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
|scaleTargetRef|scaleTargetRef specifies the scaling object. If the subresources of the scaling object can be scaled, you can enable CronHPA for the scaling object.|
|excludeDates|The value of excludeDates must be an array that specifies dates. Scaling jobs are not executed on dates that are specified by excludeDates. **Note:** The minimum time period is **one day**.

The value is in the `- "* * * * * *"` format, which indicates -"<seconds\> <minutes\> <hours\> <days of month\> <months\>" format.

For example, if you do not want to execute scaling jobs on November 15, set excludeDates to the following value: ```
excludeDates:
  - "* * * 15 11 *"
``` |
|jobs|You can set multiple CronHPA jobs in the spec section. You can set the following parameters for a CronHPA job: -   name: The name of each CronHPA job must be unique in the CronHPA configuration.
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

-   targetSize: The desired number of pods at scaling time.
-   runOnce: If you set runOnce to true, the job exits after one execution. |

## Install the CronHPA controller

You can install the CronHPA controller ack-kubernetes-cronhpa-controller by using the following methods.

**Method 1: Install the CronHPA controller from the Add-ons page in the ACK console**

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane of the ACK console, click **Clusters**.

3.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster, or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  In the left-side navigation pane of the cluster details page, choose **Operations** \> **Add-ons**.

5.  On the Add-ons page, click the **Core Components** tab. In the **Others** section, find **ack-kubernetes-cronhpa-controller** and click **Install**.


**Method 2: Install the CronHPA controller from the Manage System Components menu**

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane of the ACK console, click **Clusters**.

3.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster, or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  On the Clusters page, find the cluster that you want to manage and choose **More** \> **Manage System Components** in the **Actions** column.

5.  On the Add-ons page, click the **Core Components** tab. In the **Others** section, find **ack-kubernetes-cronhpa-controller** and click **Install**.


**Method 3: Install the CronHPA controller from App Catalog**

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane of the ACK console, choose **Marketplace** \> **App Catalog**.

3.  In the upper-right corner of the **Alibaba Cloud Apps** page, enter kubernetes-cronhpa-controller into the **Name** search box and click the search icon. Find and click **ack-kubernetes-cronhpa-controller**.

4.  On the right side of the **App Catalog - ack-kubernetes-cronhpa-controller** page, select the cluster to deploy the controller in the **Deploy** section and click **Create**.


## Create CronHPA jobs

Before you create and run CronHPA jobs for your application, you must make sure that the CronHPA controller runs as normal in your cluster. You must also make sure that only one HPA task is created for your application. For more information about how to enable CronHPA and HPA to interact without conflicts, see the following [Enable CronHPA and HPA to interact without conflicts](#section_ah0_3o3_h28) section. You can create CronHPA jobs in the following scenarios.

**Scenario 1: Create CronHPA jobs when you create an application**

In the **Scaling** section on the **Advanced** wizard page when you create an application, select **Enable** on the right side of **CronHPA** to create CronHPA jobs for the application. For more information about how to create an application, see [Deploy a stateless application from an image](/intl.en-US/User Guide for Kubernetes Clusters/Application management/Deploy a stateless application from an image.md) or [Deploy a stateful application from an image](/intl.en-US/User Guide for Kubernetes Clusters/Application management/Deploy a stateful application from an image.md).

![cronhpa](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/2431471161/p206611.png)

The ACK console automatically checks whether the CronHPA controller is installed in the cluster. If the CronHPA controller is not installed in the cluster, the **Install** button appears on the page. After the CronHPA controller is installed, the CronHPA parameters appear on the page. Set the parameters as described in the following table.

|Parameter|Description|
|---------|-----------|
|Job Name|Enter a name for the CronHPA job. The name of each CronHPA job must be unique.|
|Desired Number of Replicas|Pod replicas are scaled to the desired number at the scheduled time.|
|Scaling Schedule|Set the scaling schedule.For more information about how to set the scaling schedule for a CronHPA job, see [AliyunContainerService/kubernetes-cronhpa-controller](https://github.com/AliyunContainerService/kubernetes-cronhpa-controller). |

**Scenario 2: Create CronHPA jobs for existing applications**

The following example demonstrates how to create CronHPA jobs for an existing application. A Deployment application is used in this example.

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane of the ACK console, click **Clusters**.

3.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster, or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  In the left-side navigation pane of the details page, choose **Workloads** \> **Deployments**.

5.  On the Deployments page, find the application that you want to manage and click **Details** in the **Actions** column.

6.  Click the **Pod Scaling** tab and configure CronHPA jobs.

    -   If the CronHPA controller is not installed, the **Install** button appears on the page. Click **Install** and perform the following steps.
    -   If the CronHPA controller is installed, perform the following steps.
7.  Click **Create** on the right side of **CronHPA**. In the **Create** dialog box, configure CronHPA jobs.

    ![create](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/2431471161/p206630.png)

    For more information about how to configure CronHPA jobs, see the preceding [Table 1](#table_3xu_x42_mfb) section.


## Create or modify CronHPA jobs

1.  Go to the **Pod Scaling** page by performing the steps as described in the preceding [Create CronHPA jobs for existing applications](#p_cfx_lt5_l3v) section.

2.  On the **Pod Scaling** tab, find the created CronHPA task in the **CronHPA** section and click **Add or Modify Job** in the **Actions** column.

3.  In the **Edit** dialog box, click **Add Job** to create CronHPA jobs. You can also modify existing CronHPA jobs. Click **OK**.

    ![modify](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/1012471161/p206651.png)

4.  To delete CronHPA jobs, perform the following steps.
5.  In the **Edit** dialog box, click the delete icon in the upper-right corner of the tab for the job that you want to delete. Click **OK**.

    ![delete](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/1012471161/p206655.png)


## Enable CronHPA and HPA to interact without conflicts

CronHPA triggers horizontal scaling for containers based on schedules. HPA is used to scale pods to ensure the availability of your applications when the network traffic spikes. If ACK detects that both CronHPA and HPA are deployed, ACK sets the scaling object of CronHPA to the scaling object of HPA. This way, HPA can scale pods on behalf of CronHPA at the time that is scheduled by CronHPA.

The following YAML template shows the configurations of CronHPA and HPA:



After you compare the configurations of CronHPA and HPA, you can find that:

-   The scaleTargetRef field is used in the configurations of both CronHPA and HPA to specify the object to be scaled.
-   The crontab rules in the jobs section of the CronHPA configuration specify the number to which the pods are scaled at the scheduled time.
-   HPA triggers scaling activities based on resource usage.

When CronHPA and HPA are both deployed, CronHPA and HPA may scale pods for the same application that is specified by scaleTargetRef. CronHPA and HPA are independent and unaware of each other. As a result, the CronHPA controller and the HPA controller scale pods for the application separately. The later scaling activity overwrites the earlier one.

**Solution**

The reason for the conflict between CronHPA and HPA is that the CronHPA controller and the HPA controller are unaware of each other. To resolve the conflict, CronHPA only needs to detect the state of HPA. In the HPA configuration, scaleTargetRef specifies the Deployment that is managed by HPA. The Deployment is related to a ReplicaSet. When HPA scales the Deployment, the pods of the ReplicaSet are scaled to the desired number. ACK modifies the CronHPA configuration by setting scaleTargetRef to the object that is scaled by HPA. CronHPA can use this object to find the application that is specified by scaleTargetRef in the HPA configuration. This enables CronHPA to detect the state of HPA.

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

After you deploy the preceding YAML template, CronHPA is aware of the values of minReplicas, maxReplicas, and desiredReplicas in the HPA configuration. CronHPA is also aware of the current number of pods provisioned for the application that is specified by scaleTargetRef. CronHPA can detect the state of HPA by modifying the HPA configuration. CronHPA compares the desired number of pods with the current number of pods and then determines whether to trigger scaling activities and change the maximum number of pods in the HPA configuration. CronHPA also compares the desired number of pods with the maximum and minimum numbers of pods that are specified in the HPA configuration and then determines whether to change the minimum numbers of pods in the HPA configuration.

The following table describes the rules that enable CronHPA and HPA to interact without conflicts.

|HPA \(min/max\)|Cronhpa|Deployment|Scaling result|Description|
|---------------|-------|----------|--------------|-----------|
|1/10|5|5|-   HPA \(min/max\): 1/10
-   Deployment: 5

|If the number of pods desired by CronHPA equals the current number of pods, CronHPA does not change the maximum and minimum numbers of pods in the HPA configuration. In addition, no scaling activity is triggered.|
|1/10|4|5|-   HPA \(min/max\): 1/10
-   Deployment: 5

|If the number of pods desired by CronHPA is lower than the current number of pods, no scaling activity is triggered.|
|1/10|6|5|-   HPA \(min/max\): 6/10
-   Deployment: 6

|-   If the number of pods desired by CronHPA is greater than the current number of pods, CronHPA adds pods to reach the desired number.
-   If the number of pods desired by CronHPA is greater than the value of minReplicas in the HPA configuration, CronHPA changes the value of minReplicas. |
|5/10|4|5|-   HPA \(min/max\): 4/10
-   Deployment: 5

|-   If the number of pods desired by CronHPA is lower than the current number of pods, no scaling activity is triggered.
-   If the number of pods desired by CronHPA is lower than the value of minReplicas in the HPA configuration, CronHPA changes the value of minReplicas. |
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

[How CronHPA interacts with HPA without conflicts]()

