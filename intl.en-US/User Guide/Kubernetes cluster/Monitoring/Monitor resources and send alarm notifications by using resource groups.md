# Monitor resources and send alarm notifications by using resource groups {#task_cvf_44b_wdb .task}

-   [Create a Kubernetes cluster](intl.en-US/User Guide/Kubernetes cluster/Cluster management/Create a Kubernetes cluster.md#) if you do not have one.
-   The Kubernetes version must be 1.8.4 or later. If the Kubernetes version of your cluster is earlier than 1.8.4, you must upgrade the cluster first and then upgrade the monitoring service to create a resource alarm group quickly.

In the infrastructure Operation & Maintenance \(O&M\) of IT system, monitoring and alarm notifications guarantee the reliability and security, and facilitate the daily O&M, system monitoring, problem troubleshooting, and debugging.

For Kubernetes, the traditional container monitoring solution monitors resources and sends alarm notifications by using the statically configured monitoring agent or centralized server, which has a big problem.  For example, containers are mostly scheduled in the resource pool and information required to identify the monitoring objects is missing if you deploy the monitoring agent on the host. Compared with traditional applications, the lifecycle of containers is shorter, and the abstract upper concepts of containers such as replica sets and deployments of Kubernetes do not have a good way to abstract the collected data reversely. This makes the container monitoring data cannot be effectively aggregated or be used to send alarm notifications. The original monitoring and alarm rules may not take effect if the application is released.

Alibaba Cloud Container Service Kubernetes clusters deeply integrate with cloud monitoring, abstract the logical concepts by using application groups, and unify the logical concepts and physical concepts on monitoring data and lifecycle.  Besides, Alibaba Cloud cloud monitoring service provides many features and custom tools, which allows you to monitor the Kubernetes resources and send alarm notifications.

1.  Log on to the [Container Service console](https://cs.console.aliyun.com). 
2.  Under Kubernetes, click **Clusters** in the left-side navigation pane. 
3.  View the information such as Kubernetes version and cluster ID, and then click **More** \> **Upgrade monitoring service** at the right of the cluster to create the resource alarm group quickly. 

    **Note:** Click **Upgrade Cluster** to upgrade the cluster first if your Kubernetes version is earlier than 1.8.4.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/15811/153958597810528_en-US.png)

4.  Log on to the [CloudMonitor console](https://cloudmonitor.console.aliyun.com/). In the left-hand navigation bar, click **Application Groups**. In Application Groups, you can see the Kubernetes resource groups that contains cluster ID information. 

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/15811/153958597810529_en-US.png)

5.  Click the **Group Name** to go to the detailed group page. You can view the monitoring views in the group. Take the Kubernetes master group as an example. The topology view of the group is displayed by default. 

    Kubernetes clusters have two kinds of nodes: worker nodes and master nodes.  Master nodes generally have applications used to manage and control data deployed and the overall requirement on resources focuses on the strong robustness. Worker nodes are generally used to schedule pods and the overall requirement on resources focuses on scheduling capability. Container Service automatically creates two resource groups, a master group and a worker group, when you create the resource alarm group. The master group includes the master nodes and the related Server Load Balancer instances. The worker group includes all the worker nodes.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/15811/153958597810530_en-US.png)

6.  To view the detailed machine information in the group, click the list view icon. Then, you can view the detailed information of each cloud product in the group. 

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/15811/153958597810531_en-US.png)

7.  Click **Add Monitoring Chart** in the lower-right corner to add more monitoring charts. 

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/15811/153958597810532_en-US.png)

8.  Click **Dashboards** in the left-side navigation pane to view the detailed monitoring metrics of each cloud product in the group. 

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/15811/153958597910533_en-US.png)

9.  Click **Alarm Rule** in the left-side navigation pane. The list of existing alarm rules in the group is displayed. By default, the health check to the core components of all nodes is set in the master group. 
    1.  Click **Create Alarm Rule** to create an alarm rule for this group according to the business requirements.

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/15811/153958597910534_en-US.png)

    2.  The Create Alarm Rule page appears. Complete the configurations to create an alarm rule.

        -   Select the related resource, such as Elastic Compute Service \(ECS\).
        -   Select whether or not to use a template to create an alarm rule. If yes, select an existing alarm template from the Select Template drop-down list. You can also click **Create Alarm Template** to create a new custom alarm template. For more information, see .
        -   Configure the notification method \(such as DingTalk Robot, email, and SMS\) to obtain the Kubernetes cluster status in time.
        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/15811/153958597910535_en-US.png)

    3.  Click **Confirm** to create the alarm rule. The created alarm rule is displayed on the Alarm Rule page.

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/6947/15395859794707_en-US.png)


You can click the buttons in the left-side navigation pane to use other features that meet your resource monitoring requirements, such as fault list, event monitoring, availability monitoring, and log monitoring.

