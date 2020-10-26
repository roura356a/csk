# Edit a namespace

This topic describes how to add, delete, or modify labels of a namespace in a Kubernetes cluster.

-   A cluster is created. For more information, see [Create a cluster of ACK Managed Edition](/intl.en-US/User Guide for Kubernetes Clusters/Cluster management/Create Kubernetes clusters/Create a cluster of ACK Managed Edition.md).
-   A namespace is created. The following example is based on namespace test. For more information, see [Create a namespace](/intl.en-US/User Guide for Kubernetes Clusters/Namespace management/Create a namespace.md).

Container Service for Kubernetes \(ACK\) allows you to edit a namespace. You can add, delete, or modify labels of a namespace.

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane, click **Clusters**.

3.  On the Clusters page, click the name of a cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  In the left-side navigation pane, click **Namespaces**.

5.  Select the namespace that you want to edit and click **Edit** in the Actions column.

    ![Edit a namespace](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/5555359951/p10709.png)

6.  In the dialog box that appears, find the label that you want to edit and click **Edit**. In this example, the key-value pair of the label changes to `env:test-V2`. Then, click **Save**.

    ![Edit a label](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/5555359951/p10710.png)

7.  Click **OK**. Return to the Namespace page and verify that the label is edited.


