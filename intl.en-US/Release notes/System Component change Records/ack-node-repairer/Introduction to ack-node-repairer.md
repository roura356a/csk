---
keyword: [ack-node-repairer, health check and auto repairing of nodes, ACK Node Repairer]
---

# Introduction to ack-node-repairer

When a Node Problem Detector \(NPD\) detects node exceptions, node events or node conditions are generated and reported to the cluster of Container Service for Kubernetes \(ACK\). ACK Node Repairer automatically listens to the events or conditions on each node and fixes these issues based on the related configurations. This topic describes how to install and configure ACK Node Repairer.

-   One of the following types of ACK cluster is created:
    -   [Create a managed kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster management/Create Kubernetes clusters/Create a managed kubernetes cluster.md).
    -   [Create a dedicated Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster management/Create Kubernetes clusters/Create a dedicated Kubernetes cluster.md).
-   ACK Node Problem Detector is installed. For more information, see [Scenario 3: Use node-problem-detector with kube-eventer to raise alerts upon node anomalies](/intl.en-US/User Guide for Kubernetes Clusters/Monitoring management/Monitor events.md).
-   [Use kubectl to connect to an ACK cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster management/Access clusters/Use kubectl to connect to an ACK cluster.md).

ACK Node Repairer is programmed with a predefined list of commonly occurred node exceptions and the actions to fix these exceptions. When a node exception occurs, ACK Node Repairer automatically triggers the corresponding action on the node to fix the exception. After the node exception is fixed, NPD automatically changes the state of the node exception. This creates a closed-loop system for detecting and repairing node exceptions. The operations and maintenance \(O&M\) engineers can also define the node exceptions that need to be fixed and the actions to fix the exceptions.

NPD is a tool for diagnosing Kubernetes nodes. NPD detects node exceptions and generates node events when the following exceptions are detected: Docker engine hangs, Linux kernel hangs, outbound traffic anomalies, and file descriptor anomalies.

## Install ack-node-repairer

Before you use ACK Node Repairer, you must first install ack-node-repairer.

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  Find and click ack-node-repairer. On the App Catalog - ack-node-repairer page, click the **Parameters** tab and specify the AccessKey pair of the current account. For more information, see [Obtain an AccessKey pair]().

3.  On the right side of the page, click **Create**.


## Configure ack-node-repairer

To configure auto repairing for each node exception, you must associate the node condition with the action that is performed to fix the node exception. You must also specify the details of the action. The following example shows how to configure ack-node-repairer to automatically fix the issue of Network Time Protocol \(NTP\) service failure.

1.  View the YAML file of default-node-repairer.

    After ACK Node Repairer is installed, a noderepairers.nodes.alibabacloud.com type resource object named default-node-repairer is automatically created in the kube-system namespace. This resource object defines the node exceptions that are monitored by ACK Node Repairer and the related actions to fix the node exceptions. Run the following command to view the content of the YAML file:

    ```
    kubectl -n kube-system get noderepairers.nodes.alibabacloud.com default-node-repairer -o yaml
    ```

2.  Modify the configurations of default-node-repairer.

    In the spec.rules filed, add the detector parameter for detecting the `NTPProblem` condition and add the nodeOperation parameter for fixing NTP issues. The following code block is an example:

    ```
    spec:
      rules:
      # Specify the detector parameter for detecting NTP issues and the nodeOperation parameter for fixing NTP issues.
      - detector:
          conditionType: NTPProblem
          type: conditionType
        healers:
        - nodeOperation: restart-ntpd
          type: nodejob
    ```

3.  Define actions to fix NTP issues. The resource object type of the action is nodeoperations.nodes.alibabacloud.com.

    1.  The following code block is a sample YAML template:

        ```
        apiVersion: nodes.alibabacloud.com/v1beta1
        kind: NodeOperation
        metadata:
          generation: 1
          name: restart-ntpd-20200905220953
          namespace: kube-system
        spec:
          args: ""
          batch:
          - 1
          - 10
          - 50
          - 200
        # systemctl restart chronyd.service
          content: c3lzdGVtY3RsIHJlc3RhcnQgY2hyb255ZC5zZXJ2aWNlCg==
          forward: auto
          internal: 60
          retry: 3
          timeout: 60
          version: "20200905220953"
        ```

    2.  Run the `kubectl apply -f {FILENAME}.yaml` command to deploy the resource object in the cluster.

4.  Define an executor to perform the actions. The type of the executor is nodeoperationexecutors.nodes.alibabacloud.com.

    1.  The following code block is a sample YAML template:

        ```
        apiVersion: nodes.alibabacloud.com/v1beta1
        kind: NodeOperationExecutor
        metadata:
          name: restart-ntpd
          namespace: kube-system
        spec:
          stableVersion: "20200905220953"
          versions:
          - "20200905220953"
        ```

    2.  Run the `kubectl apply -f {FILENAME}.yaml` command to deploy the resource object in the cluster.

    After you perform the preceding steps, when NTP issues occur on a node in the cluster, ACK Node Repairer automatically runs the `systemctl restart chronyd.service` command on the node through OOS to restart NTP on the node.


