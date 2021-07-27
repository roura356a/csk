---
keyword: [K8s webhook injector, add security rules, IP address, ack-kubernetes-webhook-injector]
---

# Use ack-kubernetes-webhook-injector to dynamically add the IP address of a pod to the whitelists of Alibaba Cloud services

In some scenarios where fine-grained permission control is required, you may need to dynamically add the IP addresses of pods to specified ApsaraDB RDS whitelists. You may also need to remove these IP addresses from specified ApsaraDB RDS whitelists. You can use ack-kubernetes-webhook-injector to perform these operations. This requires you to add annotations to pod configurations. This topic describes how to install and use ack-kubernetes-webhook-injector.

-   [Create a managed Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Create Kubernetes clusters/Create a managed Kubernetes cluster.md)
-   [t16645.md\#](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Access clusters/Connect to Kubernetes clusters by using kubectl.md)

In cloud computing scenarios, you must configure security policies to allow external access to some cloud resources. For example, you must configure Server Load Balancer \(SLB\)-based access control policies for Elastic Compute Service \(ECS\) instances. You must configure an ApsaraDB RDS whitelist to allow access from specified client IP addresses. When you create a Container Service for Kubernetes \(ACK\) cluster, you can add the CIDR block of the cluster nodes to a specified ApsaraDB RDS whitelist. However, the following limits apply:

-   The whitelist controls access in a coarse-grained manner because the IP addresses of all nodes and pods are added to the whitelist.
-   The whitelist is not automatically deleted after the cluster is deleted. You must manually delete the whitelist.

To fix the preceding issues, ack-kubernetes-webhook-injector is developed by ACK to provide fine-grained security control over cloud resources. You can use ack-kubernetes-webhook-injector to dynamically add the IP address of a pod to an ApsaraDB RDS whitelist or security group. When the pod is deleted, the IP address is automatically removed from the ApsaraDB RDS whitelist or security group.

The following features are provided by ack-kubernetes-webhook-injector:

-   When a pod is created or deleted, the IP address of the pod is automatically added to or removed from a specified SLB access control list \(ACL\).
-   When a pod is created or deleted, the IP address of the pod is automatically added to or removed from a specified ApsaraDB for Redis whitelist.
-   When a pod is created or deleted, the IP address of the pod is automatically added to or removed from a specified ApsaraDB RDS whitelist.

## Install ack-kubernetes-webhook-injector

Before you use ack-kubernetes-webhook-injector, you must install the component. Perform the following steps:

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane of the ACK console, choose **Marketplace** \> **App Catalog**.

3.  Select and click **ack-kubernetes-webhook-injector**.

4.  On the **Parameters** tab, set `use_aksk` to `true`, and specify the AccessKey pair of the current account or . For more information, see [Obtain an AccessKey pair]().

    ![AK](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/6523149061/p179747.png)

5.  On the right side of the page, select a cluster to install the component and click **Create**.


## Examples of using ack-kubernetes-webhook-injector to dynamically add the IP address of a pod to an ApsaraDB RDS whitelist

You only need to add annotations to the Pod Spec parameter of the replication controller for a pod. The annotations must specify the ID of an ApsaraDB RDS instance and the name of the ApsaraDB RDS whitelist. This way, when the pod is created, the IP address of the pod is automatically added to the specified ApsaraDB RDS whitelist or security group. When the pod is deleted, the IP address of the pod is automatically removed from the specified ApsaraDB RDS whitelist or security group.

The following annotations must be included:

-   Annotations related to the ApsaraDB RDS whitelist:
    -   The ID of the ApsaraDB RDS instance: ack.aliyun.com/rds\_id.
    -   The name of the ApsaraDB RDS whitelist: ack.aliyun.com/white\_list\_name.

The following example shows how to dynamically add the IP address of a pod to an ApsaraDB RDS whitelist by using ack-kubernetes-webhook-injector.

1.  Create a Deployment and add an annotation in the pod configurations to specify an ApsaraDB RDS instance ID and add another annotation to specify the name of an ApsaraDB RDS whitelist. The following YAML template is provided as an example:

    ```
    apiVersion: apps/v1
    kind: Deployment
    metadata:
      labels:
        app: inject-test
      name: inject-test
    spec:
      replicas: 1
      selector:
        matchLabels:
          app: inject-test
      template:
        metadata:
          annotations:
            ack.aliyun.com/rds_id: <rm-wz9nanjcud75bxxxx>
            ack.aliyun.com/white_list_name: <rds_group>
          labels:
            app: inject-test
        spec:
          containers:
          - command:
            - sleep
            - "3600"
            image: alpine:latest
            name: inject-test
    ```

2.  Run the following command to query the IP address of the pod:

    ```
    kubectl --kubeconfig .kube/config_sts_test -n inject-test get pod -o wide
    ```

    Expected output:

    ```
    NAME                           READY           STATS        RESTARTS    AGE    IP                NODE
    inject-test-68cc8f9bbf-gj86n    1/1            Running         0        22s   172.25.0.28    cn-hangzhou.xxx
    ```

    The output shows that the IP address of the pod is 172.25.0.28.

3.  Log on to the [ApsaraDB RDS console](https://rdsnext.console.aliyun.com/?spm=5176.2020520152.nav-right.2.469016ddzrU6KW#/detail/rm-bp12685y16w4zjz9d/security/whiteList?region=cn-hangzhou) and check the whitelist of the specified ApsaraDB RDS instance. For more information about how to check an ApsaraDB RDS whitelist, see [Configure an enhanced IP address whitelist](/intl.en-US/RDS PostgreSQL Database/Quick start/Set the whitelist/Configure an IP address whitelist for an ApsaraDB RDS for PostgreSQL instance.md).

4.  Change the number of pod replicas to 0 for the Deployment that is created in Step 1. Then, log on to the ApsaraDB RDS console and check the whitelist of the specified ApsaraDB RDS instance.

    You can find that the pod IP address is removed from the ApsaraDB RDS whitelist.

    **Note:** You can perform similar steps to add the IP address of a pod to a security group by using ack-kubernetes-webhook-injector.


## Uninstall ack-kubernetes-webhook-injector

If you no longer use ack-kubernetes-webhook-injector, you can uninstall ack-kubernetes-webhook-injector by using the release feature that is provided by ACK. For more information, see [Delete a release](/intl.en-US/User Guide for Kubernetes Clusters/Release management/Manage releases by using Helm.md). To delete the related configurations, run the following commands:

```
kubectl -n kube-system delete secret kubernetes-webhook-injector-certs
kubectl delete mutatingwebhookconfigurations.admissionregistration.k8s.io kubernetes-webhook-injector
```

## Other security policies

ack-kubernetes-webhook-injector also supports the following security policies:

-   SLB-based access control: `ack.aliyun.com/access_control_policy_id`.
-   ApsaraDB for Redis whitelists:
    -   ApsaraDB for Redis instance ID: `ack.aliyun.com/redis_id`.
    -   The name of an ApsaraDB for Redis whitelist: `ack.aliyun.com/white_list_name`.

