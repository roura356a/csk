# Set the SSH key pair logon mode for a Kubernetes cluster {#task_hq3_4cr_b2b .task}

This topic describes how to set the SSH key pair logon mode for a Kubernetes cluster, and how to log on to the cluster by using an SSH key pair. Alibaba Cloud Container Service for Kubernetes provides this mode to secure SSH logon to a Kubernetes cluster.

1.  Log on to the [Container Service console](https://partners-intl.console.aliyun.com/#/cs). 
2.  In the left-side navigation pane under Kubernetes, choose **Clusters** \> **Clusters**. 
3.  In the upper-right corner, click **Create Kubernetes Cluster**. 

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16643/155185558710882_en-US.png)

4.  Set the key pair logon mode for the cluster, and then set other cluster parameters. For more information, see [Create a Kubernetes cluster](reseller.en-US/User Guide/Kubernetes cluster/Cluster management/Create a Kubernetes cluster.md#). Then, click **Create**. 

    -   If you have created key pairs in the Elastic Compute Service \(ECS\) console, select a key pair from the drop-down list.
    -   If you have not created any key pairs, click **Create a new key pair** to create one in the ECS console. For more information, see [Create an SSH key pair](../../../../../reseller.en-US/Security/Key pairs/Create an SSH key pair.md#).
    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16643/155185558710883_en-US.png)

5.  After the cluster is created, it is displayed in the cluster list. Click **Manage** on the right of the target cluster and record the **Master node SSH IP address** in the cluster information area. 

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16643/155185558810889_en-US.png) 

6.  Download the .pem private key file and complete the cluster logon settings according to your operating system \(which can be either Windows or Linux\). This topic only describes one type of the cluster logon settings in a Linux operating system as follows: 

    1.  Find the directory where you have stored the downloaded .pem private key file. For example, `/root/xxx.pem`. 
    2.  Run the following command to modify the private key file attribute: `chmod 400 [the directory where the .pem private key file is stored on your local host]`. In this example, `chmod 400 /root/xxx.pem` is run. 
    3.  Run the following command to connect to the cluster: ``ssh -i [the directory where the .pem private key file is stored on your local host] root@[Internet IP address]`. In the command, the Internet IP address is the Master node SSH IP address. For example, `ssh -i /root/xxx.pem root@10.10.10.100` is run in this example. 
    For more information about the cluster logon settings in a Window or Linux operating system, see [Connect to a Linux instance by using an SSH key pair](../../../../../reseller.en-US/Instances/ECS instance life cycle/Connect to instances/Connect to a Linux instance by using an SSH key pair.md#).


