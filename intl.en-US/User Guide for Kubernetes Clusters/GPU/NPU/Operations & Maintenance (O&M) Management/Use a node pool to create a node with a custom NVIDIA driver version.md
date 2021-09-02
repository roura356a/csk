---
keyword: create a node with a custom NVIDIA driver version
---

# Use a node pool to create a node with a custom NVIDIA driver version

By default, Container Service for Kubernetes \(ACK\) clusters are installed with the NVIDIA driver of version 418.87.01. If your CUDA Toolkit requires a higher version of the NVIDIA driver, you must specify a custom NVIDIA driver version for the cluster nodes. This topic describes how to use a node pool to create a node with a custom NVIDIA driver version.

## Benefits

This solution allows you to manage the NVIDIA drivers of different nodes in batches. The following two scenarios are covered:

-   Node Pool A consists of nodes on which you want to install the NVIDIA driver of version 418.181.07. To schedule a task to a node that runs the NVIDIA driver of version 418.181.07, you need only to set the **selector** of the task to the label of Node Pool A.
-   You manage cluster nodes in two groups: A and B. You want to install the NVIDIA driver of version 418.181.07 in Group A and the NVIDIA driver of version 450.102.0 in Group B. In this case, you can add nodes in Group A to Node Pool A and nodes in Group B to Node Pool B.

## Step 2: Create a node pool and specify the NVIDIA driver version

**Method 1: Select an NVIDIA driver version provided by ACK**

The following example shows how to set the NVIDIA driver version to 418.181.07:

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane of the ACK console, click **Clusters**.

3.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  In the left-side navigation pane of the details page, choose **Nodes** \> **Node Pools**.

5.  In the upper-right corner of the Node Pools page, click **Create Node Pool**.

