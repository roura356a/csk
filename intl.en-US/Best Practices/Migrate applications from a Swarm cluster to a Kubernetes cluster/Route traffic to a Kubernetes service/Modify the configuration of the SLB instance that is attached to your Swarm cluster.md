# Modify the configuration of the SLB instance that is attached to your Swarm cluster

This topic describes how to modify the configuration of the Server Load Balancer \(SLB\) instance that is attached to your Swarm cluster. You can use this method to redirect network traffic from the Swarm cluster to your cluster of Container Service for Kubernetes \(ACK\).

After an SLB instance receives requests, it distributes the requests to the backend servers. To attach a server to the listener of an SLB instance, you can add the server to one of the following groups: **default server group**, **primary/secondary server group**, and **vServer group**.

Servers in the default server group or in the same primary/secondary server group must use the same port to receive requests. However, servers in a Swarm cluster use port 9080 to receive network traffic from the SLB instance. The NodePort Service in an ACK cluster uses only a port from 30000 to 32767 to receive network traffic from the SLB instance. Therefore, if you want to redirect network traffic from the Swarm cluster to the ACK cluster, you must add the backend servers to a vServer group. To create a vServer group and add backend servers, perform the following steps:

## Check whether the backend servers are already added to a vServer group

Check whether a **vServer group** is created for the SLB instance of the Swarm cluster and the backend servers are added to the vServer group.

1.  Log on to the [SLB console](https://slbnext.console.aliyun.com/). In the left-side navigation pane, choose **Instances** \> **Instances**.

2.  On the Instances page, find the attached SLB instance and check its backend server type.

    ![Instances](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/5855855061/p48304.png)

    -   If the backend server type is default server, go to the next step.
    -   If the backend server type is vServer group, go to the [Modify the listener to redirect network traffic to the vServer group](#section_vzo_c9w_kdk) step.

## Create a vServer group

1.  On the Instances page, click the attached SLB instance, click the **Listener** tab, and then click the **VServer Groups** tab.

2.  Click **Create VServer Group** to go to the Create VServer Group page.

    ![Create VServer Group](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/6855855061/p48305.png)

3.  Click **Add** to open the My Servers dialog box.

4.  Select servers and click **Next** to [Create a vServer group](/intl.en-US/User Guide/Backend servers/VServer groups/Create a vServer group.md).

    **Note:**

    -   When you add servers to the vServer group, select all servers in the Swarm cluster. This ensures that the backend servers can handle all production traffic when you redirect network traffic to the vServer group. You can add one or more servers in the related ACK cluster to the vServer group.
    -   The port and weight settings of the servers from the Swarm cluster must be the same as those of the current **default server group** or **primary/secondary server group**.

## Modify the listener to redirect network traffic to the vServer group

After the vServer group is created, you must modify the listener to redirect network traffic from the **default server group** or **primary/secondary server group** to the vServer group.

1.  On the Instances page, click the attached SLB instance to go to the **Listener** tab. On the **Listener** tab, find the port that you want to change and click **Modify Listener** in the Actions column.

    ![Instances](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/6855855061/p48306.png)

2.  On the Configure Listener page, modify the configurations on the **Protocol and Listener** wizard page and click **Next**.

    ![Modify the listener](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/6855855061/p48307.png)

3.  In the Backend Servers section, click **VServer Group** and select vServer group swarm&K8s. Make sure that the public IP addresses, private IP addresses, and ports of the backend servers from the Swarm cluster are correct. Then, click **Next** to save the changes.

4.  Modify the configurations on the **Health Check** wizard page and click **Next**.

5.  Confirm the changes on the **Confirm** wizard page and click **Submit**.

6.  Go to the **Listener** tab. The server group that uses the related listener has been changed to \[Virtual\]swarm&k8s.


## Check whether requests are handled as expected

Monitor the service performance for a period of time and check whether production traffic handled as expected. You can choose **CloudMonitor** \> **Host Monitoring** or **CloudMonitor** \> **Log Monitoring** to check how inbound traffic is handled. You can also use other monitoring services, such as self-managed monitoring systems, to check how inbound traffic is handled.

## Add servers in the ACK cluster to the vServer group

After you add the backend servers to the vServer group, you must add servers in the ACK cluster to the vServer group. Then, you can redirect a proportion of production traffic to the ACK cluster.

1.  On the Instances page, click the attached SLB instance to go to the **Listener** tab. On the **Listener** tab, find the listener that you want to use and click the related server group in the Server Group column.

2.  On the Edit VServer Group page, click **Add** to add servers in the Kubernetes cluster to the vServer group.

    ![Edit VServer Group](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/6855855061/p48312.png)

    **Note:**

    -   You can add one or more servers of the ACK cluster to the vServer group to meet the requirements of canary releases.
    -   The port settings of the servers from the ACK cluster must be the same as those of the NodePort Service.

        The port range is 30000 to 32767. In this example, port 30080 is used, which points to the gateway-swarm-slb Service in the ACK cluster.

    -   You can also set weights to specify the percentage of network traffic that can be sent to the ACK cluster.

## Check whether network traffic is routed to the ACK cluster

Monitor the service performance for a period of time and check whether production traffic is handled as expected. Then, check whether network traffic is routed to the ACK cluster based on the weight settings of servers in the vServer group.

**Note:** In this example, the SLB instance attached to the Swarm cluster redirects only a proportion of network traffic to the ACK cluster. This way, you can check whether the applications run as expected in the ACK cluster without suspending the Swarm cluster. For more information about how to delete the Swarm cluster, see [t301604.md\#](/intl.en-US/Best Practices/Migrate applications from a Swarm cluster to a Kubernetes cluster/Delete a Swarm cluster.md).

