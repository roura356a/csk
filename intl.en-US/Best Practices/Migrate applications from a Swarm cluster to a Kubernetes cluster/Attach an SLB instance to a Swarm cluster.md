# Attach an SLB instance to a Swarm cluster

If applications in a cluster of Container Service for Swarm are accessed through <HostIP\>:<port\> or node IP addresses resolved from domain names certified by ICP filing, you cannot migrate the applications to Kubernetes without service disruption. In addition, you cannot roll back the migration in real time when an error occurs. To fix the issue, you must attach a Server Load Balancer \(SLB\) instance to the Swarm cluster so that you can manage access to applications in the Swarm cluster. Then, you can install the Cloud Monitor agent to monitor the Swarm cluster. This way, you can migrate applications without service disruption and roll back the migration when an error occurs.

## Access a Swarm cluster through an SLB instance

1.  Create an SLB instance.

    -   Automatically create an SLB instance

        If you have selected **Automatically Create SLB Instances** when you create the Swarm cluster, a pay-as-you-go SLB instance is automatically created.

        You can log on to the [SLB console](https://slbnext.console.aliyun.com/) to view the created SLB instance. Make sure that the SLB instance has a listener that listens on TCP port 9080 for the attached backend servers.

    -   Manually create an SLB instance

        If you do not select **Automatically Create SLB Instances** when you create the cluster, you must manually create an SLB instance and attach it to the Swarm cluster. Perform the following steps:

        1.  Create an SLB instance. For more information, see [Create an SLB instance](/intl.en-US/User Guide/Instance/Create an SLB instance.md).
        2.  Attach the SLB instance to the Swarm cluster. This ensures that the Swarm cluster automatically updates the SLB configurations. For more information, see [Bind and unbind a Server Load Balancer instance](/intl.en-US/User Guide/Clusters/Bind and unbind a Server Load Balancer instance.md).
2.  Configure simple routing.

    If applications are accessed through <HostIP\>:<port\>, the host IP addresses are configured in the client. If cluster nodes are replaced or new nodes are added to the cluster, you must upgrade the client to access applications deployed on new nodes. Therefore, we recommend that you configure domain names for applications and allow access to applications through domain names. We recommend that you access applications through domain names.

    **Note:** If you do not have custom domain names, Container Service for Kubernetes \(ACK\) allows you to configure a separate domain name for each application or service for free. The domain name provided by ACK is in the format of XXX.$cluster\_id.$region\_id.alicontainer.com. You can customize XXX based on your applications or services.

    This allows users to access applications through custom domain names or testing domain names that are provided by ACK. The <HostIP\>:<port\> of Elastic Compute Service \(ECS\) instances or SLB instances are no longer required to access applications. For more information, see [Simple routing - supports HTTP and HTTPS](/intl.en-US/User Guide/Service discovery and load balancing/Simple routing - supports HTTP and HTTPS.md).

3.  Test access to an application through the SLB instance.

    Use the domain name configured in the previous step to access your application, test whether the application runs as expected, and check whether the SLB instance has received traffic.

    1.  Access your application through the SLB instance.

    2.  Check whether the SLB instance has received traffic.

4.  Change the address that receives inbound traffic.

    -   Change DNS records: If your application was accessed through a domain name, change the IP address to which the domain name points from the node IP address to the IP address of the SLB instance at your DNS service provider.

        **Note:** DNS servers at all levels cache DNS records. After you change DNS records, you must monitor the traffic that flows through the SLB instance to check whether the change takes effect.

    -   Change client configurations: If your application was accessed through a node IP address, upgrade the client to allow access to the application only through domain names.

## Install the Cloud Monitor agent on ECS instances of the Swarm cluster \(optional\)

If you do not select **Install CloudMonitor Agent on ECS Nodes** when you create the Swarm cluster, the Cloud Monitor agent is not installed on the ECS instances of the Swarm cluster. As a result, you cannot view the monitoring information about the ECS instances in the Cloud Monitor console during cluster migration. For example, when you bring the Swarm cluster offline, you cannot use Cloud Monitor to check whether the ECS instances are still receiving traffic. This section describes how to view the monitoring information about the ECS instances and manually install the Cloud Monitor agent if it is not installed.

1.  View the monitoring information of ECS instances in the Swarm cluster.

    1.  Log on to the [Container Service for Swarm console](https://cs.console.aliyun.com). In the left-side navigation pane, click **Nodes**. On the Nodes page, find the node that you want to check and click **Monitor** in the Actions column.

    2.  In the left-side navigation pane of the page that appears, click **Monitoring**. In the **Monitoring Information** section, you can view the detailed monitoring information about the node in different time ranges.

    If the Cloud Monitor agent is not installed, you cannot view the detailed monitoring information about the ECS instances, such as memory usage, disk usage, and TCP connections. If the Cloud Monitor agent is not installed, you can manually install the Cloud Monitor agent as described in the next step.

2.  Manually install the Cloud Monitor agent on ECS instances in the Swarm cluster.

    1.  Log on to the [Cloud Monitor console](https://cms-intl.console.aliyun.com). In the left-side navigation pane, choose **Dashboard** \> **Host Monitoring**. On the page that appears, click the **Instances** tab, select the ECS instances, and click **Batch Install** to install the Cloud Monitor agent on the selected ECS instances.

    2.  After the Cloud Monitor agent is installed, choose **Dashboard** \> **Host Monitoring**. Click the name of an ECS instance. In the Network Metric section, you can view the curve about the inbound and outbound traffic for the ECS instance.


