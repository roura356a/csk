---
keyword: [alert configuration, alert template, alerting, monitoring]
---

# Set up alerting for an external Kubernetes cluster

Container Service for Kubernetes \(ACK\) allows you to configure alerts to centrally manage exceptions in the cluster and provides various metrics for different scenarios. You can deploy Custom Resource Definitions \(CRDs\) in a cluster to configure and manage alert rules. This topic describes how to set up alerting and configure alert rules for a registered external Kubernetes cluster.

-   A cluster registration proxy is created and a self-managed Kubernetes cluster is connected to the cluster registration proxy. For more information, see [Create a cluster registration proxy and register an on-premises cluster](/intl.en-US/User Guide for Kubernetes Clusters/Multi-cloud and hybrid cloud management/Management of external clusters/Create a cluster registration proxy and register an on-premises cluster.md).
-   A kubectl client is connected to the self-managed cluster. For more information, see [Connect to Kubernetes clusters by using kubectl](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Access clusters/Connect to Kubernetes clusters by using kubectl.md).

## Scenarios

ACK allows you to configure and manage alerts in a centralized manner to monitor various scenarios. The alerting feature is commonly used in the following scenarios:

-   **Cluster O&M**

    You can configure alerts to detect exceptions in cluster management, storage, networks, and elastic scaling at the earliest opportunity. For example, you can configure and enable **Alert Rule Set for Node Exceptions** to monitor exceptions in all nodes or specific nodes in the cluster. You can configure and enable **Alert Rule Set for Storage Exceptions** to monitor changes and exceptions in cluster storage. You can configure and enable **Alert Rule Set for Network Exceptions** to monitor changes and exceptions in cluster networks. You can configure and enable **Alert Rule Set for O&M Exceptions** to monitor changes and exceptions in cluster management operations.

-   **Application development**

    You can configure alerts to detect exceptions and abnormal metrics of running applications in the cluster at the earliest opportunity. For example, you can configure alerts to detect exceptions of pod replicas and check whether the CPU and memory usage of a Deployment exceed the thresholds. You can use the default alert template to quickly set up alerts to receive notifications about exceptions of pod replicas in the cluster. For example, you can configure and enable **Alert Rule Set for Pod Exceptions** to monitor exceptions in the pods of your application.

-   **Application management**

    To monitor the issues that occur throughout the lifecycle of an application, we recommend that you pay attention to application health, capacity planning, cluster stability, exceptions, and errors. You can configure and enable **Alert Rule Set for Critical Events** to monitor warnings and errors in the cluster. You can configure and enable **Alert Rule Set for Resource Exceptions** to monitor resource usage in the cluster and optimize capacity planning.

-   **Multi-cluster management**

    When you manage multiple clusters, you may find it a complex task to configure and synchronize alert rules across the clusters. ACK allows you to **deploy CRDs in the cluster to manage alert rules**. You can configure the same CRDs to conveniently synchronize alert rules across multiple clusters.


## Configure the cloud monitoring component in the registered external cluster

**Step 1: Configure RAM permissions for the component**

Before you can install the component in an external cluster, you must set the AccessKey pair to grant the external cluster the permissions to access Alibaba Cloud resources. Before you set the AccessKey pair, create a Resource Access Management \(RAM\) user and grant the RAM user the permissions to access Alibaba Cloud resources.

1.  Create a RAM user. For more information, see [Create a RAM user](/intl.en-US/RAM User Management/Basic operations/Create a RAM user.md).

2.  Create a permission policy. For more information, see [Create a custom policy](/intl.en-US/Policy Management/Custom policies/Create a custom policy.md).

    Use the following template to create a custom permission policy:

    ```
    {
                "Action": [
                    "log:*",
                    "arms:*",
                    "cs:UpdateContactGroup"
                ],
                "Resource": [
                    "*"
                ],
                "Effect": "Allow"
    }
    ```

3.  Grant permissions to the RAM user. For more information, see [Grant permissions to a RAM user](/intl.en-US/RAM User Management/Authorization management/Grant permissions to a RAM user.md).

4.  Create an AccessKey pair for the RAM user. For more information, see [Obtain an AccessKey pair]().

5.  Use the AccessKey pair to create a Secret named alibaba-addon-secret in the self-managed cluster.

    The system automatically uses the AccessKey pair to access cloud resources when you install the cloud monitoring component.

    ```
    kubectl -n kube-system create secret generic alibaba-addon-secret --from-literal='access-key-id=<your access key id>' --from-literal='access-key-secret=<your access key secret>'
    ```

    **Note:** Replace `<your access key id>` and `<your access key secret>` with the AccessKey pair that you obtained.


**Step 2: Install and upgrade the component**

The console automatically checks whether the alerting configuration meets the requirements and guides you to activate, install, or upgrade the component.

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane of the ACK console, click **Clusters**.

3.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  In the left-side navigation pane, choose **Operations** \> **Alerts**.

