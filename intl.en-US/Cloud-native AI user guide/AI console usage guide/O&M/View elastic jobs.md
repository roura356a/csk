---
keyword: elastic jobs
---

# View elastic jobs

You can view elastic jobs that are submitted by using Arena in AI Dashboard. This topic describes how to view the details of an elastic job in AI Dashboard.

-   The Arena client is installed. For more information, see [t1917487.dita\#task\_1917487](/intl.en-US/Solutions/Deep learning solution/Preparations/Install the latest version of Arena.md).
-   The cloud-native AI component set is installed. For more information, see [Deploy cloud-native AI component set](/intl.en-US/Cloud-native AI user guide/Environment preparation/Deploy cloud-native AI component set.md).
-   The credentials of the administrator of AI Dashboard are obtained.

1.  Run the following command to submit a training job by using Arena:

    ```
    arena submit tf \
      --name=tf-git \
      --gpus=1 \
      --image=tensorflow/tensorflow:1.5.0-devel-gpu \
      --sync-mode=git \
      --sync-source=https://code.aliyun.com/xiaozhou/tensorflow-sample-code.git \
      "python code/tensorflow-sample-code/tfjob/docker/mnist/main.py --max_steps 10000 --data_dir=code/tensorflow-sample-code/data"
    ```

2.  Log on to AI Dashboard by using the credentials of the administrator.

3.  In the left-side navigation pane of AI Dashboard, choose **Elastic Job** \> **Job List**.

4.  On the **Training Job** tab, you can view the training job submitted in Step [1](#step_yx2_nen_qbj).

5.  In the **training job** list, select the job that you want to view and click **Detail** in the Operator column.

    On the **Job Cost** page, you can view the following information about the job: **the duration**, **the estimated actual cost**, **the estimated on-demand cost**, and **the estimated saved cost**. You can also view the following information about each pod that runs the job: state, duration, resource type, instance type, and price.


**Note:** You can submit inference jobs by using Arena or kubectl and then view the job details on the **Inference Job** tab.