6.  In the **Create Node Pool** dialog box, configure the required parameters. For more information about the parameters, see [Create a dedicated Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Create Kubernetes clusters/Create a dedicated Kubernetes cluster.md). The following table describes the parameters:

    1.  Click **Show Advanced Options**.

    2.  In the **Node Label** section, click ![1](https://help-static-aliyun-doc.aliyuncs.com/assets/img/en-US/5146487161/p245638.png), set **Key** to `ack.aliyun.com/nvidia-driver-version`, and then set **Value** to `418.181.07`.

        ACK provides the following NVIDIA driver versions:

        -   418.181.07
        -   450.102.04
        -   460.32.03
    3.  After you set the parameters, click **Confirm Order**.


**Method 2: Use a custom NVIDIA driver version**

**Custom driver version**

The following example shows how to upload the NVIDIA-Linux-x86\_64-460.32.03.run file to specify a custom driver version. You can download NVIDIA driver files from the [NVIDIA official website](https://www.nvidia.com/Download/index.aspx?lang=en-us).

**Note:** The NVIDIA-Linux-x86\_64-460.32.03.run file must be stored in the root directory of the specified Object Storage Service \(OSS\) bucket.

1.  Create an OSS bucket in the [OSS console](https://oss.console.aliyun.com/). For more information, see [Create buckets](/intl.en-US/Console User Guide/Manage buckets/Create buckets.md).

2.  Upload the NVIDIA-Linux-x86\_64-460.32.03.run file to the OSS bucket. For more information, see [Upload objects](/intl.en-US/Console User Guide/Upload, download, and manage objects/Upload objects.md).

3.  After the file is uploaded to the bucket, click **Files** in the left-side navigation pane of the bucket details page.

4.  On the **Files** page, find the file that you uploaded and click **View Details** in the **Actions** column.

5.  In the **View Details** panel, turn off the **HTTPS** switch to disable HTTPS.

    **Note:** ACK downloads the driver file by using its URL, which uses the HTTP protocol. By default, OSS uses the HTTPS protocol. You must turn off the **HTTPS** switch to disable HTTPS.

6.  Check the URL of the driver file. Record the URL. The URL consists of the following parts: endpoint and runfile. For example, you can divide the following URL into two parts: `[http://nvidia-XXX-XXX-cn-beijing.aliyuncs.com/NVIDIA-Linux-x86\_64-460.32.03.run](http://nvidia-XXX-XXX-cn-beijing.aliyuncs.com/NVIDIA-Linux-x86_64-460.32.03.run)`.

    -   endpoint: nvidia-XXX-XXX-cn-beijing.aliyuncs.com
    -   runfile: NVIDIA-Linux-x86\_64-460.32.03.run

**Create a node pool with the custom driver version**

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane of the ACK console, click **Clusters**.

3.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  In the left-side navigation pane of the details page, choose **Nodes** \> **Node Pools**.

5.  In the upper-right corner of the Node Pools page, click **Create Node Pool**.

6.  In the **Create Node Pool** dialog box, set the required parameters. For more information about the parameters, see [Create a dedicated Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Create Kubernetes clusters/Create a dedicated Kubernetes cluster.md). The following table describes the parameters:

    1.  Click **Show Advanced Options**.

    2.  In the **Node Label** section, click ![1](https://help-static-aliyun-doc.aliyuncs.com/assets/img/en-US/5146487161/p245638.png) twice to add the following two labels:

        -   For the first label, set **Key** to `ack.aliyun.com/nvidia-driver-oss-endpoint` and **Value** to `nvidia-XXX-XXX-cn-beijing.aliyuncs.com`.
        -   For the second label, set **Key** to `ack.aliyun.com/nvidia-driver-runfile` and **Value** to `NVIDIA-Linux-x86_64-460.32.03.run`.
    3.  After you set the parameters, click **Confirm Order**.


**Method 3: Call an API operation to specify the NVIDIA driver version**

You can call the CreateClusterNodePool operation to create a node pool with a specified NVIDIA driver version. For more information, see [Create a node pool](/intl.en-US/API Reference/Node pools/Create a node pool.md).

1. Specify the driver version in the API request

When you call API operations to create or scale out a cluster, you need only to add a specific tag to the `tags` field in the request `body`. The following sample code shows how to add a tag to specify the driver version:

```
{
  // Other fields are omitted.
  ......
    "tags": [
        {
            "key": "ack.aliyun.com/nvidia-driver-version",
            "value": "410.104"
        }
    ],
  // Other fields are omitted.
  ......
}
```

2. Specify the URL of the driver file in the API request

To specify the URL of the driver file in your API request, provide the `endpoint` and `runfile` values of the URL. These values indicate the location of the driver file in the OSS bucket. The following code provides an example: For more information about how to obtain the `endpoint` and `runfile` values of an object stored in an OSS bucket, see [Check the URL of the driver file](#step_4ru_5y3_a84).

```
{
  // Other fields are omitted.
  ......
    "tags": [
      {
              "key": "ack.aliyun.com/nvidia-driver-oss-endpoint",
              "value": "nvidia-XXX-XXX-cn-beijing.aliyuncs.com"
      },
      {
        "key": "ack.aliyun.com/nvidia-driver-runfile",
        "value": "NVIDIA-Linux-x86_64-410.104.run"
      }
    ],
  // Other fields are omitted.
  ......
}
```

## Step 3: Check whether the custom NVIDIA driver version is installed

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane of the ACK console, click **Clusters**.

3.  In the ACK console, select the cluster to which the node belongs and click **More** \> **Open Cloud Shell** in the **Actions** column.

4.  Run the following command to query pods that have the `component: nvidia-device-plugin` label:

    ```
    kubectl get po -n kube-system -l component=nvidia-device-plugin -o wide
    ```

    Expected output:

    ```
    NAME                                            READY   STATUS    RESTARTS   AGE   IP              NODE                       NOMINATED NODE   READINESS GATES
    nvidia-device-plugin-cn-beijing.192.168.1.127   1/1     Running   0          6d    192.168.1.127   cn-beijing.192.168.1.127   <none>           <none>
    nvidia-device-plugin-cn-beijing.192.168.1.128   1/1     Running   0          17m   192.168.1.128   cn-beijing.192.168.1.128   <none>           <none>
    nvidia-device-plugin-cn-beijing.192.168.8.12    1/1     Running   0          9d    192.168.8.12    cn-beijing.192.168.8.12    <none>           <none>
    nvidia-device-plugin-cn-beijing.192.168.8.13    1/1     Running   0          9d    192.168.8.13    cn-beijing.192.168.8.13    <none>           <none>
    nvidia-device-plugin-cn-beijing.192.168.8.14    1/1     Running   0          9d    192.168.8.14    cn-beijing.192.168.8.14    <none>           <none>
    ```

    You can check the NODE column to find the node that is newly added to the cluster. The name of the pod that runs on the node is `nvidia-device-plugin-cn-beijing.192.168.1.128`.

5.  Run the following command to query the NVIDIA driver version of the node:

    ```
    kubectl exec -ti nvidia-device-plugin-cn-beijing.192.168.1.128 -n kube-system -- nvidia-smi 
    ```

    Expected output:

    ```
    Sun Feb  7 04:09:01 2021       
    +-----------------------------------------------------------------------------+
    | NVIDIA-SMI 418.181.07   Driver Version: 418.181.07   CUDA Version: N/A      |
    |-------------------------------+----------------------+----------------------+
    | GPU  Name        Persistence-M| Bus-Id        Disp.A | Volatile Uncorr. ECC |
    | Fan  Temp  Perf  Pwr:Usage/Cap|         Memory-Usage | GPU-Util  Compute M. |
    |===============================+======================+======================|
    |   0  Tesla V100-SXM2...  On   | 00000000:00:07.0 Off |                    0 |
    | N/A   27C    P0    40W / 300W |      0MiB / 16130MiB |      0%      Default |
    +-------------------------------+----------------------+----------------------+
    |   1  Tesla V100-SXM2...  On   | 00000000:00:08.0 Off |                    0 |
    | N/A   27C    P0    40W / 300W |      0MiB / 16130MiB |      0%      Default |
    +-------------------------------+----------------------+----------------------+
    |   2  Tesla V100-SXM2...  On   | 00000000:00:09.0 Off |                    0 |
    | N/A   31C    P0    39W / 300W |      0MiB / 16130MiB |      0%      Default |
    +-------------------------------+----------------------+----------------------+
    |   3  Tesla V100-SXM2...  On   | 00000000:00:0A.0 Off |                    0 |
    | N/A   27C    P0    41W / 300W |      0MiB / 16130MiB |      0%      Default |
    +-------------------------------+----------------------+----------------------+
                                                                                   
    +-----------------------------------------------------------------------------+
    | Processes:                                                       GPU Memory |
    |  GPU       PID   Type   Process name                             Usage      |
    |=============================================================================|
    |  No running processes found                                                 |
    +-----------------------------------------------------------------------------+
    ```

    The output shows that the NVIDIA driver version is 418.181.07. This indicates that the specified NVIDIA driver is installed.


**Related topics**  


[Use a node pool to upgrade the NVIDIA driver for a node](/intl.en-US/User Guide for Kubernetes Clusters/GPU/NPU/Operations & Maintenance (O&M) Management/Use a node pool to upgrade the NVIDIA driver for a node.md)

