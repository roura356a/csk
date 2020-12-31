---
keyword: [ack-node-repairer, health check and auto repairing of node, ACK Node Repairer]
---

# Introduction to ack-node-repairer

When a Node Problem Detector \(NPD\) detects node exceptions, node events or node conditions are generated and reported to the cluster of Container Service for Kubernetes \(ACK\). ACK Node Repairer automatically listens to the events or conditions on each node and fixes these issues based on the related configurations. This topic describes how to install and configure ACK Node Repairer.

-   One of the following types of ACK cluster is created:
    -   [Create a managed Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster management/Create Kubernetes clusters/Create a managed Kubernetes cluster.md).
    -   [Create a dedicated Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster management/Create Kubernetes clusters/Create a dedicated Kubernetes cluster.md).
-   ACK Node Problem Detector is installed. For more information, see [Scenario 3: Use node-problem-detector with kube-eventer to raise alerts upon node anomalies](/intl.en-US/User Guide for Kubernetes Clusters/Observability/Monitoring management/Event monitoring.md).
-   [Use kubectl to connect to an ACK cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster management/Access clusters/Use kubectl to connect to an ACK cluster.md).

ACK Node Repairer is programmed with a predefined list of commonly occurring node exceptions and the actions to fix these exceptions. When a node exception occurs, ACK Node Repairer automatically triggers the corresponding action on the node to fix the exception. After the node exception is fixed, NPD automatically changes the state of the node exception. This creates a closed-loop system for detecting and repairing node exceptions. The operations and maintenance \(O&M\) engineers can also define the node exceptions that need to be fixed and the actions to fix the exceptions.

NPD is a tool for diagnosing Kubernetes nodes. NPD detects node exceptions and generates node events when the following exceptions are detected: Docker engine hangs, Linux kernel hangs, outbound traffic anomalies, and file descriptor anomalies.

## Install ack-node-repairer

Before you use ACK Node Repairer, you must first install ack-node-repairer.

1.  Log on to the [Container Service for Kubernetes \(ACK\) console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane, choose **Marketplace** \> **App Catalog**.

3.  Find and click ack-node-repairer. On the App Catalog - ack-node-repairer page, click the **Parameters** tab and specify the AccessKey pair of the current account.

    ![AK](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/8642988061/p185927.png)

    In this example, set the AccessKey pair based on the following description:

    -   accessKey: nodeRepairer.accessKey
    -   accessSecret: nodeRepairer.accessSecret
4.  On the right side of the page, click **Create**.


## Configure ack-node-repairer

After ACK Node Repairer is installed, all auto repairing operations that are supported by the current version are automatically performed. You can configure and enable or disable auto repairing for a type of node exception. The following example shows how to configure ack-node-repairer to automatically fix the issue of Network Time Protocol \(NTP\) service failure.

1.  View the YAML file of default-node-repairer.

    After ACK Node Repairer is installed, a noderepairers.nodes.alibabacloud.com type resource object named default-node-repairer is automatically created in the kube-system namespace. This resource object defines the node exceptions that are monitored by ACK Node Repairer and the related actions to fix the node exceptions. Run the following command to view the content of the YAML file:

    ```
    kubectl -n kube-system get noderepairers.nodes.alibabacloud.com default-node-repairer -o yaml
    ```

2.  Modify the configurations of default-node-repairer.

    In the spec.rules field, add the detector parameter to detect the `NTPProblem` condition and add the healers parameter to fix NTP issues. The following code block is an example:

    ```
    spec:
      rules:
      Specify the detector parameter to detect NTP issues and the nodeOperation parameter to fix NTP issues.
      - detector:
          conditionType: NTPProblem
          type: conditionType
          paused: false
        healers:
        - nodeOperation: restart-ntpd
          type: nodejob
    ```

    **Note:** To configure auto repairing for each node exception, you must associate the node condition with the action that is performed to fix the node exception. The rules.detector.conditionType parameter specifies the node condition. If you set rules.detector.paused to true, auto repairing is disabled for this type of node condition.

    After you perform the preceding steps, when NTP issues occur on a node in the cluster, ACK Node Repairer automatically runs the `systemctl restart chronyd.service` command on the node through [OOS]() to restart NTP on the node.


## Records of auto repairing events and results

A noderepairers.nodes.alibabacloud.com type resource object is automatically created in the kube-system namespace to record each auto repairing event. To view the content of this resource object, run the following command. You can also view the auto repairing result by checking the Status field in the output.

```
kubectl -n kube-system get nodejobs.nodes.alibabacloud.com {nodejob_cr_name} -o yaml
```

