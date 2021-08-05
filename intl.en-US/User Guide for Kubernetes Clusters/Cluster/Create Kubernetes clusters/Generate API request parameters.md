# Generate API request parameters

This topic describes how to generate API request parameters to create a Container Service for Kubernetes \(ACK\) cluster in the ACK console. When you fail to create an ACK cluster due to invalid parameter settings, you can use this feature to generate API request parameters. Then, you can create the ACK cluster by calling the API.

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane of the ACK console, click **Clusters**.

3.  In the upper-right corner of the Clusters page, click **Cluster Template**.

4.  In the Select Cluster Template dialog box, select the type of the cluster that you want to create and click **Create**.

    On the cluster configuration page that appears, set the parameters. For more information about the parameters, see [Create a dedicated Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Create Kubernetes clusters/Create a dedicated Kubernetes cluster.md).

5.  Set the cluster parameters.

    To generate API request parameters, you must first specify a set of required parameters.

    -   For more information about the parameters that are required to create a dedicated Kubernetes cluster, see [Create a dedicated Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Create Kubernetes clusters/Create a dedicated Kubernetes cluster.md).
    -   For more information about the parameters that are required to create a managed Kubernetes cluster, see [CreateCluster](/intl.en-US/API Reference/Clusters/Create a cluster/Create a managed Kubernetes cluster.md).
6.  After you specify the required parameters, click **Generate API Request Parameters**.

    In the API Request Parameters dialog box, you can view the parameters that you have specified.

7.  Click **Copy** to copy the cluster configurations.

    You can directly call the API to create an ACK cluster by using the copied cluster configurations. For more information, see [Create a dedicated Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Create Kubernetes clusters/Create a dedicated Kubernetes cluster.md) and [CreateCluster](/intl.en-US/API Reference/Clusters/Create a cluster/Create a managed Kubernetes cluster.md).

    You can also use the copied cluster configurations to create an ACK cluster by using Alibaba Cloud command-line interface \(CLI\). For more information, see [Create a cluster](/intl.en-US/Use Container Service through CLI/Create a cluster.md).


