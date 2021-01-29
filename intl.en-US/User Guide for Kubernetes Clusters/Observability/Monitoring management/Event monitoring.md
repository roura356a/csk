# Event monitoring

Event monitoring is a monitoring method provided by Kubernetes. It provides improvements over the resource monitoring in terms of timeliness, accuracy, and scenarios. Developers can diagnose cluster anomalies based on the events that are collected in real time.

Kubernetes is designed based on state machines. Events are generated due to transitions between different states. Typically, Normal events are generated when state machines change to expected states, and Warning events are generated when state machines change to unexpected states. kube-eventer is an open source event emitter that is maintained by Container Service for Kubernetes \(ACK\). kube-eventer sends Kubernetes events to sinks such as DingTalk and Log Service. kube-eventer also provides filter conditions to filter different levels of events. You can use kube-eventer to collect events in real time, trigger alerts upon specific events, and asynchronously archive events. For more information, see [kube-eventer](https://github.com/AliyunContainerService/kube-eventer).

This topic describes how to configure event monitoring in the following scenarios:

## Scenario 1: Use DingTalk to raise alerts upon Kubernetes events

Using a DingTalk chatbot to monitor Kubernetes events and raise alerts is a typical scenario of ChatOps. Perform the following steps to configure a DingTalk chatbot to raise alerts:

1.  Click the ![DingTalk group settings](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/1155359951/p13518.png) icon in the upper-right corner of the chatbox of a DingTalk group to open the Group Settings page.

2.  Click **Group Assistant**, and then click **Add Robot**. In the ChatBot dialog box, click the **+** icon and select the chatbot that you want to use. In this example, **Custom** is selected.

    ![Add a custom chatbot](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/1155359951/p13519.png)

3.  On the Robot details page, click **Add** to open the Add Robot page.

    ![Add a DingTalk chatbot](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/1155359951/p13520.png)

4.  Set the following parameters, read and accept the DingTalk Custom Robot Service Terms of Service, and then click **Finished**.

    |Parameter|Description|
    |---------|-----------|
    |Edit profile picture|Set the avatar of the chatbot. This parameter is optional.|
    |Chatbot name|Enter the name of the chatbot.|
    |Add to Group|The DingTalk group to which the chatbot is added.|
    |Security Settings|Three types of security settings are supported: custom keywords, additional signatures, and IP addresses \(or CIDR blocks\). Only **custom keywords** are supported for filtering alerts that are raised upon cluster events.

Select **Custom Keywords** and enter Warning to receive alerts. If the chatbot frequently sends messages, you can add more keywords to filter the messages. You can add up to 10 keywords. Messages from ACK are also filtered through these keywords before the chatbot sends them to the DingTalk group. |

5.  On the Add Robot page, click **Copy** to copy the webhook URL.

    ![Copy the webhook URL](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/1155359951/p13532.png)

    **Note:** On the ChatBot page, find the chatbot and click the ![Settings icon](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/1155359951/p13551.png) icon to perform the following operations:

    -   Modify the avatar and name of the chatbot.
    -   **Enable** or **disable** message push.
    -   Reset the webhook URL.
    -   Remove the chatbot.
6.  Log on to the [ACK console](https://cs.console.aliyun.com).

7.  In the left-side navigation pane, choose **Marketplace** \> **App Catalog**. On the **App Catalog** page, find and click **ack-node-problem-detector**.

8.  On the App Catalog - ack-node-problem-detector page, click the **Parameters** tab and modify the following settings:

    -   In the npd section, set the enabled parameter to false.
    -   In the dingtalk section, set the enabled parameter to true.
    -   Enter the token that is contained in the webhook URL generated in [Step 5](#step_nk9_ncp_ku7).
    ![Enter the token](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/2155359951/p65888.png)

9.  On the right side of the page, select the cluster for which you want to enable event monitoring, verify that the Namespace parameter is set to kube-system and the Release Name parameter is set to ack-node-problem-detector, and then click **Create**.


kube-eventer takes effect about 30 seconds after the deployment is complete. When an event with a severity level higher than the threshold occurs, you will receive an alert in the DingTalk group, as shown in the following figure.

![DingTalk messages](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/1155359951/p13547.png)

## Scenario 2: Sink Kubernetes events to Log Service

You can sink Kubernetes events to Log Service for persistent storage, and archive and audit these events. For more information, see [Create and use a Kubernetes event center](/intl.en-US/Application/K8s Event Center/Create and use a Kubernetes event center.md).

1.  Create a Log Service project and a Logstore.

    1.  Log on to the [Log Service console](https://sls.console.aliyun.com/).

    2.  In the **Projects** section, click **Create Project**. In the Create Project panel, set the parameters and click **OK**.

        In this example, a Log Service project named k8s-log4j is created in the China \(Hangzhou\) region, in which the monitored ACK cluster is deployed.

        **Note:** We recommend that you create a Log Service project in the region where the monitored ACK cluster is deployed. When a Log Service project and an ACK cluster are deployed in the same region, logs are transmitted within the internal network. This enables real-time collection and quick retrieval of log data. This also avoids cross-region transmission, which requires additional bandwidth and time costs.

    3.  In the Projects section, find and click the k8s-log4j project. The details page of the project appears.

    4.  On the Logstores tab, click the **+** icon to open the **Create Logstore** panel.

    5.  In the Create Logstore panel, set the parameters and click **OK**.

        In this example, a Logstore named k8s-logstore is created.

        ![Create a Logstore named k8s-logstore](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/1155359951/p10631.png)

    6.  After the k8s-logstore Logstore is created, instructions on how to use the Data Import wizard appear on the page. Click **Data Import Wizard**. The **Import Data** dialog box appears.

    7.  Select **log4jAppender** and configure the settings by following the steps on the page.

        In this example, Log4jAppender is configured with the default settings. You can also customize the settings to meet your business requirements.

        ![Custom data](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/1155359951/p10634.png)

2.  Configure Log4jAppender for the monitored ACK cluster.

    1.  Log on to the [ACK console](https://cs.console.aliyun.com).

    2.  In the left-side navigation pane, choose **Marketplace** \> **App Catalog**. On the App Catalog page, find and click **ack-node-problem-detector**.

    3.  On the App Catalog - ack-node-problem-detector page, click the **Parameters** tab and modify the following settings:

        -   In the npd section, set the enabled parameter to false.
        -   In the sinks section, set the enabled parameter in the sls block to true.
        -   Specify the names of the Log Service project and Logstore that are created in [Step 1](#step_361_sas_kcb) in the related fields.
        ![](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/2155359951/p65888.png)

    4.  Click **Create** to deploy kube-eventer in the monitored cluster.

3.  An event is generated after an operation is performed on the cluster, such as a pod deletion or an application creation operation. You can log on to the Log Service console to view the collected log data. For more information, see [Consume log data](/intl.en-US/Log consumption and shipping/Real-time subscription and consumption/Consume log data.md).

    ![View the collected log data](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/2155359951/p51701.png)

4.  Set indexes and archiving. For more information, see [Enable and configure the indexing feature for a Logstore](/intl.en-US/Index and query/Enable and configure the index feature for a Logstore.md).

    1.  Log on to the Log Service console. In the **Projects** section, find and click the k8s-log4j project.

    2.  Click the ![Management icon](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/2155359951/p53157.png) icon next to the name of the Logstore, and then select **Search & Analysis**.

    3.  In the upper-right corner of the page that appears, click **Enable Index**.

    4.  In the **Search & Analysis** panel, set the parameters.

    5.  Click **OK**.

        The log query and analysis page appears.

        ![Log analysis](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/2155359951/p51715.png)

        **Note:**

        -   The index configuration takes effect within one minute.
        -   A newly enabled or modified index applies to only data that is imported after the index is enabled or modified.
    6.  If you need to implement offline archiving and computing, you can ship data from the Logstore to **Object Storage Service \(OSS\)**. For more information, see [Ship log data to OSS](/intl.en-US/Log consumption and shipping/Data shipping/Ship logs to OSS/Ship log data from Log Service to OSS.md).


## Scenario 3: Use node-problem-detector with kube-eventer to raise alerts upon node anomalies

node-problem-detector is a tool to diagnose Kubernetes nodes. node-problem-detector detects node anomalies, generates node events, and works with kube-eventer to raise alerts upon these events. node-problem-detector generates node events when the following anomalies are detected: Docker engine hangs, Linux kernel hangs, outbound traffic anomalies, and file descriptor anomalies. Perform the following steps to raise alerts upon node events:

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane, choose **Marketplace** \> **App Catalog**. On the App Catalog page, find and click **ack-node-problem-detector**.

3.  On the App Catalog - ack-node-problem-detector page, click the **Parameters** tab to view the default configurations of node-problem-detector.

    ![Configuration page](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/3155359951/p51739.png)

    You can set the sink parameters as described in the following table for kube-eventer.

    |Parameter|Description|Default value|
    |---------|-----------|-------------|
    |`npd.image.repository`|The address of the node-problem-detector image.|registry.aliyuncs.com/acs/node-problem-detector|
    |`npd.image.tag`|The version of the node-problem-detector image.|v0.6.3-28-160499f|
    |`alibaba_cloud_plugins`|Plug-ins that are used for node diagnostics. For more information, see the [Node diagnostics plug-ins supported by node-problem-detector](#table_74i_e0y_s8b) table.|fd\_check, ntp\_check, network\_problem\_check, and inode\_usage\_check are supported.|
    |`plugin_settings.check_fd_warning_percentage`|The alerting threshold for monitoring the percentage of opened file descriptors.|80|
    |`plugin_settings.inode_warning_percenage`|The alerting threshold for monitoring the inode usage.|80|
    |`eventer.image.repository`|The address of the kube-eventer image.|registry.cn-hangzhou.aliyuncs.com/acs/eventer|
    |`eventer.image.tag`|The version of the kube-eventer image.|v1.6.0-4c4c66c-aliyun|
    |`eventer.image.pullPolicy`|The policy that specifies how the kube-eventer image is pulled.|IfNotPresent|
    |`eventer.sinks.sls.enabled`|Specifies whether to enable Log Service as a sink of kube-eventer.|false|
    |`eventer.sinks.sls.project`|The name of the Log Service project.|None|
    |`eventer.sinks.sls.logstore`|The name of the Logstore in the Log Service project.|None|
    |`eventer.sinks.dingtalk.enabled`|Specifies whether to enable DingTalk as a sink of kube-eventer.|false|
    |`eventer.sinks.dingtalk.level`|The level of events at which alerts are raised.|warning|
    |`eventer.sinks.dingtalk.label`|The labels of the events.|None|
    |`eventer.sinks.dingtalk.token`|The token of the DingTalk chatbot.|None|
    |`eventer.sinks.dingtalk.monitorkinds`|The type of the resources for which event monitoring is enabled.|None|
    |`eventer.sinks.dingtalk.monitornamespaces`|The namespace of the resources for which event monitoring is enabled.|None|

    Node diagnostics plug-ins supported by node-problem-detector

    |Plug-in|Function|Description|
    |-------|--------|-----------|
    |fd\_check|Checks whether the percentage of opened file descriptors on each cluster node exceeds 80%.|The default threshold is 80%. The threshold is adjustable.|
    |ram\_role\_check|Checks whether cluster nodes are assigned the required Resource Access Management \(RAM\) role and whether the AccessKey ID and AccessKey secret are configured for the RAM role.|None|
    |ntp\_check|Checks whether the system clocks of cluster nodes are properly synchronized through Network Time Protocol \(NTP\).|None|
    |nvidia\_gpu\_check|Checks whether the NVIDIA GPUs of cluster nodes can generate Xid messages.|None|
    |network\_problem\_check|Checks whether the connection tracking \(conntrack\) table usage on each cluster node exceeds 90%.|None|
    |inodes\_usage\_check|Checks whether the inode usage on the system disk of each cluster node exceeds 80%.|The default threshold is 80%. The threshold is adjustable.|
    |csi\_hang\_check|Checks whether the Container Storage Interface \(CSI\) plug-in is working as normal on cluster nodes.|None|
    |ps\_hang\_check|Checks whether processes in the uninterruptible sleep \(D\) state exist in the systems of cluster nodes.|None|
    |public\_network\_check|Checks whether cluster nodes can access the Internet.|None|
    |irqbalance\_check|Checks whether the irqbalance daemon is working as normal in the systems of cluster nodes.|None|

4.  In the Deploy section, select the monitored cluster from the Cluster drop-down list. The Namespace parameter is automatically set to kube-system, and the Release Name parameter is automatically set to ack-node-problem-detector. Click **Create**.

    Go to the Clusters page. On the Clusters page, find and click the name of the monitored cluster or **Applications** in the **Actions** column. On the page that appears, click the **DaemonSets** tab. On the DaemonSets tab, you can find that **ack-node-problem-detector-daemonset** is running as normal.

    When both node-problem-detector and kube-eventer are running as normal, the system sinks events and raises alerts based on the kube-eventer configuration.


## Scenario 4: Use node-problem-detector with the Kubernetes event center of Log Service to sink cluster events

node-problem-detector works with the preceding plug-ins to detect node anomalies and generate cluster events. A Kubernetes cluster also generates events when the status of the cluster changes. For example, when a pod is evicted or an image pull fails, a related event is generated. The Kubernetes event center of Log Service collects, stores, and visualizes cluster events. It allows you to query and analyze these events, and configure alerts. To sink cluster events to the Kubernetes event center in the Log Service console, adopt the following methods:

Method 1: If **Install node-problem-detector and Create Event Center** was selected when you created the cluster, perform the following steps to go to the Kubernetes event center. For more information about how to install node-problem-detector and deploy the Kubernetes event center when you create a cluster, see [Create a managed Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster management/Create Kubernetes clusters/Create a managed Kubernetes cluster.md).

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane of the ACK console, click **Clusters**.

3.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster, or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  Choose **Operations** \> **Events**.

5.  Click the **Cluster Events Management** tab. In the left-side navigation pane of the **K8s Event Center** page, find the cluster that you want to manage and click the ![Show](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/5301471161/p203800.png) icon to the left of the cluster name to view event details that are provided by the Kubernetes event center.

    The Kubernetes event center provides event overview, event details, and information about pod lifecycles. You can also customize queries and alerts.


Method 2: If the Kubernetes event center was not deployed when you created the cluster, perform the following steps to deploy and use the Kubernetes event center.

1.  Deploy ack-node-problem-detector in the monitored cluster and enable Log Service for the monitored cluster. For more information, see [Scenario 3](#section_rya_7i0_9e3).

    If ack-node-problem-detector is deployed but Log Service is not enabled, delete and redeploy ack-node-problem-detector. After ack-node-problem-detector is redeployed, a related Log Service project is automatically created in the Log Service console for the cluster.

    When you redeploy ack-node-problem-detector, change the value of the enabled parameter from false to true, as shown in the following figure. This enables Log Service and automatically creates a Log Service project.

    ![sls](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/3155359951/p135891.png)

2.  Log on to the [Log Service console](https://sls.console.aliyun.com/lognext/profile) to configure the Kubernetes event center for the cluster.

    1.  In the **Import Data** section, click **Kubernetes - Standard Output**.

    2.  Select the Log Service project that is automatically created in the preceding step from the **Project** drop-down list, and select **k8s-event** from the **Logstore** drop-down list.

    3.  Click **Next** and click **Complete Installation**.

3.  In the **Projects** section of the [Log Service console](https://sls.console.aliyun.com/lognext/profile), find and click the Log Service project.

4.  In the left-side navigation pane, click the **Dashbaord** icon and click **Kubernetes Event Center V1.1**.

    ![Kubernetes Event Center](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/3155359951/p135913.png)

    On the dashboard of the Kubernetes event center, you can view all cluster events.


