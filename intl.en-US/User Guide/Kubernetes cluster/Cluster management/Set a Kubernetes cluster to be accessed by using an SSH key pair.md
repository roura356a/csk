# Set a Kubernetes cluster to be accessed by using an SSH key pair {#task_hq3_4cr_b2b .task}

This topic describes how to set a Kubernetes cluster to be accessed by using an SSH key pair. Alibaba Cloud Container Service for Kubernetes provides this function to secure Kubernetes cluster access.

1.  Log on to the [Container Service console](https://partners-intl.console.aliyun.com/#/cs). 
2.  In the left-side navigation pane under Kubernetes, click **Clusters**. 
3.  In the upper-right corner, click **Create Kubernetes Cluster**. 

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16643/155151269710882_en-US.png)

4.  Select Key Pair in the Login field. Complete the other configurations. For more information, see [EN-US\_TP\_6880.md\#](reseller.en-US/User Guide/Kubernetes cluster/Clusters/Create a cluster.md#). Then, click **Create**. 

    1.  If you have created key pairs in the Elastic Compute Service \(ECS\) console, select a key pair from the Key Pair Name drop-down list.
    2.  If you have no key pair, click **Create a new key pair** to create one in the ECS console. For more information, see [Create an SSH key pair](../../../../../reseller.en-US/Security/Key pairs/Create an SSH key pair.md#).
    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/14698/15515126976174_en-US.png)

5.  After the cluster is created, click **Manage** at the right of the cluster on the Cluster List page. View the **Master node SSH IP address** under Connection Information. 

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/14698/15515126976176_en-US.png) 

6.  Download the .pem private key file. Complete the configurations based on your local operating system environment, such as Windows or Linux. For more information, see [Connect to a Linux instance by using an SSH key pair](../../../../../reseller.en-US/Instances/ECS instance life cycle/Connect to instances/Connect to a Linux instance by using an SSH key pair.md#). Take Linux as an example. 
    1.  Find the path where your downloaded .pem private key file is stored on your local machine. For example, `/root/xxx.pem`. 
    2.  Run the following command to modify the attributes of the private key file: `chmod 400 [path where the .pem private key file is stored on the local machine]`. For example, `chmod 400 /root/xxx.pem`. 
    3.  Run the following command to connect to the cluster: ``ssh -i [path where the .pem private key file is stored on the local machine] root@[master-public-ip]`. Wherein, master-public-ip is the master node SSH IP address. For example, `ssh -i /root/xxx.pem root@10.10.10.100`. 

