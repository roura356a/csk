# Monitor a Kubernetes cluster and send alarm notifications by using DingTalk {#task_qts_14g_hfb .task}

After you deploy a robot in a DingTalk group, the cluster sends a notification of an exception event to the DingTalk group through the robot, implementing real-time monitoring and alarming for cluster exception events.

-   You have created a DingTalk group .
-   You have created a Kubernetes cluster. For more information, see [Create a Kubernetes cluster](reseller.en-US/User Guide/Kubernetes cluster/Cluster management/Create a Kubernetes cluster.md#).

1.  Click the ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/22144/154416716313518_en-US.png) icon in the upper-right corner of the DingTalk group. 
2.   Click **ChatBot**. On the ChatBot page, select a robot. Select a **Custom** robot.![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/22144/154416716313519_en-US.png)

 
3.   On the Robot details page, click **Add**.![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/22144/154416716313520_en-US.png)

 
4.  Configure the following parameters for a robot and then click **Finished**: 

    |Configuration|Description|
    |-------------|-----------|
    |Edit profile picture|\(Optional\) Set a profile picture for the robot.|
    |ChatBot Name|The robot name.|
    |Add to Group|The DingTalk group to which the robot is added to.|
    |Enable the outgoing function|\(Optional\) By perform the @robot operation, you can send messages to a specified external service as well as return response results of the external service to the group.**Note:** We recommend that you do not enable this function.

|
    |POST address|The HTTP service address that receives messages.**Note:** You can configure this parameter after you enable the outgoing function.

|
    |Token|The key used to verify that a request is from DingTalk.**Note:** You can configure this parameter after you enable the outgoing function.

|

5.   Click **Copy** to copy the webhook address.![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/22144/154416716313532_en-US.png)

 

    **Note:** On the ChatBot page, click the ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/22144/154416716313551_en-US.png) icon at the right of a robot and then you can perform following operations:

    -   Modify the profile picture and name of the robot.
    -   **Open** or **Close** notifications.
    -   Reset the webhook address.
    -   Remove the robot.
    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/22144/154416716313550_en-US.png)

6.  Log on to the [Container Service console](https://partners-intl.console.aliyun.com/#/cs). 
7.  Under the Kubernetes menu, click **Application** \> **Deployment** in the left-side navigation pane. 
8.   Select a cluster, select the **kube-system** namespace, and click **Create by Template** in the upper-right corner.![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/22144/154416716413533_en-US.png)

 
9.  Configure a template based on the following parameters, and then click **Deploy**. 

    |Configuration|Description|
    |-------------|-----------|
    |Clusters|Select a cluster.|
    |Namespace|Select a namespace to which resource object belongs. The default namespace is **default**. Select **kube-system**.|
    |Sample template|Alibaba Cloud Container Service provides Kubernetes YAML sample templates of many resource types for you to deploy resource objects quickly. You can write your own template based on the format requirements of Kubernetes YAML orchestration to describe the resource type you want to define. Select **Custom**.|
    |Template|Enter the following custom content:    ```
apiVersion: extensions/v1beta1
kind: Deployment
metadata:
  name: eventer
  namespace: kube-system
spec:
  replicas: 1
  template:
    metadata:
      labels:
        task: monitoring
        k8s-app: eventer
      annotations:
        scheduler.alpha.kubernetes.io/critical-pod: ''
    spec:
      serviceAccount: admin
      containers:
      - name: eventer
        image: registry.cn-hangzhou.aliyuncs.com/acs/eventer:v1.6.0
        imagePullPolicy: IfNotPresent
        command:
        - /eventer
        - --source=kubernetes:https://kubernetes.default
        - --sink=dingtalk:[your_webhook_url]&label=[your_cluster_id]&level       #level cab be configured as normal or warning. The default is warning. When the level is configure as normal, alarm notifications of the normal and warning levels can be received in the DingTalk group. When you do not configure the level or configure the level as warning, alarm notifications of only the warning level can be received in the DingTalk group

    ```

|

    On the Cluster List page, click **Dashboard** at the right of the cluster. On the Dashboard, select **kube-system** from the drop-down list of **Namespace**, and click **Deployments** in the left-side navigation pane. The deployed eventer is displayed.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/22144/154416716413541_en-US.png)


The eventer takes effect 30 seconds after you complete the deployment. When an event exceeds the threshold level, you receive the following alarm notifications in the DingTalk group.

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/22144/154416716413547_en-US.png)

