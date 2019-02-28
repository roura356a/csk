# Create a namespace {#task_kms_ztx_b2b .task}

This topic describes how to create a namespace.

You have created a Kubernetes cluster. For more information, see [Create a Kubernetes cluster](reseller.en-US/User Guide/Kubernetes cluster/Cluster management/Create a Kubernetes cluster.md#).

In a Kubernetes cluster, you can use namespaces to create multiple virtual spaces. When a large number of users share a cluster, multiple namespaces can be used to effectively divide different work spaces and assign cluster resources to different tasks. Furthermore, you can use [resource quotas](https://kubernetes.io/docs/concepts/policy/resource-quotas/) to assign resources to each namespace.

1.  Log on to the [Container Service console](https://partners-intl.console.aliyun.com/#/cs). 
2.  In the left-side navigation pane under Kubernetes, choose **Clusters** \> **Namespace**. 
3.  Select the target cluster, and then click **Create** in the upper-right corner. 

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/18871/155131876838715_en-US.png)

4.  In the displayed dialog box, set a namespace. 

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/18854/155131876810650_en-US.png)

    -   **Name**: Enter a name for the namespace name. The name must be 1 to 63 characters in length and can contain numbers, letters, and hyphens \(-\). It must start and end with a letter or number. In this example, test is used as the name.
    -   **Tags**: Add one or multiple tags to the namespace to identify the characteristics of the namespace. For example, you can set a tag to identify that this namespace is used for the test environment.

        You can enter a variable name and a variable value, and then click **Add** on the right to add a tag to the namespace.

5.  Click **OK**. 
6.  The namespace named test is displayed in the namespace list. 

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/18871/155131876838716_en-US.png)


