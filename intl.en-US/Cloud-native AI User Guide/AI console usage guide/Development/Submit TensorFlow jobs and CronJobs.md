---
keyword: [submit a TensorFlow job, submit a CronJob]
---

# Submit TensorFlow jobs and CronJobs

This topic describes how to submit a TensorFlow job and a CronJob in the AI development console.

-   .
-   A dataset or source code repository is configured for the training job. For more information, see [Configure datasets and source code repositories for a training job](/intl.en-US/Cloud-native AI User Guide/AI console usage guide/Development/Configure datasets and source code repositories for a training job.md).

## Submit a TensorFlow job

1.  In the left-side navigation pane of the AI development console, click **Submit Job**.

2.  In the **Basic Information** section, set **Job Name**, **Job Type** \(the default is TF Stand-alone\), **Namespace**, **Data Configuration**, and **Code Configuration**, and then enter the command to execute in the **Execution Command** field.

    **Note:** **Namespace**: You can select only the **namespace** that is allocated to you by the cluster administrator. You can set other parameters based on your requirements.

3.  In the **Resources** section, specify **instances count** and **image** for training models. Then, specify **CPU \(Cores\)** \(the default is 4\), **Memory \(GB\)** \(the default is 8 GB\), and **GPU \(Card Numbers\)** \(the default is 0\) for the training job.

4.  Click **Submit Job**.

5.  After the training job is submitted, click **Job List** in the left-side navigation pane of the AI development console. On the page that appears, you can view information about the job, such as the name and the status of the job.


## Submit a CronJob

1.  In the left-side navigation pane of the AI development console, click **Submit Job**.

2.  In the **Basic Information** section, set **Job Name**, **Job Type** \(the default is TF Stand-alone\), **Namespace**, **Data Configuration**, and **Code Configuration**, and then enter the command to execute in the **Execution Command** field.

    **Note:** **Namespace**: You can select only the **namespace** that is allocated to you by the cluster administrator. You can set other parameters based on your requirements.

3.  Turn on **dlc-dashboard-cron** and set the following parameters for the CronJob:

    -   **Cron Schedule**: Enter a standard cron expression. For more information about cron expressions, see [how-use-cron-linux](https://opensource.com/article/17/11/how-use-cron-linux).
    -   If the current training job is still in progress, you can select a concurrency policy from the **dlc-dashboard-cron-co** drop-down list. Valid values:
        -   Allow: allows you to create a new training job.
        -   Forbid: forbids you from creating a new training job before the current training job is finished.
        -   Replace: replaces the current training job with a new training job.
4.  In the **Resources** section, specify **instances count** and **image** for training models. Then, specify **CPU \(Cores\)** \(the default is 4\), **Memory \(GB\)** \(the default is 8 GB\), and **GPU \(Card Numbers\)** \(the default is 0\) for the training job.

5.  Click **Submit Job**.

6.  After the training job is submitted, click **Job List** in the left-side navigation pane of the AI development console. On the page that appears, you can view information about the job, such as the name and the status of the job.


