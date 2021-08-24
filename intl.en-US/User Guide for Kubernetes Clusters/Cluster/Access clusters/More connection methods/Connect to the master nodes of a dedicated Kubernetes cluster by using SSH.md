---
keyword: [dedicated Kubernetes cluster, SSH logon, master node]
---

# Connect to the master nodes of a dedicated Kubernetes cluster by using SSH

The master nodes of a dedicated Kubernetes cluster are created and maintained by users. To facilitate cluster maintenance and management, Container Service for Kubernetes \(ACK\) allows you to connect to the master nodes of a dedicated Kubernetes cluster by using SSH. This topic describes how to enable SSH logon over the Internet for a dedicated Kubernetes cluster and how to connect to the master nodes of a dedicated Kubernetes cluster.

An elastic IP address \(EIP\) is associated with the API server of the dedicated Kubernetes cluster. For more information, see [Control public access to the API server of a cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Access clusters/Cluster access control/Control public access to the API server of a cluster.md).

-   When you create a dedicated Kubernetes cluster, if you select **Use SSH to Access the Cluster from the Internet**, ACK automatically generates an IP address for you to access the master nodes through SSH. You can view the IP address on the **Basic Information** tab of the cluster details page. You can use the IP address to log on to the master nodes of the dedicated Kubernetes cluster.

    ![master SSH](https://help-static-aliyun-doc.aliyuncs.com/assets/img/en-US/4409449261/p302024.png)

-   If you do not enable SSH logon over the Internet when you create the cluster, you must add a listener that listens on SSH port 22 to the Server Load Balancer \(SLB\) instance of the API server. Then, you can log on to the master nodes of the cluster by using SSH.

## Procedure

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane of the ACK console, click **Clusters**.

3.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  On the **Cluster Information** page, click the Cluster Resources tab. Find **APIServerSLB** and click the ID of the SLB instance.

5.  In the left-side navigation pane of the SLB console, choose **CLB \(FKA SLB\)** \> **Instances**. Click the **Listener** tab and then click **Add Listener**.

    The following is an example on how to add a listener that listens on SSH port 22 in a few steps. For more information about listener settings, see [Add a TCP listener](/intl.en-US/Classic Load Balancer/User Guide/Listeners/Add a TCP listener.md).

    1.  Select **TCP** as the listener protocol, set **Listening Port** to **22** \(SSH port\), and then click **Next**.

    2.  Select **Default Server Group**. In the **Servers Added** section, enter **22** in the Port column for all of the master nodes, set the weights of the master nodes based on your requirements, and then click **Next**.

    3.  On the **Health Check** wizard page, click **Next**.

    4.  On the **Confirm** wizard page, click **Submit**.

    After the listener is added, you can view the listener named **tcp\_22** on the **Listener** tab.

    ![ssh-listener](https://help-static-aliyun-doc.aliyuncs.com/assets/img/en-US/4409449261/p302066.png)

6.  On the **Instance Details** tab of the SLB instance, you can view the public IP address \(EIP\) of the SLB instance.

    You can use SSH to connect to the master nodes of the cluster through the EIP.

    ![Use SSH to connect to new ACK clusters](https://help-static-aliyun-doc.aliyuncs.com/assets/img/en-US/4319748261/p9054.png)

7.  Use the IP address to connect to the master nodes through SSH.

    When you create a cluster, you can select the following two logon modes: key pair logon and password logon. For more information, see [Create a dedicated Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Create Kubernetes clusters/Create a dedicated Kubernetes cluster.md). Select a logon mode based on your requirements:

    -   Password logon: The default username is root. The password is the same as the one that you have specified when you create the cluster.
    -   Key pair logon: For more information, see [Connect to a Linux instance by using an SSH key pair](/intl.en-US/Instance/Connect to instances/Connect to an instance by using third-party client tools/Connect to a Linux instance by using an SSH key pair.md).

