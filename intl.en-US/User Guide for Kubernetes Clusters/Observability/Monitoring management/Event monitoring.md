---
keyword: [event monitoring, node-problem-detector, kube-eventer, EventBridge]
---

# Event monitoring

Event monitoring is a monitoring method provided by Kubernetes. It provides improvements over resource monitoring in terms of timeliness, accuracy, and scenarios. You can use node-problem-detector with the Kubernetes event center of Log Service to sink cluster events, and configure node-problem-detector to diagnose clusters and send error events to sinks. You can sink cluster events to DingTalk, Log Service, and EventBridge. This allows you to monitor exceptions and issues in clusters in real time.

Kubernetes is designed based on the state machine. Events are generated due to transitions between different states. Typically, Normal events are generated when the state machine changes to expected states and Warning events are generated when the state machine changes to unexpected states.

Container Service for Kubernetes \(ACK\) provides out-of-the-box monitoring solutions for events in different scenarios. The node-problem-detector and kube-eventer components that are maintained by ACK allow you to monitor Kubernetes events.

![Diagram of event monitoring](https://help-static-aliyun-doc.aliyuncs.com/assets/img/en-US/7784133261/p272466.png)

-   node-problem-detector is a tool to diagnose Kubernetes nodes. node-problem-detector detects node exceptions, generates node events, and works with kube-eventer to raise alerts upon these events and enable closed-loop management of alerts. node-problem-detector generates node events when the following exceptions are detected: Docker engine hangs, Linux kernel hangs, outbound traffic exceptions, and file descriptor exceptions. For more information, see [NPD](https://github.com/AliyunContainerService/node-problem-detector).
-   kube-eventer is an open source event emitter that is maintained by ACK. kube-eventer sends Kubernetes events to sinks such as DingTalk, Log Service, and EventBridge. kube-eventer also provides filter conditions to filter different levels of events. You can use kube-eventer to collect events in real time, trigger alerts upon specific events, and asynchronously archive events. For more information, see [kube-eventer](https://github.com/AliyunContainerService/kube-eventer).

This topic describes how to configure event monitoring in the following scenarios:

## Scenario 1: Use node-problem-detector with the Kubernetes event center of Log Service to sink cluster events

node-problem-detector works with third-party plug-ins to detect node exceptions and generate cluster events. A Kubernetes cluster also generates events when the status of the cluster changes. For example, when a pod is evicted or an image pull operation fails, a related event is generated. The Kubernetes event center of Log Service collects, stores, and visualizes cluster events. It allows you to query and analyze these events, and configure alerts. You can sink cluster events to the Kubernetes event center of Log Service by using the following methods.

Method 1: If **Install node-problem-detector and Create Event Center** was selected when you created the cluster, perform the following steps to go to the Kubernetes event center. For more information about how to install node-problem-detector and deploy the Kubernetes event center when you create a cluster, see [Create a managed Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Create Kubernetes clusters/Create a managed Kubernetes cluster.md).

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane of the ACK console, click **Clusters**.

3.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  Choose **Operations** \> **Event Center**.

5.  Click **Cluster Events Management** in the upper-right corner of the page to go to the K8s Event Center page. In the left-side navigation pane of the **K8s Event Center** page, find the cluster that you want to manage and click the ![Show/Hide](https://help-static-aliyun-doc.aliyuncs.com/assets/img/en-US/5301471161/p203800.png) icon to the left of the cluster name. You can view event details that are provided by the Kubernetes event center.

    The Kubernetes event center provides event overview, event details, and information about pod lifecycles. You can also customize queries and configure alerts.

    ![Event overview](https://help-static-aliyun-doc.aliyuncs.com/assets/img/en-US/0639340261/p243385.png)


Method 2: If the Kubernetes event center was not deployed when you created the cluster, perform the following steps to deploy and use the Kubernetes event center:

1.  Install node-problem-detector in the monitored cluster and enable Log Service. For more information about how to install node-problem-detector, see [Scenario 3: Use DingTalk to raise alerts upon Kubernetes events](#section_dfk_z88_5xf).

    **Note:** If node-problem-detector is deployed but Log Service is not enabled, reinstall node-problem-detector.

    1.  In the left-side navigation pane, click **Clusters**.
    2.  On the Clusters page, find the cluster that you want to manage and click the name or click **Details** in the **Actions** column.
    3.  Choose **Applications** \> **Helm**.
    4.  On the Helm page, delete the ack-node-problem-detector release to uninstall node-problem-detector.
    When you configure parameters for node-problem-detector, create a Log Service project for the cluster by setting `eventer.sinks.sls.enabled` to true.

    ![sls](https://help-static-aliyun-doc.aliyuncs.com/assets/img/en-US/3155359951/p135891.png)

    After node-problem-detector is redeployed, a Log Service project is automatically created in the Log Service console for the cluster.

2.  Log on to the [Log Service console](https://sls.console.aliyun.com/lognext/profile) to configure the Kubernetes event center for the cluster.

    1.  In the **Import Data** section, click **Kubernetes - Standard Output**.

    2.  Select the Log Service project that is automatically created in the preceding step from the **Project** drop-down list, and select **k8s-event** from the **Logstore** drop-down list.

    3.  Click **Next** and click **Complete Installation**.

3.  In the **Projects** section of the [Log Service console](https://sls.console.aliyun.com/lognext/profile), find and click the Log Service project.

4.  In the left-side navigation pane, click the **Dashboard** icon and click **Kubernetes Event Center V1.5**.

    ![Kubernetes Event Center](https://help-static-aliyun-doc.aliyuncs.com/assets/img/en-US/7259987261/p135913.png)

    On the dashboard of the Kubernetes event center, you can view all cluster events.


## Scenario 2: Configure node-problem-detector to diagnose a cluster and send events of exceptions to sinks

node-problem-detector is a tool to diagnose Kubernetes nodes. node-problem-detector detects node exceptions, generates node events, and works with kube-eventer to raise alerts upon these events and enable closed-loop management of alerts. node-problem-detector generates node events when the following exceptions are detected: Docker engine hangs, Linux kernel hangs, outbound traffic exceptions, and file descriptor exceptions. Perform the following steps to install and configure node-problem-detector.

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane, choose **Marketplace** \> **App Catalog**. On the App Catalog page, find and click **ack-node-problem-detector**.

    **Note:** If the Kubernetes event center is deployed, you must first uninstall the ack-node-problem-detector component.

    1.  In the left-side navigation pane, click **Clusters**.
    2.  On the Clusters page, find the cluster that you want to manage and click the name or click **Details** in the **Actions** column.
    3.  Choose **Applications** \> **Helm**.
    4.  On the Helm page, delete the ack-node-problem-detector release.
3.  On the App Catalog - ack-node-problem-detector page, select the cluster where want to install ack-node-problem-detector in the Deploy section.

    Namespace is automatically set to kube-system. Release Name is automatically set to ack-node-problem-detector. On the **Parameters** tab, you can view the default configurations of node-problem-detector.

    ![Configuration page](https://help-static-aliyun-doc.aliyuncs.com/assets/img/en-US/0666524161/p51739.png)

    You can set the sink parameters for kube-eventer as described in the following table.

    |Parameter|Description|Default|
    |---------|-----------|-------|
    |`npd.image.repository`|The image address of node-problem-detector|`registry.aliyuncs.com/acs/node-problem-detector`|
    |`npd.image.tag`|The image version of node-problem-detector|`v0.6.3-28-160499f`|
    |`alibaba_cloud_plugins`|Plug-ins that are used to diagnose nodes. For more information, see the [Node diagnosis plug-ins supported by node-problem-detector](#table_7t4_8bu_75w) table.|`fd_check`, `ntp_check`, `network_problem_check`, and `inode_usage_check` are supported.|
    |`plugin_settings.check_fd_warning_percentage`|The alerting threshold for the percentage of opened file descriptors monitored by `fd_check`|80|
    |`plugin_settings.inode_warning_percentage`|The alerting threshold for monitoring the `inode` usage|80|
    |`controller.regionId`|The region where the cluster that has ack-node-problem-detector installed is deployed. Only `cn-hangzhou`, `cn-beijing`, `cn-shenzhen`, `cn-shanghai` are supported.|The region where the cluster that has the plug-in installed is deployed|
    |`controller.clusterType`|The type of the cluster where ack-node-problem-detector is installed|ManagedKubernetes|
    |`controller.clusterId`|The ID of the cluster where ack-node-problem-detector is installed|The ID of the cluster where ack-node-problem-detector is installed|
    |`controller.clusterName`|The name of the cluster where ack-node-problem-detector is installed|The name of the cluster where ack-node-problem-detector is installed|
    |`controller.ramRoleType`|The type of the assigned Resource Access Control \(RAM\) role. A value of `restricted` indicates that token-based authentication is enabled for the RAM role.|The default RAM role type assigned to the cluster|
    |`eventer.image.repository`|The image address of `kube-eventer`|`registry.cn-hangzhou.aliyuncs.com/acs/eventer`|
    |`eventer.image.tag`|The image version of `kube-eventer`|`v1.6.0-4c4c66c-aliyun`|
    |`eventer.image.pullPolicy`|Specifies how the `kube-eventer` image is pulled|IfNotPresent|
    |`eventer.sinks.sls.enabled`|Specifies whether to enable Log Service as a sink of `kube-eventer`|`false`|
    |`eventer.sinks.sls.project`|The name of the Log Service project|None|
    |`eventer.sinks.sls.logstore`|The name of the Logstore in the Log Service project|None|
    |`eventer.sinks.dingtalk.enabled`|Specifies whether to enable DingTalk as a sink of `kube-eventer`|`false`|
    |`eventer.sinks.dingtalk.level`|The level of events at which alerts are raised|`warning`|
    |`eventer.sinks.dingtalk.label`|The labels of the events|None|
    |`eventer.sinks.dingtalk.token`|The token of the DingTalk chatbot|None|
    |`eventer.sinks.dingtalk.monitorkinds`|The type of resource for which event monitoring is enabled|None|
    |`eventer.sinks.dingtalk.monitornamespaces`|The namespace of the resources for which event monitoring is enabled|None|
    |`eventer.sinks.eventbridge.enable`|Specifies whether to enable `eventBridge` as a sink of `kube-eventer`|`false`|

    Node diagnosis plug-ins supported by node-problem-detector are listed in the following table.

    |Plug-in|Feature|Description|
    |-------|-------|-----------|
    |fd\_check|Checks whether the percentage of opened file descriptors on each cluster node exceeds a maximum of 80%|The default threshold is 80%. The threshold is adjustable. This plug-in consumes a considerable amount of resources to perform the check. We recommend that you do not enable this plug-in.|
    |ram\_role\_check|Checks whether cluster nodes are assigned the required RAM role and whether the AccessKey ID and AccessKey secret are configured for the RAM role|None|
    |ntp\_check|Checks whether the system clocks of cluster nodes are properly synchronized through Network Time Protocol \(NTP\)|This plug-in is enabled by default.|
    |nvidia\_gpu\_check|Checks whether the NVIDIA GPUs of cluster nodes can generate `Xid` messages|None|
    |network\_problem\_check|Checks whether the `connection tracking (conntrack)` table usage on each cluster node exceeds 90%|This plug-in is enabled by default.|
    |inodes\_usage\_check|Checks whether the `inode` usage on the system disk of each cluster node exceeds 80%|The default threshold is 80%. The threshold is adjustable. This plug-in is enabled by default.|
    |csi\_hang\_check|Checks whether the Container Storage Interface \(CSI\) plug-in works as expected on cluster nodes|None|
    |ps\_hang\_check|Checks whether processes in the uninterruptible sleep \(D\) state exist in the systems of cluster nodes|None|
    |public\_network\_check|Checks whether cluster nodes can access the Internet|None|
    |irqbalance\_check|Checks whether the `irqbalance` daemon works as expected in the systems of cluster nodes|None|
    |pid\_pressure\_check|Checks whether the ratio of `pid` processes in the node system to the maximum `pid` processes allowed in the kernel exceeds 85%|This plug-in is enabled by default.|
    |docker\_offline\_check|Checks whether the `docker daemon` works as expected on cluster nodes|This plug-in is enabled by default.|

    **Note:** Some plug-ins are enabled by default, as shown in the preceding table. You can find these plug-ins if you select **Install node-problem-detector and Create Event Center** when you enable **Log Service** for the cluster. You can also find these plug-ins when you install the **ack-node-problem-detector** component on the **Add-ons** page. You must manually enable some plug-ins when you deploy the **ack-node-problem-detector** component from the **App Catalog** page.

4.  Click **Create** in the Deploy section.

    Go to the Clusters page. On the Clusters page, find and click the name of the monitored cluster or **Applications** in the **Actions** column. On the page that appears, click the **DaemonSets** tab. On the DaemonSets tab, you can find that **ack-node-problem-detector-daemonset** is running as expected.

    When both node-problem-detector and `kube-eventer` work as expected, the system sinks events and raises alerts based on the `kube-eventer` configurations.


## Scenario 3: Use DingTalk to raise alerts upon Kubernetes events

Using a DingTalk chatbot to monitor Kubernetes events and raise alerts is a typical scenario of ChatOps. Perform the following steps to configure a DingTalk chatbot to raise alerts.

1.  Click ![DingTalk group settings icon](https://help-static-aliyun-doc.aliyuncs.com/assets/img/en-US/1155359951/p13518.png) in the upper-right corner of the chatbox of a DingTalk group to open the Group Settings page.

2.  Click **Group Assistant**, and then click **Add Robot**. In the ChatBot dialog box, click the **+** icon and select the chatbot that you want to use. In this example, **Custom** is selected.

    ![Add a custom chatbot](https://help-static-aliyun-doc.aliyuncs.com/assets/img/en-US/1155359951/p13519.png)

3.  On the Robot details page, click **Add** to open the Add Robot page.

    ![Add a DingTalk chatbot](https://help-static-aliyun-doc.aliyuncs.com/assets/img/en-US/1155359951/p13520.png)

4.  Set the following parameters, read and accept the DingTalk Custom Robot Service Terms of Service, and then click **Finished**.

    |Parameter|Description|
    |---------|-----------|
    |Edit profile picture|The avatar of the chatbot. This parameter is optional.|
    |Chatbot name|The name of the chatbot.|
    |Add to Group|The DingTalk group to which the chatbot is added.|
    |Security settings|Three types of security setting are supported: custom keywords, additional signatures, and IP addresses \(or CIDR blocks\). Only **Custom Keywords** are supported for filtering alerts that are raised upon cluster events.

Select **Custom Keywords** and enter Warning to receive alerts. If the chatbot frequently sends messages, you can add more keywords to filter the messages. You can add up to 10 keywords. Messages from ACK are also filtered through these keywords before the chatbot sends them to the DingTalk group. |

5.  Click **Copy** to copy the **webhook** URL.

    ![Copy the webhook URL](https://help-static-aliyun-doc.aliyuncs.com/assets/img/en-US/1155359951/p13532.png)

    **Note:** On the ChatBot page, find the chatbot and click ![Settings icon](https://help-static-aliyun-doc.aliyuncs.com/assets/img/en-US/1155359951/p13551.png) to perform the following operations:

    -   Modify the avatar and name of the chatbot.
    -   **Enable** or **disable** message push.
    -   Reset the **webhook** URL.
    -   Remove the chatbot.
6.  Log on to the [ACK console](https://cs.console.aliyun.com).

7.  In the left-side navigation pane, choose **Marketplace** \> **App Catalog**. On the **Alibaba Cloud Apps** tab, find and click **ack-node-problem-detector**.

    **Note:** If the Kubernetes event center is deployed, you must first uninstall the ack-node-problem-detector component.

    1.  In the left-side navigation pane, click **Clusters**.
    2.  On the Clusters page, find the cluster that you want to manage and click the name or click **Details** in the **Actions** column.
    3.  Choose **Applications** \> **Helm**.
    4.  On the Helm page, delete the ack-node-problem-detector plug-in.
8.  On the App Catalog - ack-node-problem-detector page, click the **Parameters** tab and modify the following settings:

    -   In the `npd` section, set the `enabled` parameter to false.
    -   In the `eventer.sinks.dingtalk.enabled` section, set the enabled parameter to true.
    -   Enter the token that is contained in the **webhook** URL generated in [Step 5](#step_nk9_ncp_ku7).
    ![Enter the token](https://help-static-aliyun-doc.aliyuncs.com/assets/img/en-US/4621674161/p65888.png)

9.  On the right side of the page, select the cluster for which you want to enable event monitoring. Then, verify that the Namespace parameter is set to kube-system and the Release Name parameter is set to ack-node-problem-detector, and click **Create**.


Expected results:

**kube-eventer** takes effect about 30 seconds after the deployment is completed. When an event with a severity level higher than the threshold occurs, you will receive an alert in the DingTalk group, as shown in the following figure.

![DingTalk messages](https://help-static-aliyun-doc.aliyuncs.com/assets/img/en-US/1155359951/p13547.png)

## Scenario 4: Sink Kubernetes events to Log Service

You can sink Kubernetes events to Log Service for persistent storage, and archive and audit these events. For more information, see [Create and use a Kubernetes event center](/intl.en-US/Application/K8s Event Center/Create and use a Kubernetes event center.md).

1.  Create a Log Service project and a Logstore.

    1.  Log on to the [Log Service console](https://sls.console.aliyun.com/).

    2.  In the **Projects** section, click **Create Project**. In the Create Project panel, set the parameters and click **OK**.

        In this example, a Log Service project named k8s-log4j is created in the China \(Hangzhou\) region where the monitored ACK cluster is deployed.

        **Note:** We recommend that you create a Log Service project in the region where the monitored ACK cluster is deployed. When a Log Service project and an ACK cluster are deployed in the same region, logs are transmitted over the internal network. This enables quick retrieval of log data. This also avoids cross-region transmission, which requires additional bandwidth and time costs.

    3.  In the Projects section, find and click the k8s-log4j project. The details page of the project appears.

    4.  On the Logstores tab, click the **+** icon to open the **Create Logstore** panel.

    5.  In the Create Logstore panel, set the parameters and click **OK**.

        In this example, a Logstore named k8s-logstore is created.

        ![Create a Logstore named k8s-logstore](https://help-static-aliyun-doc.aliyuncs.com/assets/img/en-US/1155359951/p10631.png)

    6.  After the k8s-logstore Logstore is created, instructions on how to use the Data Import wizard appear on the page. Click **Data Import Wizard**. The **Import Data** dialog box appears.

    7.  Select **log4jAppender** and configure the settings by following the steps on the page.

        In this example, Log4jAppender uses the default settings. You can also customize the settings to meet your business requirements.

        ![Custom data](https://help-static-aliyun-doc.aliyuncs.com/assets/img/en-US/8259987261/p10634.png)

2.  Configure Log4jAppender for the monitored ACK cluster.

    1.  Log on to the [ACK console](https://cs.console.aliyun.com).

    2.  In the left-side navigation pane, choose **Marketplace** \> **App Catalog**. On the App Catalog page, find and click **ack-node-problem-detector**.

        **Note:** If the Kubernetes event center is deployed, you must first uninstall the ack-node-problem-detector component.

        1.  In the left-side navigation pane, click **Clusters**.
        2.  On the Clusters page, find the cluster that you want to manage and click the name or click **Details** in the **Actions** column.
        3.  Choose **Applications** \> **Helm**.
        4.  On the Helm page, delete the ack-node-problem-detector plug-in.
    3.  On the App Catalog - ack-node-problem-detector page, click the **Parameters** tab and modify the following settings:

        -   In the `npd` section, set the `enabled` parameter to false.
        -   In the `eventer.sinks.dingtalk.enabled` section, set the enabled parameter to true.
        -   Enter the names of `Project` and `Logstore` that are created in [Step 1](#step_361_sas_kcb) in the related fields.

            If you do not customize `Project` when you create the ACK cluster, the `Project` parameter is set to k8s-log-\{YOUR\_CLUSTER\_ID\} by default.

        ![Event monitoring](https://help-static-aliyun-doc.aliyuncs.com/assets/img/en-US/4621674161/p65888.png)

    4.  Click **Create** to deploy **kube-eventer** in the monitored cluster.

3.  An event is generated after an operation is performed on the cluster, such as a pod deletion or an application creation. You can log on to the Log Service console to view the collected log data. For more information, see [Consume log data](/intl.en-US/Log consumption and shipping/Real-time subscription and consumption/Multi-language applications/Consume log data.md).

    ![View the collected log data](https://help-static-aliyun-doc.aliyuncs.com/assets/img/en-US/4621674161/p51701.png)

4.  Set indexes and archiving. For more information, see [Configure indexes](/intl.en-US/Index and query/Configure indexes.md).

    1.  Log on to the Log Service console. In the **Projects** section, find and click the name of the project.

    2.  Click ![Management icon](https://help-static-aliyun-doc.aliyuncs.com/assets/img/en-US/2155359951/p53157.png)next to the name of the Logstore, and then select **Search & Analysis**.

    3.  In the upper-right corner of the page that appears, click **Enable Index**.

    4.  In the **Search & Analysis** panel, set the parameters.

    5.  Click **OK**.

        The log query and analysis page appears.

        ![Log analysis](https://help-static-aliyun-doc.aliyuncs.com/assets/img/en-US/5621674161/p51715.png)

        **Note:**

        -   The index configuration takes effect within 1 minute.
        -   A newly enabled or modified index applies only to data that is imported after the index is enabled or modified.
    6.  If you wan to implement offline archiving and computing, you can ship data from the Logstore to **Object Storage Service \(OSS\)**. For more information, see [Ship log data to OSS](/intl.en-US/Log consumption and shipping/Data shipping/Ship logs to OSS/Ship log data from Log Service to OSS.md).


## Scenario 5: Sink Kubernetes events to EventBridge

EventBridge is a serverless event service provided by Alibaba Cloud. Alibaba Cloud services, custom applications, and software as a service \(SaaS\) applications can connect to EventBridge in a standardized and centralized manner. In addition, EventBridge can route events among these applications based on standardized CloudEvents 1.0 protocol. ACK events can be sunk to EventBridge, which allows you to build a loosely-coupled and distributed event-driven architecture in EventBridge. For more information about EventBridge, see [What is EventBridge?]().

1.  Activate EventBridge. For more information, see [Activate EventBridge and grant permissions to a RAM user]().

2.  Log on to the [ACK console](https://cs.console.aliyun.com).

3.  In the left-side navigation pane, choose **Marketplace** \> **App Catalog**. On the App Catalog page, find and click **ack-node-problem-detector**.

    **Note:** If the Kubernetes event center is deployed, you must first uninstall the ack-node-problem-detector component.

    1.  In the left-side navigation pane, click **Clusters**.
    2.  On the Clusters page, find the cluster that you want to manage and click the name or click **Details** in the **Actions** column.
    3.  Choose **Applications** \> **Helm**.
    4.  On the Helm page, delete the ack-node-problem-detector plug-in.
4.  On the App Catalog - ack-node-problem-detector page, click the **Parameters** tab and modify the following settings:

    Configure the Kubernetes event center and enable EventBridge as a sink of Kubernetes events.

    -   In the `npd` section, set the `enabled` parameter to true.
    -   In the `eventer.sinks.eventbridge.enable` section, set the parameter to true.

        ![eventbridge_enable](https://help-static-aliyun-doc.aliyuncs.com/assets/img/en-US/4710719161/p243687.png)

5.  After the configuration is completed, click **Create** to deploy the ack-node-problem-detector component.

6.  After EventBridge is enabled as a sink of Kubernetes events, you can view Kubernetes events in the EventBridge console.

    1.  Log on to the [EventBridge console](https://eventbridge.console.aliyun.com/).

    2.  In the left-side navigation pane, click **System Event Bus**.

    3.  In the left-side navigation pane of the System Event Bus page, click **Event Query**.

    4.  Select a query method and a query range, and click **Search**.

    5.  In the list of events, find the event and click **Event Details** in the **Actions** column.

    ![EventBridge_detail](https://help-static-aliyun-doc.aliyuncs.com/assets/img/en-US/4710719161/p243761.png)


