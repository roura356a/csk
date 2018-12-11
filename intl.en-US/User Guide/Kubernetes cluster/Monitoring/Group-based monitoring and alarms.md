# Group-based monitoring and alarms {#task_cvf_44b_wdb .task}

Alibaba Cloud Container Service is interoperable with CloudMonitor to enable group-based monitoring and alarms.

-   [Create a Kubernetes cluster](reseller.en-US/User Guide/Kubernetes cluster/Cluster management/Create a Kubernetes cluster.md#) if you do not have one.
-   The Kubernetes version must be 1.8.4 or later. Otherwise, you must first upgrade the cluster.

In the Operation & Maintenance \(O&M\) of IT infrastructure, monitoring and alarms facilitate daily O&M, system monitoring, troubleshooting, and debugging, and guarantee the reliability and security of O&M.

The traditional container monitoring solution that uses a statically configured monitoring agent or a centralized server for monitoring and alarms may not be suitable for the Kubernetes scenario because it can cause some problems. For example, the information required to identify the monitoring objects is missing because containers are mostly scheduled in the resource pool whereas the monitoring agent is deployed on the host. Also, containers have shorter lives than applications. The monitoring and alarm rules, and such monitoring data as ReplicaSet and Deployment for a single container cannot be used for the corresponding application.

Alibaba Cloud Container Service for Kubernetes is deeply integrated with CloudMonitor to use application groups to unify the monitoring objects and metrics. In addition, CloudMonitor of Alibaba Cloud is equipped with many functions and custom tools, which provide you with the best practice to monitor your Kubernetes resources and manage the alarms.

1.  Log on to the [CloudMonitor console](https://partners-intl.console.aliyun.com/#/cloudmonitor). 
2.  In the left-side navigation pane, click **Application Groups**. The Kubernetes groups with cluster IDs are displayed. 

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/15811/154452158010529_en-US.png)

3.  Click the **Group Name** to go to the group details page. You can view the resources contained in the group. For example, in a Master group of Kubernetes, you can see such resources as Elastic Compute Service \(ECS\) instances and Server Load Balancer \(SLB\) instances. 

    Kubernetes has two types of nodes: Worker nodes and Master nodes. Master nodes generally contain management and control applications and the resources are required to be highly robust. Worker nodes are generally responsible for scheduling pods and the overall requirement on the resources focuses on scheduling capability. When you create a group, Container Service automatically creates two resource groups, a Master group and a Worker group. The Master group includes the Master nodes and the related SLB instances. The Worker group includes all the Worker nodes.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/15811/154452158010530_en-US.png)

4.  You can view the details of other cloud products, such as SLB, in the group. 

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/15811/154452158010531_en-US.png)

5.  In the left-side navigation pane, click **Dashboards** to view the detailed monitoring metrics of each cloud product in the group. 

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/15811/154452158010533_en-US.png)

6.  In the left-side navigation pane, click **Alarm Rule**. A list of existing alarm rules in the group is displayed. By default, the health of the core components of all nodes in the Mater group is checked. 
    1.  Click **Create Alarm Rule** to create an alarm rule for the group according to your business requirements.

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/15811/154452158010534_en-US.png)

    2.  On the displayed Create Alarm Rule page, set the alarm rules.

        -   Select the related resource, such as ECS.
        -   Set the notification method. For example, you can know the Kubernetes cluster status through DingTalk, email, and SMS.
        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/15811/154452158010535_en-US.png)

    3.  Click **Confirm**. The created alarm rule is displayed on the Alarm Rule page.

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/15811/154452158010536_en-US.png)


More features are provided to meet your resource monitoring requirements, such as fault list, event monitoring, availability monitoring, and log monitoring. You can find them in the left-side navigation pane.

