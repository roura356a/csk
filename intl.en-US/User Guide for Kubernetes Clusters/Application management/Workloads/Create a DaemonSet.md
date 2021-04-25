---
keyword: [DaemonSet, DaemonSet, K8s]
---

# Create a DaemonSet

A DaemonSet ensures that each node in a Kubernetes cluster runs one copy of a pod. Typically, DaemonSets are created to deploy applications that are used to log, monitor, and manage the system of a Kubernetes cluster. This topic describes how to create a DaemonSet in a Container Service for Kubernetes \(ACK\) cluster.

## Create a DaemonSet in the ACK console

**Create a DaemonSet from an image**

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane, click **Clusters**.

3.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  In the left-side navigation pane of the details page, choose **Workloads** \> **DaemonSets**.

5.  In the upper-right corner of the **Daemon Sets** page, click **Create from Image**.

6.  Set the parameters of the DaemonSet.

    1.  On the **Basic Information** wizard page, specify basic information about the application. For more information about the parameters, see [Step 1: Configure basic settings](/intl.en-US/User Guide for Kubernetes Clusters/Application management/Workloads/Deploy a stateless application from an image.md).

    2.  On the **Container** wizard page, configure the containers. For more information about the parameters, see [Step 2: Configure the containers](/intl.en-US/User Guide for Kubernetes Clusters/Application management/Workloads/Deploy a stateless application from an image.md).

    3.  On the **Advanced** wizard page, configure the advanced settings of the DaemonSet.

        A DaemonSet can be scheduled to a node in the unschedulable state. You can configure node affinity, pod affinity, and toleration settings to schedule DaemonSet pods to specific nodes. For more information about the parameters, see [Step 3: Configure advanced settings](/intl.en-US/User Guide for Kubernetes Clusters/Application management/Workloads/Deploy a stateless application from an image.md).

7.  Click **Create**.

    After the DaemonSet is created, you can find it on the DaemonSets page.


**Create a DaemonSet from a template**

1.  In the upper-right corner of the **DaemonSets** page, click **Create from Template**.

2.  On the Create page, specify the template information in the **Template** section.

3.  Click **Create** below the **Template** section.

    After the DaemonSet is created, you can find it on the DaemonSets page.


## Create a DaemonSet by using kubectl

Before you create a DaemonSet by using kubectl, you must install kubectl and connect to the cluster by using kubectl. For more information, see [Use kubectl to connect to an ACK cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Access clusters/Use kubectl to connect to an ACK cluster.md).

A DaemonSet can be scheduled to a node in the unschedulable state. You can set the following parameters to schedule DaemonSet pods to specific nodes.

|Parameter|Description|
|---------|-----------|
|nodeSelector|This parameter specifies that pods can be scheduled to only nodes with matching labels.|
|nodeAffinity|The node affinity settings. This parameter specifies that pods can be scheduled to only specific nodes with labels that meet the affinity rules. You can use this parameter to configure more node matching policies for pod scheduling.|
|podAffinity|The pod affinity settings. This parameter specifies that pods can be scheduled to only nodes where pods that meet the affinity rules are deployed.|

In this topic, a DaemonSet named fluentd-elasticsearch is used as an example to demonstrate how to create a DaemonSet by using kubectl.

1.  Create a YAML file named daemonset.yaml and copy the following content into the file:

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

2.  Run the following command to create the DaemonSet:

    ```
    kubectl create -f daemonset.yaml
    ```

    If the output is `daemonset.apps/fluentd-elasticsearch created`, it indicates that the DaemonSet is created.


## What to do next

After the DaemonSet is created, you can perform the following operations:

-   Go to the DaemonSets page, find the created DaemonSet and click **Details** in the **Actions** column. On the details page, you can view basic information about the DaemonSet, such as the pods, access methods, events, and logs.
-   Go to the DaemonSets page, find the created DaemonSet and choose **More** \> **View in YAML** in the **Actions** column to view the DaemonSet configurations in the YAML format. You can also choose **More** \> **Delete** in the Actions column to delete the DaemonSet.

