---
keyword: [cGPU, cGPU Basic Edition, cGPU Professional Edition, upgrade]
---

# Upgrade cGPU Basic Edition to cGPU Professional Edition in a professional Kubernetes cluster

If cGPU Basic Edition is installed in a dedicated Kubernetes cluster, cGPU cannot work as normal after you migrate the cluster workloads to a professional Kubernetes cluster. Professional Kubernetes clusters support only cGPU Professional Edition. In this case, you must upgrade cGPU Basic Edition to cGPU Professional Edition in the professional Kubernetes cluster after the migration is completed. This topic describes how to upgrade cGPU Basic Edition to cGPU Professional Edition in a professional Kubernetes cluster.

Workloads are migrated from a dedicated Kubernetes cluster to a professional Kubernetes cluster. cGPU Basic Edition is installed in the dedicated Kubernetes cluster before the migration. For more information, see [Hot migration from dedicated Kubernetes clusters to professional managed Kubernetes clusters](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Migrate to professional managed Kubernetes clusters/Hot migration from dedicated Kubernetes clusters to professional managed Kubernetes clusters.md).

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane of the ACK console, click **Clusters**.

3.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  In the left-side navigation pane of the details page, choose **Workloads** \> **Jobs**.

5.  On the Jobs page, click **Create from YAML** in the upper-right corner.

6.  On the Create page, set **Sample Template** to **Custom**. Copy the following YAML template to the **Template** section.

    This template is used to create a Job that uninstalls cGPU Basic Edition and modifies the labels of GPU-accelerated nodes.

    ```
    apiVersion: batch/v1
    kind: Job
    metadata:
      name: gpushare-migration
      namespace: kube-system
    spec:
      backoffLimit: 0
      template:
        spec:
          serviceAccount: admin
          containers:
          - name: gpushare-migration
            # Replace <cn-beijing> in the following image address with the ID of the region where the cluster is deployed. 
            image: registry-vpc.cn-beijing.aliyuncs.com/acs/gpushare-migration:v0.1.0
            env:
              - name: CHANGE_LABELS_INFO
                value: "cgpu=true::ack.node.gpu.schedule=cgpu,gpushare=true::ack.node.gpu.schedule=share"
          restartPolicy: Never
    ```

7.  Click **Create**. Click the Job name **gpushare-migration** to view the deployment progress.

    ![gpushare-migration](https://help-static-aliyun-doc.aliyuncs.com/assets/img/en-US/5518621361/p311696.png)

    On the details page of the **gpushare-migration** Job, click the Pods tab. If the state of the **pod** is Completed, it indicates that the Job succeeds.

8.  Install cGPU Professional Edition. For more information, see [Step 1: Install ack-ai-installer](/intl.en-US/User Guide for Kubernetes Clusters/GPU/NPU/GPU scheduling/cGPU Professional Edition/Install and use ack-ai-installer and the GPU inspection tool.md).

9.  Install a GPU memory inspection tool in the cluster. For more information, see [Step 4: Install and use the GPU scheduling inspection tool](/intl.en-US/User Guide for Kubernetes Clusters/GPU/NPU/GPU scheduling/cGPU Professional Edition/Install and use ack-ai-installer and the GPU inspection tool.md).


For more information about how to test the GPU sharing, GPU scheduling, and GPU memory isolation features of cGPU Professional Edition, see [Enable GPU sharing](/intl.en-US/User Guide for Kubernetes Clusters/GPU/NPU/GPU scheduling/cGPU Professional Edition/Enable GPU sharing.md).

