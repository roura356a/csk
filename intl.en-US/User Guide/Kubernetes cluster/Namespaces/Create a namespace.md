# Create a namespace {#task_kms_ztx_b2b .task}

You have created a Kubernetes cluster. For more information, see [Create a Kubernetes cluster](intl.en-US/User Guide/Kubernetes cluster/集群管理/Create a Kubernetes cluster.md#).

In Kubernetes clusters, you can use the Namespace function to create multiple virtual spaces. When the number of users in one cluster is large, multiple namespaces are used to divide the workspaces effectively and the cluster resources into different purposes. The namespace resources are assigned by using the [resource-quotas](https://kubernetes.io/docs/concepts/policy/resource-quotas/).

1.  Log on to the [Container Service console](https://cs.console.aliyun.com/). 
2.  Under Kubernetes, click **Clusters** \> **Namespace** in the left-side navigation pane. 
3.  Select the cluster from the Clusters drop-down list and then click **Create** in the upper-right corner. 

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/18854/153829136710649_en-US.png)

4.  Configure the namespace in the displayed dialog box. 

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/18854/153829136710650_en-US.png)

    -   **Name**: Enter the namespace name, which is 1–63 characters long, can only contain numbers, letters, and hyphens \(-\), and must start and end with a letter or number. In this example, enter test as the name.
    -   **Tags**: Add one or more tags for the namespace to identify the characteristics of the namespace, for example, to identify this namespace to be used for the test environment.

        You can enter the variable name and variable value, and then click **Add** on the right to add a tag for the namespace.

5.  Click **OK** after completing the configurations. 
6.  The namespace test is successfully created and displayed in the namespace list. 

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/18854/153829136710651_en-US.png)


