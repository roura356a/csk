---
keyword: [CronJob, CronJob, Job, Kubernetes]
---

# Create a CronJob

CronJobs are used to create periodic and recurring tasks. For example, you can create CronJobs to run backups or send emails. Jobs are used to process short-lived, one-off tasks. A CronJob creates one or more Jobs based on a specific schedule. This topic describes how to create a CronJob.

## Create a CronJob in the ACK console

**Create a CronJob from an image**

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane of the ACK console, click **Clusters**.

3.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  In the left-side navigation pane of the details page, choose **Workloads** \> **CronJobs**.

5.  On the **CronJobs** page, click **Create from Image** in the upper-right corner.

6.  Set parameters for the CronJob.

    1.  On the **Basic Information** wizard page, configure the basic settings. For more information, see [Step 5: Configure basic settings](/intl.en-US/User Guide for Kubernetes Clusters/Application management/Workloads/Create a Job.md).

    2.  On the **Container** wizard page, configure one or more containers. For more information, see [Container configurations](/intl.en-US/User Guide for Kubernetes Clusters/Application management/Workloads/Create a Job.md).

    3.  On the **Advanced** wizard page, configure the advanced settings.

        |Section|Parameter|Description|
        |-------|---------|-----------|
        |CronJobs|Schedule|You can specify a schedule on a daily, weekly, or monthly basis. You can also specify a cron expression. A con expression is a string of six or seven fields that are separated with five or six spaces. Each field describes an individual detail of the schedule. Specify the cron expression in the following format:

        -   ```
Seconds Minutes Hours DayofMonth Month DayofWeek Year
```

        -   ```
Seconds Minutes Hours DayofMonth Month DayofWeek
```

For more information, see [Cron Expressions](https://docs.oracle.com/cd/E12058_01/doc/doc.1014/e12030/cron_expressions.htm). |
        |Concurrency Policy|You can select one of the following concurrency polices:

        -   Allow: allows Jobs to concurrently run. Concurrent Jobs may compete for cluster resources.
        -   Forbid: disallows Jobs to concurrently run. If a Job is not complete within the schedule, the next Job is skipped.
        -   Replace: If a Job is not complete within the schedule, the Job is skipped. |
        |Job History|You can specify the numbers of successful or failed Jobs for which you want to retain records. If you set the parameters to 0, the system does not retain the records of Jobs. |
        |Job Settings|Completions|For more information about how to set parameters in the Job Settings section, see [Job settings](/intl.en-US/User Guide for Kubernetes Clusters/Application management/Workloads/Create a Job.md).|
        |Parallelism|
        |Timeout|
        |BackoffLimit|
        |Restart|
        |Labels,Annotations|Pod Labels|You can add labels to pods in key-value pairs. **Note:** The key of a label must be 1 to 253 characters in length, and can contain only letters, digits, hyphens \(-\), underscores \(\_\), and periods \(.\). |
        |Pod Annotations|You can add annotations to pods in key-value pairs. **Note:** The key of an annotation must be 1 to 253 characters in length, and can contain only letters, digits, hyphens \(-\), underscores \(\_\), and periods \(.\). |

7.  Click **Create**.

    After the CronJob is created, you can view the CronJob on the CronJobs page.


**Create a CronJob from a YAML template**

1.  On the **CronJobs** page, click **Create from YAML** in the upper-right corner.

2.  On the Create page, configure the CronJob in the **Template** section.

3.  Click **Create**.


## Create a CronJob by using kubectl

Before you use kubectl to create a CronJob, you must download kubectl and connect to your cluster by using kubectl. For more information, see [t16645.md\#](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Access clusters/Connect to Kubernetes clusters by using kubectl.md).

The following table describes the key parameters that are used to create a CronJob.

|Parameter|Description|
|---------|-----------|
|.spec.schedule|Specifies the schedule of the CronJob. For more information about the schedule format, see [Cron schedule](https://kubernetes.io/docs/concepts/workloads/controllers/cron-jobs/#cron-schedule-syntax).|
|.spec.jobTemplate|Specifies the type of Job to be run. For more information about Job types, see [Job patterns](https://kubernetes.io/docs/concepts/workloads/controllers/job/#job-patterns).|
|.spec.startingDeadlineSeconds|Specifies the due time before which a Job must be run.|
|.spec.concurrencyPolicy|Specifies the concurrency policy. Valid values: Allow, Forbid, and Replace.-   Allow: allows Jobs to run concurrently. Concurrent Jobs compete for cluster resources.
-   Forbid: disallows Jobs to run concurrently. If a Job is not complete within the schedule, the next Job is skipped.
-   Replace: If a Job is not complete within the schedule, the Job is skipped. |

A CronJob named hello is created in this example to demonstrate how to create a CronJob by using kubectl.

1.  Create a cronjob.yaml file and copy the following content into the file:

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

2.  Run the following command to create a CronJob:

    ```
    kubectl create -f cronjob.yaml
    ```

    If `cronjob.batch/hello created` is returned, the CronJob is created.


## What to do next

After you create a CronJob, you can perform the following operations:

-   On the CronJobs page, find the created CronJob. Click **Details** in the **Actions** column to view basic information about the CronJob. The information includes the Job list, events, and logs.
-   On the CronJobs page, find the created CronJob. You can choose **More** \> **View in YAML** in the **Actions** column to view the YAML file of the CronJob. You can also choose **More** \> **Stop** to stop the CronJob or choose **More** \> **Delete** to delete the CronJob.

