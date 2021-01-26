# Use SSH to connect to an ACK cluster

If you do not enable SSH logon when you create a cluster of Container Service for Kubernetes \(ACK\), you cannot connect to the cluster by using SSH or kubectl. To use SSH to connect to such a cluster after it is created, you can manually attach elastic IP addresses \(EIPs\) to ECS instances in the cluster, configure security group rules, and open SSH port 22.

For more information about how to enable SSH logon when you create a cluster, see [Bind an EIP to the Kubernetes API server when you create an ACK cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster management/Access clusters/Access the Kubernetes API server over the Internet.md).

## Procedure

1.  Log on to the [Container Service for Kubernetes \(ACK\) console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane, click **Clusters**.

3.  On the Clusters page, find the cluster that you want to manage and click **Details** in the **Actions** column.

4.  On the **details** page of the cluster, click the Cluster Resources tab. On the Cluster Resources tab, click the ID on the right side of **APIServer SLB**.

5.  In the left-side navigation pane, choose **Instances** \> **Instances**. On the Instances page, click the **Listener** tab. On the Listener tab, click **Add Listener**.

6.  Add an SSH listener. Add an SSH listener. For more information, see [Add a TCP listener](/intl.en-US/Classic Load Balancer/User Guide/Listeners/Add a TCP listener.md).

    After the listener is created, you can use SSH to connect to your cluster through the public IP address of the SLB instance.

    ![Use SSH to connect to new ACK clusters](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/5435359951/p9054.png)


