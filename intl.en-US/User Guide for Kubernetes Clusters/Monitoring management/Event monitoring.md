# Event monitoring

Event monitoring is another monitoring method provided by Kubernetes. It makes up for the disadvantages of resource monitoring in terms of timeliness, accuracy, and scenarios. Developers can diagnose cluster anomalies based on the events that are collected in real time.

Kubernetes is designed based on the state machine. Events are generated upon transitions between different states. Typically, Normal events are generated when the state machine changes to expected states and Warning events are generated when the state machine changes to unexpected states. kube-eventer is an open source event emitter that is maintained by Alibaba Cloud Container Service for Kubernetes \(ACK\). kube-eventer sends Kubernetes events to sinks such as DingTalk and Log Service. kube-eventer also provides filter conditions to filter different levels of events. You can use kube-eventer to collect events in real time, trigger alerts upon specific events, and archive events asynchronously. For more information, see [kube-eventer](https://github.com/AliyunContainerService/kube-eventer).

This topic describes how to configure event monitoring in the following scenarios:

## Scenario 1: Use DingTalk to raise alerts upon Kubernetes events

Using a DingTalk chatbot to monitor Kubernetes events and raising alerts is a typical scenario of ChatOps. Perform the following steps to configure a DingTalk chatbot to raise alerts.

1.  Click the ![DingTalk group settings](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/1155359951/p13518.png) icon in the upper-right corner of the chatbox of a DingTalk group to open the Group Settings page.

2.  Click **Group Assistant**, and then click **Add Robot**. In the ChatBot dialog box, click the **+** icon, and then select the target chatbot. In this example, **Custom** is selected.

    ![Add a custom chatbot](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/1155359951/p13519.png)

3.  In the Robot details dialog box, click **Add** to open the Add Robot dialog box.

    ![Add a DingTalk chatbot](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/1155359951/p13520.png)

4.  Set the following parameters, read and accept the DingTalk Custom Robot Service Terms of Service, and then click **Finished**.

    |Parameter|Description|
    |---------|-----------|
    |Edit profile picture|The avatar of the chatbot. This parameter is optional.|
    |Chatbot name|The name of the chatbot.|
    |Add to Group|The group to which the chatbot is added.|
    |Security Settings|Three types of security settings are supported: custom keywords, additional signature, and IP addresses. Only **custom keywords** are supported for filtering alerts that are raised upon cluster events.

Select the **Custom Keywords** check box and then enter Warning to receive alerts. If the chatbot frequently sends messages, you can add more custom keywords to filter the messages for the chatbot to send. You can add up to 10 custom keywords. |

5.  In the Add Robot dialog box, click **Copy** to copy the webhook URL.

    ![Copy the webhook URL](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/1155359951/p13532.png)

    **Note:** In the ChatBot dialog box, find the target chatbot and click the ![Settings button](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/1155359951/p13551.png) icon to modify the settings:

    -   Modify the avatar and name of the chatbot.
    -   **Enable** or **disable** message push.
    -   Reset the webhook URL.
    -   Remove the chatbot.
6.  Log on to the [ACK console](https://cs.console.aliyun.com).

7.  In the left-side navigation pane, choose **Marketplace** \> **App Catalog**. On the App Catalog page, find and click **ack-node-problem-detector**.

    ![App Catalog](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/2155359951/p51538.png)

8.  On the App Catalog - ack-node-problem-detector page, click the **Parameters** tab and modify the following settings:

    -   In the npd section, set the enabled parameter to false.
    -   In the dingtalk section, set the enabled parameter to true.
    -   Enter the token obtained from the webhook that is created in [Step 5](#step_nk9_ncp_ku7).
    ![Enter the token](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/2155359951/p65888.png)

9.  On the right side of the page, select the target cluster, verify that the Namespace parameter is set to kube-system and the Release Name parameter is set to ack-node-problem-detector, and then click **Create**.


kube-eventer takes effect about 30 seconds after the deployment is complete. When an event with a severity level higher than the threshold occurs, you will receive an alert from the DingTalk group, as shown in the following figure.

![DingTalk messages](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/1155359951/p13547.png)

## Scenario 2: Sink Kubernetes events to Log Service

You can sink Kubernetes events to Log Service for persistent storage, and archive and audit these events. For more information, see [Create and use a Kubernetes event center](/intl.en-US/Application/K8s Event Center/Create and use a Kubernetes event center.md).

1.  Create a Log Service project and a Logstore.

    1.  Log on to the [Log Service console](https://sls.console.aliyun.com/).

    2.  In the **Projects** section, click **Create Project**. In the Create Project pane, set the parameters and click **OK**.

        In this example, a Log Service project named k8s-log4j is created in the China \(Hangzhou\) region where the target ACK cluster is deployed.

        **Note:** We recommend that you create a Log Service project in the region where the target ACK cluster is deployed. When a Log Service project and an ACK cluster are deployed in the same region, log data is transmitted within the internal network. Log data can be dynamically collected and quickly retrieved. This also avoids cross-region transmission, which requires additional bandwidth and time costs.

    3.  In the Projects section, find and click the k8s-log4j project. The details page of the project appears.

    4.  On the Logstores tab, click the **+** icon to open the **Create Logstore** pane.

    5.  In the Create Logstore pane, set the parameters and click **OK**.

        In this example, the Logstore is named k8s-logstore.

        ![Create a Logstore named k8s-logstore](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/1155359951/p10631.png)

    6.  After the k8s-logstore Logstore is created, a dialog box appears. It provides instructions on how to use the Data Import wizard. Click **Data Import Wizard**. Then, the **Import Data** dialog box appears.

    7.  Select **log4jAppender** and configure the settings by following the steps on the page.

        In this example, log4jAppender is configured with the default settings. You can also customize the settings to meet your business requirements.

        ![Custom data](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/1155359951/p10634.png)

2.  Configure the k8s-log4j project in the ACK cluster.

    1.  Log on to the [ACK console](https://cs.console.aliyun.com).

    2.  In the left-side navigation pane, choose **Marketplace** \> **App Catalog**. On the App Catalog page, find and click **ack-node-problem-detector**.

        ![App Catalog](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/2155359951/p51538.png)

    3.  On the App Catalog - ack-node-problem-detector page, click the **Parameters** tab and modify the following settings:

        -   In the npd section, set the enabled parameter to false.
        -   In the sinks section, set the enabled parameter in the sls block to true.
        -   Specify the names of the Log Service project and Logstore created in [Step 1](#step_361_sas_kcb) in the corresponding fields.
        ![](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/2155359951/p65888.png)

    4.  Click **Create** to deploy kube-eventer to the target cluster.

3.  After an operation, such as pod deletion or application creation, is performed on the cluster, an event is generated. You can log on to the Log Service console to view the collected log. For more information, see [Consume log data](/intl.en-US/Log consumption and shipping/Real-time subscription and consumption/Consume log data.md).

    ![View the collected log](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/2155359951/p51701.png)

4.  Set indexes and offline archiving. For more information, see [Enable and configure the indexing feature for a Logstore](/intl.en-US/Index and query/Enable and configure the index feature for a Logstore.md).

    1.  Log on to the Log Service console. In the **Projects** section, find and click the target project.

    2.  Click the ![Management icon](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/2155359951/p53157.png) icon next to the name of the Logstore, and then select **Search & Analysis**.

    3.  In the upper-right corner of the page that appears, click **Enable Index**.

        ![Enable Index](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/2155359951/p51711.png)

    4.  Configure indexes

    5.  Click **OK**.

        The log query and analysis page appears.

        ![Log analysis](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/2155359951/p51715.png)

        **Note:**

        -   The index configurations take effect within one minute.
        -   An enabled or modified index applies to only data that is imported after the index is enabled or modified.
    6.  If you need to implement offline archiving and computing, you can ship data from the Logstore to **OSS**. For more information, see [Ship logs to OSS](/intl.en-US/Log consumption and shipping/Data shipping/Ship logs to OSS/Ship log data from Log Service to OSS.md).


## Scenario 3: Use node-problem-detector with kube-eventer to raise alerts upon node anomalies

node-problem-detector is a tool for diagnosing Kubernetes nodes. node-problem-detector detects node anomalies, generates node events, and works with kube-eventer to raise alerts upon these events. node-problem-detector generates node events when the following anomalies are detected: Docker engine hangs, Linux kernel hangs, outbound traffic anomalies, and file descriptor anomalies. Perform the following steps to raise alerts upon node events:

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane, choose **Marketplace** \> **App Catalog**. On the App Catalog page, find and click **ack-node-problem-detector**.

    ![App Catalog](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/2155359951/p51538.png)

3.  On the App Catalog - ack-node-problem-detector page, click the **Parameters** tab to view the default configurations of node-problem-detector.

    ![Configuration page](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/3155359951/p51739.png)

    You can set the following sink parameters for kube-eventer.

    |Parameter|Description|Default|
    |---------|-----------|-------|
    |`npd.image.repository`|The address of the node-problem-detector image.|registry.aliyuncs.com/acs/node-problem-detector|
    |`npd.image.tag`|The version of the node-problem-detector image.|v0.6.3-28-160499f|
    |`alibaba_cloud_plugins`|Plug-ins that are used for node diagnostics. For more information, see the [Node diagnostics plug-ins supported by node-problem-detector](#table_74i_e0y_s8b) table.|fd\_check, ntp\_check, network\_problem\_check, and inode\_usage\_check are supported.|
    |`plugin_settings.check_fd_warning_percentage`|The alerting threshold for monitoring the percentage of opened file descriptors.|80|
    |`plugin_settings.inode_warning_percenage`|The alerting threshold for monitoring the inode usage.|80|
    |`eventer.image.repository`|The address of the kube-eventer image.|registry.cn-hangzhou.aliyuncs.com/acs/eventer|
    |`eventer.image.tag`|The version of the kube-eventer image.|v1.6.0-4c4c66c-aliyun|
    |`eventer.image.pullPolicy`|The policy that specifies how the kube-eventer image is pulled.|IfNotPresent|
    |`eventer.sinks.sls.enabled`|Specifies whether to enable Log Service as a sink of kube-eventer.|false|
    |`eventer.sinks.sls.project`|The name of the Log Service project.|N/A|
    |`eventer.sinks.sls.logstore`|The name of the Logstore in the Log Service project.|N/A|
    |`eventer.sinks.dingtalk.enabled`|Specifies whether to enable DingTalk as a sink of kube-eventer.|false|
    |`eventer.sinks.dingtalk.level`|The level of events at which alerts are raised.|warning|
    |`eventer.sinks.dingtalk.label`|The labels of the events.|N/A|
    |`eventer.sinks.dingtalk.token`|The token of the DingTalk chatbot.|N/A|
    |`eventer.sinks.dingtalk.monitorkinds`|The type of resource for which events are monitored.|N/A|
    |`eventer.sinks.dingtalk.monitornamespaces`|The namespace of the resources for which events are monitored.|N/A|

    Node diagnostics plug-ins supported by node-problem-detector

    |Plug-in|Feature|Description|
    |-------|-------|-----------|
    |fd\_check|Checks whether the percentage of opened file descriptors on each cluster node exceeds 80%.|The default threshold is 80%. The threshold can be modified.|
    |ram\_role\_check|Checks whether cluster nodes are assigned the required RAM role and whether the AccessKey ID and AccessKey secret are configured for the RAM role.|N/A|
    |ntp\_check|Checks whether the system clocks of cluster nodes are synchronized properly through Network Time Protocol \(NTP\).|N/A|
    |nvidia\_gpu\_check|Checks whether the NVIDIA GPUs of cluster nodes can generate Xid messages.|N/A|
    |network\_problem\_check|Checks whether the connection tracking \(conntrack\) table usage on each cluster node exceeds 90%.|N/A|
    |inodes\_usage\_check|Checks whether the inode usage on the system disks of cluster nodes exceeds 80%.|The default threshold is 80%. The threshold can be modified.|
    |csi\_hang\_check|Checks whether the Container Storage Interface \(CSI\) plug-in is working properly on cluster nodes.|N/A|
    |ps\_hang\_check|Checks whether processes in uninterruptible sleep \(D\) state exist in the systems of cluster nodes.|N/A|
    |public\_network\_check|Checks whether cluster nodes can access the Internet.|N/A|
    |irqbalance\_check|Checks whether the irqbalance daemon is working properly in the systems of cluster nodes.|N/A|

4.  In the Deploy section, select the target cluster from the Cluster drop-down list. The Namespace parameter is automatically set to kube-system and the Release Name parameter is automatically set to ack-node-problem-detector. Click **Create**.

    In the left-side navigation pane, choose **Applications** \> **DaemonSets**. On the DaemonSets page, select the target cluster and namespace, and verify that the **ack-node-problem-detector** DaemonSet is running properly.

    ![daemonset](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/3155359951/p51745.png)

    When both node-problem-detector and kube-eventer are running properly, events are sunk and alerts are raised based on the kube-eventer configurations.


## Scenario 4: Use node-problem-detector with the Kubernetes event center of Log Service to sink cluster events

node-problem-detector works with the preceding listed plug-ins to detect node anomalies, and generate cluster events. A Kubernetes cluster also generates events when the status of the cluster changes. For example, when pods are evicted or image pulls fail, corresponding events are generated. The Kubernetes event center of Log Service collects, stores, and visualizes cluster events. It allows you to query and analyze these events, and configure alerts. To sink cluster events to the Kubernetes event center in the Log Service console, perform the following steps:

1.  Deploy ack-node-problem-detector in the target cluster and activate Log Service for the target cluster. For more information, see [Scenario 3](#section_rya_7i0_9e3).

    If ack-node-problem-detector is deployed but Log Service is not activated, delete and redeploy ack-node-problem-detector. After ack-node-problem-detector is redeployed, a corresponding Log Service project is automatically created in the Log Service console for the cluster.

    When you redeploy ack-node-problem-detector, modify the enabled parameter in the following code block from false to true.

    ![sls](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/3155359951/p135891.png)

2.  Log on to the [Log Service console](https://sls.console.aliyun.com/lognext/profile) to configure the Kubernetes event center for the cluster.

    1.  In the **Import Data** section, click **Kubernetes - Standard Output**.

    2.  Select the target project from the **Project** drop-down list, and select **k8s-event** from the **Logstore** drop-down list.

    3.  Click **Next**, and then click **Complete Installation**.

3.  In the **Projects** section of the [Log Service console](https://sls.console.aliyun.com/lognext/profile), find and click the target project.

4.  In the left-side navigation pane, click the **Dashbaord** icon, and then click **Kubernetes Event Center V1.1**.

    ![Kubernetes Event Center](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/3155359951/p135913.png)

    On the dashboard of the Kubernetes event center, you can view all events of the cluster.