5.  On the Alerts page, the console automatically checks whether the following conditions are met.

    If not all conditions are met, follow the on-screen instructions to install or upgrade the required components.

    -   Log Service is activated. If you have not activated Log Service, log on to the [Log Service console](https://sls.console.aliyun.com) and follow the on-screen instructions to activate the service.
    -   **Event Center** is installed. For more information, see [Event monitoring](/intl.en-US/User Guide for Kubernetes Clusters/Observability/Monitoring management/Event monitoring.md).
    -   The alicloud-monitor-controller component is upgraded to the latest version. For more information, see [alicloud-monitor-controller](/intl.en-US/Release notes/System Component change Records/Logs and monitoring/alicloud-monitor-controller.md).
    ![Alerting](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/5627952261/p255151.png)


## Set up alerting

**Step 1: Enable the default alert rules**

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane of the ACK console, click **Clusters**.

3.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  In the left-side navigation pane, choose **Operations** \> **Alerts**.

5.  On the Alert Rules tab, enable the alert rule set.

    ![Alert rules](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/6627952261/p249722.png)


**Step 2: Configure alert rules**

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane of the ACK console, click **Clusters**.

3.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  In the left-side navigation pane, choose **Operations** \> **Alerts**.

    |Feature|Description|
    |-------|-----------|
    |**Alert Rules**|    -   By default, ACK provides an alert template that is used to generate alerts for exceptions and metrics.
    -   Alert rules are classified into several alert rule sets. You can configure multiple alert contact groups for each alert rule set and enable or disable alert rule sets.
    -   An alert rule set consists of multiple alert rules, and each alert rule corresponds to an alert item. You can create a YAML file to configure multiple alert rule sets on a cluster. You can also update the YAML file to generate alert rules.
    -   For more information about how to configure alert rules by using a YAML file, see [Configure alert rules by using CRDs](#section_9ua_z95_ugc).
    -   For more information about the default alert template, see [The default alert template](#section_mrd_x9o_nrr). |
    |**Alert History**|You can view up to 100 historical alerts. You can select an alert and click the link in the **Alert Rule** column to view rule details in the monitoring system. You can click **Details** to go to the resource page where the alert is triggered. The alert may be triggered by an exception or an abnormal metric. ![Alert history](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/4082062261/p255435.png) |
    |**Alert Contacts**|You can create, edit, or delete alert contacts.|
    |**Alert Contact Groups**|You can create, edit, or delete alert contact groups. When no alert contact group exists, the console automatically creates the default alert contact group based on your registration information.|

5.  On the Alert Rules tab, click **Modify Contacts** to configure the alert contact group to which the alerts are sent. You can turn on or turn off **Status** to enable or disable the alert rule set.


## Configure alert rules by using CRDs

When the alerting feature is enabled, the system automatically creates a resource object of the AckAlertRule type in the kube-system namespace. This resource object contains the default alert template. You can use this resource object to configure alert rule sets.

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane of the ACK console, click **Clusters**.

3.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  In the left-side navigation pane, choose **Operations** \> **Alerts**.

5.  On the Alert Rules tab, click **Configure Alert Rule** in the upper-right corner. You can view the configuration of the AckAlertRule resource object and modify the YAML file to update the configuration.

    The following YAML file is provided as an example of the alert rule configuration:

    ```
    apiVersion: alert.alibabacloud.com/v1beta1
    kind: AckAlertRule
    metadata:
      name: default
    spec:
      groups:
        - name: pod-exceptions                     ## The name of the alert rule set. 
          rules:
            - name: pod-oom                        ## The name of the alert rule. 
              type: event                          ## The type of the alert rule. Valid enumeration values: event and metric. 
              expression: sls.app.ack.pod.oom      ## The expression of the alert rule. When the type of the alert rule is set to event, the expression must be set to sls_event_id, which is the event ID in Log Service. 
              enable: enable                       ## The status of the alert rule. Valid enumeration values: enable and disable. 
            - name: pod-failed
              type: event
              expression: sls.app.ack.pod.failed
              enable: enable
    ```


## The default alert template

ACK creates the default alert rules on the following conditions:

-   The default alert rules are enabled.
-   You go to the Alert Rules tab for the first time and default alert rules are not enabled.

The following table describes the default alert rules.

|Alert Rule Set|Alert Rule|ACK\_CR\_Rule\_Name|SLS\_Event\_ID|
|--------------|----------|-------------------|--------------|
|Alert Rule Set for Critical Events|Errors|error-event|sls.app.ack.error|
|Warnings|warn-event|sls.app.ack.warn|
|Alert Rule Set for Node Exceptions|Docker process exceptions on nodes|docker-hang|sls.app.ack.docker.hang|
|Evictions|eviction-event|sls.app.ack.eviction|
|GPU XID errors|gpu-xid-error|sls.app.ack.gpu.xid\_error|
|Node restarts|node-restart|sls.app.ack.node.restart|
|Network Time Protocol \(NTP\) service failures on nodes|node-ntp-down|sls.app.ack.ntp.down|
|Pod Lifecycle Event Generator \(PLEG\) errors on nodes|node-pleg-error|sls.app.ack.node.pleg\_error|
|Process errors on nodes|ps-hang|sls.app.ack.ps.hang|
|Alert Rule Set for Resource Exceptions|Excess file handles on nodes|node-fd-pressure|sls.app.ack.node.fd\_pressure|
|Insufficient node disk space|node-disk-pressure|sls.app.ack.node.disk\_pressure|
|Excessive processes on nodes|node-pid-pressure|sls.app.ack.node.pid\_pressure|
|Insufficient node resources for scheduling|node-res-insufficient|sls.app.ack.resource.insufficient|
|Insufficient node IP addresses|node-ip-pressure|sls.app.ack.ip.not\_enough|
|Alert Rule Set for Pod Exceptions|Pod out-of-memory \(OOM\) errors|pod-oom|sls.app.ack.pod.oom|
|Pod restart failures|pod-failed|sls.app.ack.pod.failed|
|Image pull failures|image-pull-back-off|sls.app.ack.image.pull\_back\_off|
|Alert Rule Set for O&M Exceptions|No available Server Load Balancer \(SLB\) instance|slb-no-ava|sls.app.ack.ccm.no\_ava\_slb|
|SLB instance update failures|slb-sync-err|sls.app.ack.ccm.sync\_slb\_failed|
|SLB instance deletion failures|slb-del-err|sls.app.ack.ccm.del\_slb\_failed|
|Node deletion failures|node-del-err|sls.app.ack.ccm.del\_node\_failed|
|Node addition failures|node-add-err|sls.app.ack.ccm.add\_node\_failed|
|Route creation failures|route-create-err|sls.app.ack.ccm.create\_route\_failed|
|Route update failures|route-sync-err|sls.app.ack.ccm.sync\_route\_failed|
|High-risk configurations detected in inspections|si-c-a-risk|sls.app.ack.si.config\_audit\_high\_risk|
|Command execution failures in managed node pools|nlc-run-cmd-err|sls.app.ack.nlc.run\_command\_fail|
|No command provided in managed node pools|nlc-empty-cmd|sls.app.ack.nlc.empty\_task\_cmd|
|URL mode not implemented in managed node pools|nlc-url-m-unimp|sls.app.ack.nlc.url\_mode\_unimpl|
|Unknown repair operations in managed node pools|nlc-opt-no-found|sls.app.ack.nlc.op\_not\_found|
|Node draining and removal failures in managed node pools|nlc-des-node-err|sls.app.ack.nlc.destroy\_node\_fail|
|Node draining failures in managed node pools|nlc-drain-node-err|sls.app.ack.nlc.drain\_node\_fail|
|Elastic Compute Service \(ECS\) restart timeouts in managed node pools|nlc-restart-ecs-wait|sls.app.ack.nlc.restart\_ecs\_wait\_fail|
|ECS restart failures in managed node pools|nlc-restart-ecs-err|sls.app.ack.nlc.restart\_ecs\_fail|
|ECS reset failures in managed node pools|nlc-reset-ecs-err|sls.app.ack.nlc.reset\_ecs\_fail|
|Auto-repair task failures in managed node pools|nlc-sel-repair-err|sls.app.ack.nlc.repair\_fail|
|Alert Rule Set for Network Exceptions|Invalid Terway resources|terway-invalid-res|sls.app.ack.terway.invalid\_resource|
|IP allocation failures of Terway|terway-alloc-ip-err|sls.app.ack.terway.alloc\_ip\_fail|
|Ingress bandwidth configuration parsing failures|terway-parse-err|sls.app.ack.terway.parse\_fail|
|Network resource allocation failures of Terway|terway-alloc-res-err|sls.app.ack.terway.allocate\_failure|
|Network resource reclaim failures of Terway|terway-dispose-err|sls.app.ack.terway.dispose\_failure|
|Terway virtual mode changes|terway-virt-mod-err|sls.app.ack.terway.virtual\_mode\_change|
|Pod IP checks executed by Terway|terway-ip-check|sls.app.ack.terway.config\_check|
|Ingress configuration reload failures|ingress-reload-err|sls.app.ack.ingress.err\_reload\_nginx|
|Alert Rule Set for Storage Exceptions|Disk size is less than 20 GiB|csi\_invalid\_size|sls.app.ack.csi.invalid\_disk\_size|
|Subscription disks cannot be mounted|csi\_not\_portable|sls.app.ack.csi.disk\_not\_portable|
|Unmount failures occur because the mount target is in use|csi\_device\_busy|sls.app.ack.csi.deivce\_busy|
|No disks are available|csi\_no\_ava\_disk|sls.app.ack.csi.no\_ava\_disk|
|I/O hangs of cloud disks|csi\_disk\_iohang|sls.app.ack.csi.disk\_iohang|
|Slow I/O of the underlying disks of persistent volume claims \(PVCs\)|csi\_latency\_high|sls.app.ack.csi.latency\_too\_high|
|Disk usage exceeds the threshold|disk\_space\_press|sls.app.ack.csi.no\_enough\_disk\_space|

