---
keyword: [CronJob, CronJob, Job, K8s]
---

# Create a CronJob

A CronJob is used to perform periodic or recurring operations, such as backing up data and sending emails. Jobs are used to run one-time tasks in batches. A CronJob creates Jobs based on a specified schedule. This topic describes how to create a CronJob.

## Create a CronJob in the ACK console

**Create a CronJob from an image**

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane, click **Clusters**.

3.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  In the left-side navigation pane of the details page, choose **Workloads** \> **CronJobs**.

5.  In the upper-right corner of the **CronJobs** page, click **Create from Image**.

6.  Set the parameters of the CronJob.

    1.  On the **Basic Information** wizard page, specify basic information about the application. For more information about the parameters, see [Step 5: Configure basic information about the application](/intl.en-US/User Guide for Kubernetes Clusters/Application management/Workloads/Create a Job application by using an image.md).

    2.  On the **Container** wizard page, configure the containers. For more information about the parameters, see [Container configurations](/intl.en-US/User Guide for Kubernetes Clusters/Application management/Workloads/Create a Job application by using an image.md).

    3.  On the **Advanced** wizard page, configure the advanced settings of the CronJob.

        |Category|Parameter|Description|
        |--------|---------|-----------|
        |CronJobs|Schedule|You can set the schedule by day, by week, or by month. You can also specify a cron expression. A con expression is a string of six or seven fields that are separated with five or six spaces. Each field describes an individual detail of the schedule. Specify the cron expression in the following format:

        -   ```
Seconds Minutes Hours Day of Month Month Day of Week Year
```

        -   ```
Seconds Minutes Hours Day of Month Month Day of Week
```

For more information about, see [Cron expressions](https://docs.oracle.com/cd/E12058_01/doc/doc.1014/e12030/cron_expressions.htm). |
        |Concurrency Policy|You can select the following concurrency policies:        -   Allow: This policy allows Jobs to concurrently run. These Jobs may compete for resources of the cluster.
        -   Forbid: This policy forbids Jobs to concurrently run. When the time to run a new Job is reached, if the previous Job is not complete, the new Job is skipped.
        -   Replace: When the time to run a new Job is reached, if the previous Job is not complete, the new Job replaces the previous one. |
        |Job History|You can specify the number of complete Jobs or failed Jobs that are retained. A value of 0 indicates that no complete Job or failed Job is retained.|
        |Job Settings|Completions|For more information about the parameters, see [Job settings](/intl.en-US/User Guide for Kubernetes Clusters/Application management/Workloads/Create a Job application by using an image.md).|
        |Parallelism|
        |Timeout|
        |BackoffLimit|
        |Restart|
        |Labels,Annotations|Pod Labels|Specify the names and values of the pod labels. **Note:** A name must be 1 to 253 characters in length, and can contain letters, digits, hyphens \(-\), underscores \(\_\), and periods \(.\). |
        |Pod Annotations|Specify the names and values of the pod annotations. **Note:** A name must be 1 to 253 characters in length, and can contain letters, digits, hyphens \(-\), underscores \(\_\), and periods \(.\). |

7.  Click **Create**.

    After the CronJob is created, you can find it on the CronJobs page.


**Create a CronJob from a template**

1.  In the upper-right corner of the **CronJobs** page, click **Create from Template**.

2.  On the Create page, specify the template information in the **Template** code editor.

3.  Click **Create**.


## Create a CronJob by using kubectl

Before you create a CronJob by using kubectl, you must install kubectl and connect to the cluster by using kubectl. For more information, see [Use kubectl to connect to an ACK cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Access clusters/Use kubectl to connect to an ACK cluster.md).

The following table describes the key parameters that are required to create a CronJob.

|Parameter|Description|
|---------|-----------|
|.spec.schedule|Specify the schedule. For more information, see [Cron schedule](https://kubernetes.io/docs/concepts/workloads/controllers/cron-jobs/#cron-schedule-syntax).|
|.spec.jobTemplate|Specify the configurations of the Jobs that are managed by the CronJob. For more information, see [Job patterns](https://kubernetes.io/docs/concepts/workloads/controllers/job/#job-patterns).|
|.spec.startingDeadlineSeconds|Specify a time limit within which a Job can be started.|
|.spec.concurrencyPolicy|Specify the concurrency policy for Jobs. Valid values:-   Allow: This policy allows Jobs to concurrently run. These Jobs may compete for resources of the cluster.
-   Forbid: This policy forbids Jobs to concurrently run. When the time to run a new Job is reached, if the previous Job is not finished, the new Job is skipped.
-   Replace: When the time to run a new Job is reached, if the previous Job is not complete, the new Job replaces the previous one. |

In this topic, a sample CronJob named hello is created by using kubectl.

1.  Create a file named cronjob.yaml and copy the following content into the file:

    ```
    apiVersion: batch/v2alpha1
    kind: CronJob
    metadata:
      name: hello
    spec:
      schedule: "*/1 * * * *"
      jobTemplate:
        spec:
          template:
            spec:
              containers:
              - name: hello
                image: busybox
                args:
                - /bin/sh
                - -c
                - date; echo Hello from the Kubernetes cluster
              restartPolicy: OnFailure
    ```

2.  Run the following command to create the CronJob:

    ```
    kubectl create -f cronjob.yaml
    ```

    If the output is `cronjob.batch/hello created`, it indicates that the CronJob is created.


## What to do next

After the CronJob is created, you can perform the following operations:

-   Go to the CronJobs page, find the created CronJob and click **Details** in the **Actions** column. On the details page, you can view basic information about the CronJob, such as the Jobs, events, and logs.
-   Go to the CronJobs page, find the created CronJob and choose **More** \> **View in YAML** in the **Actions** column to view the CronJob configurations in the YAML format. You can also choose **More** \> **Stop** to stop the CronJob, or choose **More** \> **Delete** to delete the CronJob.

