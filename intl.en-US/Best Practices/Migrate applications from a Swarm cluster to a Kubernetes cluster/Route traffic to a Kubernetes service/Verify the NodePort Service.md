# Verify the NodePort Service

In the Kubernetes-piggymetrics-cluster cluster, the NodePort Service is used to handle production traffic forwarded from the Server Load Balancer \(SLB\) instance that is attached to a Container Service for Swarm cluster. This topic describes how to verify the NodePort Service.

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane, click **Clusters**.

3.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  In the left-side navigation pane of the details page, choose **Services and Ingresses** \> **Services**.

5.  Check whether the Service is created.

    In the list of Services, you can verify that the type of the gateway-swarm-slb Service is NodePort.

    ![View the Service](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/2518748161/p241384.png)

6.  Check whether the Service functions as expected.

    You can log on to an Elastic Compute Service \(ECS\) instance in the Container Service for Kubernetes \(ACK\) cluster. Then, send a request to the NodePort Service to check whether the Service is accessible. In this example, the Service port is 30080 and the IP address of the ECS instance is 192.168.XX.X0.

    1.  In the left-side navigation pane of the details page, choose **Nodes** \> **Node**.

    2.  On the Nodes page, select the Kubernetes-piggymetrics-cluster cluster and click the ID of the ECS instance that you want to manage.

        ![Nodes](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/3518748161/p48071.png)

    3.  On the Instance Details page, click **Connect** to log on to the instance.

    4.  After you log on to the ECS instance 192.168.XX.X0, run the ping, telnet, and wget commands to access 192.168.XX.X1:30080 to check whether the NodePort Service functions as expected.

        ![NodePort Service](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/0915514161/p48072.png)

        ACK automatically creates a security group for ECS instances in the same cluster. By default, only IP addresses within the pod CIDR blocks can access the ECS instances. You cannot access the ECS instances from outside the cluster. This limit is not applicable when you access the ECS instances from outside the cluster through an SLB instance. In this example, an ECS instance in the ACK cluster is used to verify the NodePort Service. To view the security groups, perform the following operations:

        1.  Log on to the [ECS console](https://ecs.console.aliyun.com/). In the left-side navigation pane, choose **Network & Security** \> **Security Groups**. On the Security Groups page, find the security group that you want to manage and click **Add Rules** in the Actions column.

            ![Add rules](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/3518748161/p48073.png)

        2.  On the **Security Group Rules** page, click the **Inbound** tab and view the IP addresses and ports included in the security group.

            ![Security group rules](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/3518748161/p48074.png)

    After you verify that the NodePort Service functions as expected, you must modify the listener of the SLB instance in the Container Service for Swarm cluster and attach the NordPort Service to the SLB instance. For more information, see [Modify the configuration of the SLB instance that is attached to your Swarm cluster](/intl.en-US/Best Practices/Migrate applications from a Swarm cluster to a Kubernetes cluster/Route traffic to a Kubernetes service/Modify the configuration of the SLB instance that is attached to your Swarm cluster.md).


