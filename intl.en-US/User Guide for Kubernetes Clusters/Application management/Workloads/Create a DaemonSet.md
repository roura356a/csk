---
keyword: [DaemonSet, DaemonSet, Kubernetes]
---

# Create a DaemonSet

A DaemonSet ensures that each node runs a copy of a pod. You can use a DaemonSet to run a log collection daemon, a monitoring daemon, or a system management application on each node. This topic describes how to create a DaemonSet for a Container Service for Kubernetes \(ACK\) cluster.

## Create a DaemonSet in the ACK console

**Create a DaemonSet from an image**

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane, click **Clusters**.

3.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  In the left-side navigation pane of the details page, choose **Workloads** \> **DaemonSets**.

5.  In the upper-right corner of the **DaemonSets** page, click **Create from Image**.

6.  Set parameters for the DaemonSet.

    1.  On the **Basic Information** wizard page, configure the basic settings. For more information, see [Create a stateless application from an image](/intl.en-US/User Guide for Kubernetes Clusters/Application management/Workloads/Use a Deployment to create a stateless application.md).

    2.  On the **Container** wizard page, configure one or more containers. For more information, see [t17653.md\#section\_ne4\_jlh\_d4r](/intl.en-US/User Guide for Kubernetes Clusters/Application management/Workloads/Use a Deployment to create a stateless application.md).

    3.  On the **Advanced** wizard page, configure the advanced settings.

        A DaemonSet can schedule a pod to a node that is in the Unschedulable state. To run a pod on only a specific node, set node affinity, pod affinity, or toleration rules. For more information, see [t17653.md\#section\_49e\_62x\_44j](/intl.en-US/User Guide for Kubernetes Clusters/Application management/Workloads/Use a Deployment to create a stateless application.md).

7.  Click **Create**.

    After the DaemonSet is created, you can view the DaemonSet on the DaemonSets page.


**Create a DaemonSet from a YAML template**

1.  In the upper-right corner of the **DaemonSets** page, click **Create from YAML**.

2.  On the Create page, configure the DaemonSet in the **Template** section.

3.  Click **Create** below the **Template** section.

    After the DaemonSet is created, you can view the DaemonSet on the DaemonSets page.


## Create a DaemonSet by using kubectl

Before you use kubectl to create a DaemonSet, you must download kubectl and connect to your cluster by using kubectl. For more information, see [Use kubectl to connect to an ACK cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Access clusters/Use kubectl to connect to an ACK cluster.md).

A DaemonSet can schedule a pod to a node that is in the Unschedulable state. To run a pod on only a specific node, set the following parameters.

|Parameter|Description|
|---------|-----------|
|nodeSelector|A pod is scheduled to only the node with the specified labels.|
|nodeAffinity|Node affinity. Pods are scheduled to nodes based on node labels. Node affinity allows you to set other matching rules.|
|podAffinity|Pod affinity. Pods are scheduled to nodes based on pod labels. A pod is scheduled to only the node that runs a pod that matches the affinity rules.|

To demonstrate how to create a DaemonSet by using kubectl, a DaemonSet named fluentd-elasticsearch is created in this example.

1.  Create a daemonset.yaml file and copy the following content into the file:

    ```
    apiVersion: apps/v1
    kind: DaemonSet
    metadata:
      name: fluentd-elasticsearch
      namespace: kube-system
      labels:
        k8s-app: fluentd-logging
    spec:
      selector:
        matchLabels:
          name: fluentd-elasticsearch
      template:
        metadata:
          labels:
            name: fluentd-elasticsearch
        spec:
          tolerations:
          # this toleration is to have the daemonset runnable on master nodes
          # remove it if your masters can't run pods
          - key: node-role.kubernetes.io/master
            effect: NoSchedule
          containers:
          - name: fluentd-elasticsearch
            image: quay.io/fluentd_elasticsearch/fluentd:v2.5.2
            resources:
              limits:
                memory: 200Mi
              requests:
                cpu: 100m
                memory: 200Mi
            volumeMounts:
            - name: varlog
              mountPath: /var/log
            - name: varlibdockercontainers
              mountPath: /var/lib/docker/containers
              readOnly: true
          terminationGracePeriodSeconds: 30
          volumes:
          - name: varlog
            hostPath:
              path: /var/log
          - name: varlibdockercontainers
            hostPath:
              path: /var/lib/docker/containers
    ```

2.  Run the following command to create a DaemonSet:

    ```
    kubectl create -f daemonset.yaml
    ```

    If `daemonset.apps/fluentd-elasticsearch created` is returned, the DaemonSet is created.


## What to do next

After you create a DaemonSet, you can perform the following operations:

-   On the DaemonSets page, find the created DaemonSet and click **Details** in the **Actions** column. On the details page, you can view basic information about the DaemonSet. The information includes pods, access method, events, and logs.
-   On the DaemonSets page, find the created DaemonSet. You can choose **More** \> **View in YAML** in the **Actions** column to view the YAML file of the DaemonSet. You can also choose **More** \> **Delete** to delete the DaemonSet.

