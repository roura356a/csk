---
keyword: [pod scheduling, node pools, labels]
---

# Schedule an application to a specific node pool

Label is an important concept of Kubernetes. Services, Deployments, and pods are associated with each other by labels. You can configure pod scheduling policies based on node labels. This allows you to schedule pods to nodes that have specific labels. This topic describes how to schedule an application to a specific node pool.

## Procedure

1.  Add a label to the nodes in a node pool.

    ACK allows you to manage a group of cluster nodes by using a node pool. For example, you can manage labels and taints of the nodes in a node pool in a unified manner. For more information about how to create a node pool, see [Create a node pool](/intl.en-US/User Guide for Kubernetes Clusters/Node management/Node pool management/Create a node pool.md).

    1.  Log on to the [ACK console](https://cs.console.aliyun.com).

    2.  In the left-side navigation pane of the ACK console, click **Clusters**.

    3.  On the Clusters page, find the cluster that you want to manage and click **Node Pools** in the **Actions** column.

    4.  In the upper-right corner of the **Node Pools** page, click **Create Node Pool**.

    5.  In the Create Node Pool dialog box, click **Show Advanced Options** and click ![Node pools](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/2896339061/p148967.png) on the right side of **Node Label** to add labels to nodes.

        In this example, the pod: nginx label is added.

    You can also click **Scale Out** in the **Actions** column to add labels to the nodes in the node pool. If auto scaling is enabled for a node pool, click **Modify** in the **Actions** column to add labels to the nodes in the node pool.

    ![Podnginx](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/8535580061/p148973.png)

2.  Configure a scheduling policy for an application.

    After the preceding step is completed, the pod: nginx label is added to the nodes in the node pool. You can set the nodeSelector or nodeAffinity field in pod configurations to ensure that an application is scheduled to nodes with matching labels in a node pool. Perform the following steps:

    -   Set nodeSelector.

        nodeSelector is a field in the spec section of pod configurations. Specify pod: nginx as the value of nodeSelector. Sample template:

        ```
        apiVersion: apps/v1 
        kind: Deployment
        metadata:
          name: nginx-deployment-basic
          labels:
            app: nginx
        spec:
          replicas: 2
          selector:
            matchLabels:
              app: nginx
          template:
            metadata:
              labels:
                app: nginx
            spec:
              nodeSelector:
                pod: nginx      # After you set the value to the pod: nginx label of the nodes in a node pool, this application can run only on nodes with this label in the node pool. 
              containers:
              - name: nginx
                image: nginx:1.7.9
                ports:
                - containerPort: 80
        ```

    -   Set nodeAffinity.

        You can also use nodeAffinity to schedule an application based on your requirements. nodeAffinity supports the following scheduling policies:

        -   ```
- requiredDuringSchedulingIgnoredDuringExecution
```

            If this policy is used, a pod can be scheduled to only a node that meets the match rules. If no node meets the match rules, the system retries until a node that meets the rules is found. IgnoreDuringExecution indicates that if the label of the node where the pod is deployed changes and no longer meets the match rules, the pod continues to run on the node.

        -   ```
- requiredDuringSchedulingRequiredDuringExecution
```

            If this policy is used, the pod can be scheduled to only a node that meets the match rules. If no node meets the rules, the system retries until a node that meets the rules is found. RequiredDuringExecution indicates that if the label of the node where the pod is deployed changes and no longer meets the match rules, the system redeploys the pod to another node that meets the rules.

        -   ```
- preferredDuringSchedulingIgnoredDuringExecution
```

            If this policy is used, the pod is preferably scheduled to a node that meets the match rules. If no node meets the rules, the system ignores the rules.

        -   ```
- preferredDuringSchedulingRequiredDuringExecution
```

            If this policy is used, the pod is preferably scheduled to a node that meets the match rules. If no node meets the rules, the system ignores the rules. RequiredDuringExecution indicates that if the label of a node where the pod is deployed changes and still meets the match rules, the system reschedules the pod to a node that meets the match rules.

        In the following example, the requiredDuringSchedulingIgnoredDuringExecution policy is used to ensure that the application always runs on a node in a specific node pool.

        ```
        apiVersion: apps/v1
        kind: Deployment
        metadata:
          name: nginx-with-affinity
          labels:
            app: nginx-with-affinity
        spec:
          replicas: 2
          selector:
            matchLabels:
              app: nginx-with-affinity
          template:
            metadata:
              labels:
                app: nginx-with-affinity
            spec:
              affinity:
                nodeAffinity:
                  requiredDuringSchedulingIgnoredDuringExecution:
                    nodeSelectorTerms:
                    - matchExpressions:
                      - key: pod
                        operator: In      # This policy ensures that the application can run only on a node that has the pod: nginx label. 
                        values:
                        - nginx
              containers:
              - name: nginx-with-affinity
                image: nginx:1.7.9
                ports:
                - containerPort: 80
        ```


In the preceding example, all application pods are scheduled to the xxx.xxx.0.74 node. This node has the pod: nignx label.

![label](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/8535580061/p149116.png)

