---
keyword: [cloud-native AI, monitoring, GPU resources]
---

# Work with AI dashboards

You can install the cloud-native AI monitoring component ack-arena-exporter to monitor the use of GPU resources by cluster, node, or training job, and gain insights into resource quotas in different namespaces. This topic introduces dashboards for clusters, nodes, training jobs, and resource quotas. This topic also describes how to install ack-arena-exporter and work with these dashboards.

-   Only professional Kubernetes clusters are supported and the Kubernetes version of the clusters must be 1.18.8 or later.
-   The Arena component of V0.7.0 or later is installed. For more information, see [Install Arena]().
-   The Application Real-Time Monitoring Service \(ARMS\) Prometheus component is installed. For more information, see [arms-prometheus](https://cs.console.aliyun.com/#/k8s/catalog/detail/incubator_ack-arms-prometheus).
-   The AI Dashboard component is installed. For more information, see [Deploy the cloud-native AI component set](/intl.en-US/Cloud-native AI User Guide/Environment preparation/Deploy the cloud-native AI component set.md).

After you use Arena to submit a training job, you may want to monitor the use of GPUs and GPU memory by cluster, node, training job, or resource quota. In traditional Container Service for Kubernetes \(ACK\) clusters, you can monitor the use of GPU resources \(including GPU utilization, GPU memory usage, and GPU power\) only on a specific node or view GPU resources used by a specific pod.

To monitor resource usage in more dimensions, ACK offers cloud-native AI dashboards. Compared with the GPU dashboard in traditional ACK clusters, the AI dashboards have the following benefits:

-   The AI dashboards consist of the cluster dashboard, node dashboard, training job dashboard, and resource quota dashboard.
-   The cluster dashboard displays the total number of GPU-accelerated nodes, number of unhealthy GPU-accelerated nodes, average GPU utilization, and numbers of training jobs in different states.
-   The node dashboard displays the total number of GPUs, number of allocated GPUs, GPU utilization, and GPU memory usage on each node.
-   The training job dashboard displays the status, duration, number of requested GPUs, average GPU utilization, and GPU memory usage of each training job.
-   The resource quota dashboard displays the allocation and usage of a resource type in a specific namespace, such as Max Quota, Min Quota, and Used Quota.

## Install ack-arena-exporter

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane of the ACK console, click **Clusters**.

3.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  In the left-side navigation pane, choose **Applications** \> **AI Project Acceleration \(Public Review\)**.

5.  Install ack-arena-exporter.

    -   If you have installed the cloud-native AI component set:

        On the Cloud-native AI Component Set page, find the monitoring component **ack-arena-exporter** and click **Deploy** in the **Actions** column.

    -   If you have not installed the cloud-native AI component set:

        On the Cloud-native AI Component Set page, click **Deploy**. In the **Monitoring** section, select **Monitoring Component** and click **Deploy Cloud-native AI Component Set**.


## Introduction to AI dashboards

**Cluster dashboard**

After you log on to AI Dashboard, you are redirected to the cluster dashboard by default. For more information, see [Method 1: Log on to AI Dashboard](/intl.en-US/Cloud-native AI User Guide/AI console usage guide/O&M/Access AI Dashboard.md).

You can view the following metrics in the cluster dashboard:

-   **GPU Summary Of Cluster**: displays the total number of GPU-accelerated nodes, number of allocated GPU-accelerated nodes, and number of unhealthy GPU-accelerated nodes in the cluster.
-   **Total GPU Nodes**: the total number of GPU-accelerated nodes in the cluster.
-   **Unhealthy GPU Nodes**: the number of unhealthy GPU-accelerated nodes in the cluster.
-   **GPU Memory\(Used/Total\)**: the ratio of GPU memory used by the cluster to the total GPU memory.
-   **GPU Memory\(Allocated/Total\)**: the ratio of GPU memory allocated by the cluster to the total GPU memory.
-   **GPU Utilization**: the average GPU utilization of the cluster.
-   **GPUs\(Allocated/Total\)**: the ratio of the number of GPUs that are allocated by the cluster to the total number of GPUs.
-   **Training Job Summary Of Cluster**: the numbers of training jobs that are in the following states: Running, Pending, Succeeded, and Failed.

![jk1](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/8249737261/p278620.png)

**Node dashboard**

On the Cluster page, click **Nodes** in the upper-right corner to navigate to the node dashboard.

You can view the following metrics in the node dashboard:

-   **GPU Node Details**: displays information about the cluster nodes in a table. The following information is displayed:
    -   The name of each node
    -   The IP address of each node in the cluster
    -   The role of each node in the cluster
    -   The status of each node
    -   The GPU mode of each node: exclusive or shared
    -   The number of GPUs owned by each node
    -   The total amount of GPU memory owned by each node
    -   The number of GPUs allocated on each node
    -   The amount of GPU memory allocated on each node
    -   The amount of GPU memory used on each node
    -   The average GPU utilization on each node
-   **GPU Duty Cycle**: the utilization of each GPU on each node.
-   **GPU Memory Usage**: the memory usage of each GPU on each node.
-   **GPU Memory Usage Percentage**: the percentage of memory usage per GPU on each node.
-   **Allocated GPUs Per Node**: the number of GPUs allocated on each node.
-   **GPU Number Per Node**: the total number of GPUs on each node.
-   **Total GPU Memory Per Node**: the total amount of GPU memory on each node.

![jk21](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/8249737261/p278625.png)

![jk22](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/8249737261/p278626.png)

You can use the **gpu\_node** and **GPU** filters in the upper-left corner of the Nodes page to filter nodes and GPUs. This allows you to view the GPU usage on only specified nodes.

![jk23](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/8249737261/p278628.png)

**Training job dashboard**

On the Nodes page, click **TrainingJobs** in the upper-right corner to navigate to the training job dashboard.

You can view the following metrics in the training job dashboard:

-   **Training Jobs**: displays the information about each training job in a table. The following information is displayed:
    -   The namespace of each training job
    -   The name of each training job
    -   The type of each training job
    -   The status of each training job
    -   The duration of each training job
    -   The number of GPUs that are requested by each training job
    -   The amount of GPU memory that is request by each training job
    -   The amount of GPU memory that is used by each training job
    -   The average GPU utilization of each training job
-   **Job Instance Used GPU Memory**: the amount of GPU memory that is used by each job instance.
-   **Job Instance Used GPU Memory Percentage**: the percentage of GPU memory that is used by each job instance.
-   **Job Instance GPU Duty Cycle**: the GPU utilization of each job instance.

![jk31](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/8249737261/p278634.png)

You can use the **job\_namespace**, **job\_type**, and **job\_name** filters to filter training jobs by namespace, type, and name. This allows you to view detailed information about only specified training jobs.

![jk32](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/8249737261/p278635.png)

**Resource quota dashboard**

On the Training Jobs page, click **Quota** in the upper-right corner to navigate to the resource quota dashboard.

You can view the following metrics in the resource quota dashboard:

-   **Quota\(cpu\)**
-   **Quota\(memory\)**
-   **Quota\(nvidia.com/gpu\)**
-   **Quota\(aliyun.com/gpu-mem\)**
-   **Quota\(aliyun.com/gpu\)**

Each metric in the preceding list displays the information about resource quotas in a table. The following information is displayed:

-   **Elastic Quota Name**: the name of the quota group.
-   **Namespace**: the namespace to which resources belong.
-   **Resource Name**: the type of resources.
-   **Max Quota**: the maximum amount of resources that you can use in the specified namespace.
-   **Min Quota**: the minimum amount of resources that you can use in the specified namespace when the cluster does not have sufficient resources.
-   **Used Quota**: the amount of resources that are used in the specified namespace.

![jk41](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/9249737261/p279120.png)

## Examples on how to work with AI dashboards

In this example, Arena is used to submit an `mpijob` training job. After you submit the training job, view the changes in the AI dashboards.

1.  After you log on to AI Dashboard, you are redirected to the cluster dashboard by default.

    The following figure shows how the cluster dashboard looks like when no training job is submitted to the cluster.

    ![jk51](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/9249737261/p279125.png)

    The preceding figure indicates that the cluster contains two GPU-accelerated nodes and two GPUs.

2.  Run the following command to submit an `mpijob` training job:

    ```
    arena submit mpijob \
        --name=mpi-test \
        --gpus=1 \
        --workers=1 \
        --working-dir=/perseus-demo/tensorflow-demo/ \
        --image=registry.cn-beijing.aliyuncs.com/ai-samples/horovod:0.13.11-tf1.10.0-torch0.4.0-py3.5 \
        'mpirun python /benchmarks/scripts/tf_cnn_benchmarks/tf_cnn_benchmarks.py --model resnet101 --batch_size 64 --num_batches 5000 --variable_update horovod --train_dir=/training_logs --summary_verbosity=3 --save_summaries_steps=10'
    ```

3.  Check the cluster dashboard again.

    ![jk52](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/9249737261/p279124.png)

    The preceding figure indicates that metrics such as GPU Utilization and GPUs \(Allocated/Total\) are updated.

4.  Run the following command to query the node where the job runs:

    ```
    arena list
    ```

    Expected output:

    ```
    NAME      STATUS   TRAINER  DURATION  GPU(Requested)  GPU(Allocated)  NODE
    mpi-test  RUNNING  MPIJOB   8s        1               1               192.168.1.151
    ```

5.  On the Cluster page, click **Nodes** in the upper-right corner to navigate to the node dashboard. The output returned in Step 4 indicates that the IP address of the node where the job runs is `192.168.1.151`. You can find that the GPU-related metrics of the node are updated, as shown in the following figure.

    ![jk53](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/9249737261/p279122.png)

6.  On the Nodes page, click **TrainingJobs** in the upper-right corner to navigate to the training job dashboard where you can view information about training jobs in the cluster, such as the names and status of training jobs.

    ![jk54](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/9249737261/p279123.png)


