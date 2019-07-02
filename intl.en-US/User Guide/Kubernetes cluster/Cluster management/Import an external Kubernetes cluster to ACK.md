# Import an external Kubernetes cluster to ACK {#concept_473355 .concept}

This topic describes how to use the Container Service console to import an external Kubernetes cluster to Alibaba Cloud Container Service for Kubernetes \(ACK\). The external Kubernetes cluster can be a cluster that you created on your local host or one that you created on a platform of any other cloud vendors.

## Prerequisites {#section_7ek_yiv_lew .section}

Container Service, Resource Orchestration Service \(ROS\), Resource Access Management \(RAM\), and Auto Scaling service are activated.

**Note:** These services can be activated by logging on to the [Container Service console](https://cs.console.aliyun.com/), [ROS console](https://ros.console.aliyun.com/), [RAM console](https://ram.console.aliyun.com/), and [Auto Scaling console](https://essnew.console.aliyun.com).

## Limits {#section_5gf_ek4_mn3 .section}

-   The ACK cluster where the external cluster is to be imported cannot be used to modify the external cluster configurations.undefined

    You must log on to the external cluster to modify its configurations. For example, you can add and remove a node from the cluster, upgrade the cluster version, and modify parameters of a Kubernetes component.

-   When you attach tags to a cluster, the following rules apply:
    -   For each tag, a key is required, but its value is optional.
    -   A key can contain up to 64 characters but cannot start with `aliyun`, `http://`, or `https://`. It is not case sensitive.
    -   The value must be 1 to 128 characters in length, and cannot start with `http://` or `https://`. It is not case sensitive.
    -   For a cluster, each tag attached to it must be unique. If you attach a new tag that shares a key with an existing tag to a cluster, the new tag overwrites the existing tag.
    -   A maximum of 20 tags can be attached to a cluster. If you want to continue to attach more tags, you must first remove the necessary number of tags to allow these tags to be added.

## Procedure {#section_o03_hks_j9u .section}

1.  Create a Kubernetes cluster where your external Kubernetes cluster is to be imported.

    1.  Log on to the [Container Service console](https://cs.console.aliyun.com/).
    2.  Log on to the [Container Service console](https://partners-intl.console.aliyun.com/#/cs).
    3.  In the left-side navigation pane under Container Service-Kubernetes, choose **Clusters** \> **Clusters**.
    4.  In the upper-right corner, click **Create Kubernetes Cluster**. On the displayed Select Cluster Template page, find **Import Existing Cluster**, and click **Create**.

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/380912/156202986048661_en-US.png)

        The following is the **Import Existing Cluster** page.

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/380912/156202986148722_en-US.png)

    5.  Enter the cluster name.

        The cluster name must be 1 to 63 characters in length, and can contain letters, numbers, letters, and hyphens \(-\).

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/380912/156202986148611_en-US.png)

    6.  Select the region and zone where you want the cluster to be located.

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/380912/156202986148614_en-US.png)

    7.  Set the cluster network.

        **Note:** Kubernetes clusters support only the VPC network type.

        You can select a VPC and a Vswitch from the drop-down lists.

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/380912/156202986148615_en-US.png)

    8.  Associate an EIP with the cluster.

        **Note:** The EIP is used to connect the cluster \(created with ACK\) with the external cluster.

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/380912/156202986148725_en-US.png)

    9.  Attach tags to the cluster.

        Enter a key and its value, and click **Add**.

    10. Click **Create**.
    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/380912/156202986248728_en-US.png)

2.  Import the external cluster into the created cluster.
    1.  Find the cluster you created. Then, in the **Action** column, click **Manage**.
    2.  In the **Cluster Resource** area, click the **Cluster Agent Configuration** tab.
    3.  Click **Copy** to copy the code into the file agent.yaml.
    4.  In the external cluster, run the kubectl apply -f agent.yaml command to deploy a cluster agent.

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/380912/156202986248732_en-US.png)

    5.  In the external cluster, run the kubectl get all -n ack-system command to view the agent status.

        ``` {#codeblock_1qu_7ei_pbr}
        NAME                                     READY   STATUS    RESTARTS   AGE
        pod/ack-cluster-agent-655b75c987-dwp6b   1/1     Running   0          9s
        
        NAME                                DESIRED   CURRENT   UP-TO-DATE   AVAILABLE   AGE
        deployment.apps/ack-cluster-agent   1         1         1            1           26m
        
        NAME                                           DESIRED   CURRENT   READY   AGE
        replicaset.apps/ack-cluster-agent-655b75c987   1         1         1       26m
        ```

    6.  Return to the Cluster List page, verify that the cluster you created is in the Running status.

        You can click **Manage** in the **Action** column to view the information and resources of the cluster.

        **Note:** In the **Cluster Information** area, you can find **API Server Internet endpoint** that provides you with the Internet IP address and its associated port of the cluster API server. With the endpoint, you can use kubectl or other tools on your terminal to manage the cluster.

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/380912/156202986248729_en-US.png)


## What to do {#section_fae_ze8_1f1 .section}

Connect to the created cluster by using kubectl \(for more information, see [kubectl](reseller.en-US/User Guide/Kubernetes cluster/Cluster management/Connect to a Kubernetes cluster by using kubectl.md#)\), you can do the following:

-   View the cluster nodes by running the kubectl get node command.
-   Use its kubeconfig file to connect to the external Kubernetes cluster at the remote end, and then deploy application workloads on the external cluster.

