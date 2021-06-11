---
keyword: [dataset, AI Dashboard]
---

# Manage datasets

A dataset is a set of data that has similar attributes or is used by the same targets. You can update datasets and delete data of different sources from datasets. This topic describes how to manage datasets in AI Dashboard.

-   The cloud-native AI component set is installed. For more information, see [Deploy cloud-native AI component set](/intl.en-US/Cloud-native AI user guide/Environment preparation/Deploy cloud-native AI component set.md).
-   The credentials of the administrator of AI Dashboard are obtained.

## Create an accelerated dataset based on an existing PVC

1.  Log on to AI Dashboard by using the credentials of the administrator.

2.  In the left-side navigation pane of AI Dashboard, choose **Dataset** \> **Dataset List**.

3.  Select the dataset that you want to accelerate and click **Accelerate** in the **Operator** column.

    **Note:** If no other types of data exist, the Dataset List page displays all persistent volume claims \(PVCs\) in the current cluster.

4.  In the **Edit Dataset** dialog box, set the following parameters:

    -   Specify a **name** for the accelerated dataset and select the **namespace** to which the dataset belongs.
    -   Select **PVC** for **Source Type**, set **Data Source**, and then set **Sub Dir**.
    -   Set **Runtime Config**. The name field must be set to the name of the dataset.

        The following code is provided as a sample template of the **runtime configurations**:

        ```
        apiVersion: data.fluid.io/v1alpha1
        kind: AlluxioRuntime
        metadata:
          name: fluid-imagenet
        spec:
          replicas: 4
          data:
            replicas: 1
          tieredstore:
            levels:
              - mediumtype: SSD
                path: /var/lib/docker/alluxio
                quota: 150Gi
                high: "0.99"
                low: "0.8"
        ```

5.  Click **Save**. Verify that the **state** of the dataset becomes **NotReady**. Wait a few seconds, and then verify that the **state** of the dataset is changed to **Ready**.


## Create an accelerated dataset based on Object Storage Service \(OSS\)

**Step 1: Create a Secret**

The OSS configuration is sensitive information and must be transferred in an encrypted form. You must create a Secret. The following YAML template is provided as an example:

```
apiVersion: v1
kind: Secret
metadata:
  name: imagenet-oss-xxx
  namespace: default
type: kubernetes.io/basic-auth
stringData:
  username: <ACCESS_ID>
  password: <ACCESS_KEY>
```

Run the following command to create a Secret:

```
kubectl create -f oss_access_secret.yaml
```

**Step 2: Create an accelerated dataset based on OSS**

1.  Log on to AI Dashboard by using the credentials of the administrator.

2.  In the left-side navigation pane of AI Dashboard, choose **Dataset** \> **Dataset List**.

3.  Click **Add**.

4.  In the **Create Dataset** dialog box, set the following parameters:

    1.  Specify a **name** for the dataset that you want to create and select the **namespace** to which the dataset belongs.

    2.  Select **OSS** for **Source Type**. The parameters are described in the following table.

        ![AI-6](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/1362933261/p239579.png)

        |Parameter|Example|Description|
        |---------|-------|-----------|
        |Name|oss-imagenet|You can enter a custom name.|
        |Namespace|default|Set the value to the `namespace` that is specified in the configurations of the Secret that you created. For more information, see [Create a Secret](#section_2jz_umo_piw).|
        |Data Source|oss://my-dawnbench-v2/|N/A|
        |Sub Dir|mydawnbench-v2|You can enter a custom subdirectory.|
        |endpoint|oss-xxx.aliyuncs.com|N/A|
        |accessKeyId|        -   secret: imagenet-oss-xxx
        -   secretKey: username
|        -   Set **secret** to the `name` of the Secret that you created. For more information, see [Create a Secret](#section_2jz_umo_piw).
        -   Set **secretkey** to the `username` that is specified in the configurations of the Secret that you created. For more information, see [Create a Secret](#section_2jz_umo_piw). |
        |accessKeySecret|        -   secret: imagenet-oss-xxx
        -   secretKey: password
|        -   Set **secret** to the `name` of the Secret that you created. For more information, see [Create a Secret](#section_2jz_umo_piw).
        -   Set **secretkey** to the `password` that is specified in the configurations of the Secret that you created. For more information, see [Create a Secret](#section_2jz_umo_piw). |

    3.  To make sure that the dataset is created on the node that you want to access, run the following command to add a node affinity:

        ```
        aliyun.accelerator/nvidia_name in Tesla-V100-SXM2-16GB;Tesla-V100-SXM2-32GB
        ```

    4.  Set **Runtime Config**. The name field must be set to the name of the dataset.

        The following code is a sample template of the **runtime configurations**:

        ```
        apiVersion: data.fluid.io/v1alpha1
        kind: JindoRuntime
        metadata:
          name: oss-imagenet
        spec:
          replicas: 2
          data:
            replicas: 1
          tieredstore:
            levels:
              - mediumtype: SSD
                path: /var/lib/docker/alluxio
                quota: 150Gi
                high: '0.99'
                low: '0.8'
        ```

5.  Click **Save**. Verify that the **state** of the dataset becomes **NotReady**. Wait a few seconds, and then verify that the **state** of the dataset is changed to **Ready**.


## Create an accelerated dataset based on other stores

1.  Log on to AI Dashboard by using the credentials of the administrator.

2.  In the left-side navigation pane of AI Dashboard, choose **Dataset** \> **Dataset List**.

3.  Click **Add**.

4.  In the **Create Dataset** dialog box, set the following parameters:

    -   Specify a **name** for the dataset that you want to create and select the **namespace** to which the dataset belongs.
    -   Select **Others** for **Source Type**, set **Data Source**, and then set **Sub Dir**.
    -   Set **Runtime Config**. The name field must be set to the name of the dataset.

        The following code is provided as a sample template of the **runtime configurations**:

        ```
        apiVersion: data.fluid.io/v1alpha1
        kind: AlluxioRuntime
        metadata:
          name: fluid-imagenet
        spec:
          replicas: 4
          data:
            replicas: 1
          tieredstore:
            levels:
              - mediumtype: SSD
                path: /var/lib/docker/alluxio
                quota: 150Gi
                high: "0.99"
                low: "0.8"
        ```

5.  Click **Save**. Verify that the **state** of the dataset becomes **NotReady**. Wait a few seconds, and then verify that the **state** of the dataset is changed to **Ready**.


## Delete a dataset

**Note:** General users cannot delete datasets that are not accelerated. Only the RAM user that is configured as the administrator can delete datasets.

1.  Log on to AI Dashboard as the administrator.

2.  Find the dataset that you want to delete and click **Delete** in the **Operator** column.


