# Create a gn5 Kubernetes cluster {#concept_mq2_v32_vdb .concept}

The Kubernetes deep learning solution supports creating a Kubernetes cluster that contains Elastic Compute Service \(ECS\) instances or Elastic GPU Service \(GPU\) instances as nodes. This topic uses the Kubernetes GPU cluster as an example to describe how to create a Kubernetes cluster.

**Note:** For more information about how to create a Kubernetes cluster that contains ECS instances as nodes, see [Create a Kubernetes cluster](../../../../reseller.en-US//Create a Kubernetes cluster.md#).

The Kubernetes GPU scheduling solution is based on both the official plug-in and nvidia-container-runtime provided by NVIDIA. In comparison to the open source Kubernetes solutions, the Kubernetes GPU scheduling solution provided by Alibaba Cloud requires less configuration.

Based on this solution, you can use the container technology to build an image for a containerized application. Integrating Kubernetes with GPU clusters to perform high-density computing tasks, such as deep learning and imaging. This solution helps you simplify the deployment and auto scaling configuration without the need to install NVIDIA drivers and CUDA.

The following sections describe how to create a Kubernetes GPU cluster in Alibaba Cloud Container Service.

## Limits {#section_amc_bk2_vdb .section}

-   Currently, you can only create gn5 Kubernetes GPU clusters in VPCs.
-   To create a pay-as-you-go instance or SLB instance, you must have an Alibaba Cloud account with a balance of CNY 100. The account must have passed the real-name verification.
-   The Kubernetes cluster version for deep learning solution must be 1.9.3 or later version.

## Prerequisites {#section_b4z_1j2_vdb .section}

To buy pay-as-you-go GPU instances of the gn instance type, you need to [submit a ticket](https://selfservice.console.aliyun.com/ticket/scene/ecs/%E4%BA%91%E6%9C%8D%E5%8A%A1%E5%99%A8%20ECS/detail) and enter the following reason.

I need to apply for pay-as-you-go GPU instances of the gn5 instance type. Please activate it. Thank you.

## Procedure {#section_c4z_1j2_vdb .section}

1.  Log on to the [Container Service console](https://cs.console.aliyun.com/).
2.  In the top of the left-side navigation pane, switch to Container Service for Kubernetes and select **Clusters** to open the Clusters page.
3.  In the upper-right corner, click **Create Kubernetes Cluster**.

    ![](images/9083_en-US.png)

4.  Configure the basic information, as shown in the following figure.

    ![](images/9085_en-US.png)

5.  Select an instance type for the worker node. This example uses a gn5 instance type.

    ![](images/9089_en-US.png)

6.  To enable SSH logon for connecting to Kubernetes master nodes, select the Allow Using SSH to Log on to Clusters from the Internet check box. Note that you must enable public access by selecting Expose API Server with EIP before you enable SSH logon.

    ![](images/9090_en-US.png)

7.  For more information about how to configure other information, see [Create a Kubernetes cluster](../../../../reseller.en-US//Create a Kubernetes cluster.md#).
8.  After the cluster is created, click **Clusters** in the left-side navigation pane, and you can see the created GPU cluster in the cluster list.

    ![](images/9091_en-US.png)

9.  In the Action column of the cluster, click **Manage** to open the Basic Information page and view the IP address of the master node for SSH logon.

    ![](images/9092_en-US.png)

10. To view the GPU nodes in the Kubernetes cluster, use an SSH client to connect to the master node and run the following command:

    ```
     $ kubectl get nodes -l 'aliyun.accelerator/nvidia_name' --show-labels
     ...                               
     NAME                                 STATUS    ROLES     AGE       VERSION   LABELS
     cn-hangzhou.i-bp12xvjjwqe6j7nca2q8   Ready     <none>    1h        v1.9.3    aliyun.accelerator/nvidia_count=1,aliyun.accelerator/nvidia_mem=16276MiB,aliyun.accelerator/nvidia_name=Tesla-P100-PCIE-16GB, ..
    ```

11. View the status of the GPU node.

    ```
    $ kubectl get node ${node_name} -o=yaml
     ...
     status:
       addresses:
       - address: 172.16.166.23
         type: InternalIP
       allocatable:
         cpu: "8"
         memory: 61578152Ki
         nvidia.com/gpu: "1"
         pods: "110"
       capacity:
         cpu: "8"
         memory: 61680552Ki
         nvidia.com/gpu: "1"
         pods: "110"
     ...
    ```


The Kubernetes GPU cluster is created.

