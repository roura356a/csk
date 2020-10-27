# Use SSH key pairs to connect to an ACK cluster

This topic describes how to use SSH key pairs to log on to a cluster of Container Service for Kubernetes \(ACK\). This logon method secures remote access to ACK clusters.

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane, click **Clusters**.

3.  On the Clusters page, click **Create Kubernetes Cluster** in the upper-right corner of the page.

4.  On the **Select Cluster Template** page, select a cluster type and click **create**.

5.  Set the logon type to SSH key pair. Set the remaining parameters that are required to create an ACK cluster and click **Create Cluster**. For more information about the parameters, see [Create a cluster of ACK Managed Edition](/intl.en-US/User Guide for Kubernetes Clusters/Cluster management/Create Kubernetes clusters/Create a cluster of ACK Managed Edition.md).

    1.  If you have created an SSH key pair in the Elastic Compute Service \(ECS\) console, you can select it from the Key Pair drop-down list.
    2.  If no SSH key pair is available, click **create a key pair** to create one in the ECS console. For more information, see [Create an SSH key pair](/intl.en-US/Security/Key pairs/Use an SSH key pair/Create an SSH key pair.md).
    ![Create an SSH key pair.](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/7435359951/p10883.png)

6.  After the cluster is created, go to the Clusters page. On the Clusters page, find the newly created cluster and click **Details** in the Actions column. On the **Basic Information** tab, you can obtain the **IP address for SSH connection** to a master node, as shown in the following figure.

    ![Connect to a cluster](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/7435359951/p10889.png)

7.  Download the .pem private key file and configure SSH logon based on the operating system \(Windows or Linux\) of your local machine. For more information, see [Create a cluster of ACK Managed Edition](/intl.en-US/User Guide for Kubernetes Clusters/Cluster management/Create Kubernetes clusters/Create a cluster of ACK Managed Edition.md). In this example, the Linux operating system is used.

    1.  Find the downloaded .pem private key file, for example, `/root/xxx.pem`.

    2.  Run the following command to modify the attributes of the file: `chmod 400 [path of .pem]`. Example: `chmod 400 /root/xxx.pem`.

    3.  Run the following command to use SSH to connect the cluster: `ssh -i [path of .pem] root@[master-public-ip]`. Replace master-public-ip with the IP address that you have obtained in Step 7, for example, `ssh -i /root/xxx.pem root@10.10.xx.xxx`.


