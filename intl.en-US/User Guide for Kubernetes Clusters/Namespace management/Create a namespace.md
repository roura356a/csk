# Create a namespace

This topic describes how to create a namespace in a Kubernetes cluster.

[Create a cluster of ACK Proprietary Edition](/intl.en-US/User Guide for Kubernetes Clusters/Cluster management/Create Kubernetes clusters/Create a cluster of ACK Proprietary Edition.md)

In a Kubernetes cluster, you can use namespaces to create multiple virtual clusters. If multiple users share a cluster, you can create namespaces to divide workspaces and assign cluster resources to different tasks. You can use [resource quotas](https://kubernetes.io/docs/concepts/policy/resource-quotas/) to assign resources to namespaces.

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane, click **Clusters**.

3.  On the Clusters page, click the name of a cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  In the left-side navigation pane, click **Namespaces**.

5.  On the page that appears, click **Create** in the upper-right corner.

6.  In the dialog box that appears, configure the parameters.

    ![Configure parameters for a namespace](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/3555359951/p10650.png)

    -   **Name**: Enter a namespace name. In this example, set the value to test. The name must be 1 to 63 characters in length, and can contain digits, letters, and hyphens \(-\). It must start and end with a letter or a digit.
    -   **Label**: Add one or more labels to the namespace. You can use labels to identify the attributes of the namespace. In this example, use a key-value pair to identify that the namespace is used in a test environment.

        To add a label, enter a key and a value and click **Add** in the Actions column.

7.  Click **OK**.

    Return to the Namespace page. In the list of namespaces, the test namespace appears.


