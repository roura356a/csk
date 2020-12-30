---
keyword: [Pod scheduling, Node pools, Labels]
---

# Schedule an application pod to a specified node pool

Labels play an important role in Kubernetes. Services, deployments, and pods are associated by labels. You can set pod scheduling policies related to node labels to schedule pods to nodes that have specified labels. This topic describes how to schedule an application pod to a specified node pool.

## Procedure

1.  Specify a label for a node pool.

    In Alibaba Cloud Container Service for Kubernetes \(ACK\), you can manage a group of cluster nodes in a node pool. For example, you can manage labels and taints of all nodes in a node pool. For more information about how to create a node pool, see [Create a node pool](/intl.en-US/User Guide for Kubernetes Clusters/Node management/Node pool management/Create a node pool.md).

    1.  Log on to the [ACK console](https://cs.console.aliyun.com).

    2.  In the left-side navigation pane, click **Clusters**.

    3.  On the right side of the cluster that you want to manage, click **Node Pools**.

    4.  In the upper-right corner of the **Node Pools** page, click **Create Node Pool**.

    5.  On the Create Node Pool page, click **Show Advanced Options**, and then click the ![Node pools](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/2896339061/p148967.png) icon to add**labels** to nodes.

        In this example, the label added to the node is pod: nginx.

    You can also click **Scale Out** on the right side of a node pool to update or add labels for nodes. If automatic scaling is enabled for a node pool, click **Modify** on the right side of the node pool to update or add labels for nodes.

    ![Podnginx](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/8535580061/p148973.png)

2.  Set a scheduling policy for the application pod.

    In the preceding step, the pod: nginx label is attached to the nodes in the node pool. You can use the nodeSelector or nodeAffinity field to schedule an application pod to a specified node pool. The following content describes how to schedule an application pod:

    -   Set nodeSelector.

        nodeSelector is a field in the spec section. Add the pod: nginx label to nodeSelector. Example:

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
                pod: nginx      # After you add the label of a node pool here, this application pod can run only on nodes in the node pool.
              containers:
              - name: nginx
                image: nginx:1.7.9
                ports:
                - containerPort: 80
        ```

    -   Set nodeAffinity.

        You can also use nodeAffinity to schedule application pods. The following four scheduling policies are available:

        -   ```
- requiredDuringSchedulingIgnoredDuringExecution
```

            If this policy is used, the pod must be deployed on a node that meets the requirements. If no nodes meet the requirements, the system retries until a node that meets the requirements is found. IgnoreDuringExecution indicates that if the tag of the node where the pod is deployed changes and does not meet the requirements, the pod keeps running on the node.

        -   ```
- requiredDuringSchedulingRequiredDuringExecution
```

            If this policy is used, the pod must be deployed on a node that meets the requirements. If no nodes meet the requirements, the system retries until a node that meets the requirements is found. RequiredDuringExecution indicates that if the tag of the node where the pod is deployed changes and does not meet the requirements, the system selects another node that meets the requirements to deploy the pod.

        -   ```
- preferredDuringSchedulingIgnoredDuringExecution
```

            If this policy is used, the pod is preferentially deployed on a node that meets the requirements. If no nodes meet the requirements, the system ignores these requirements.

        -   ```
- preferredDuringSchedulingRequiredDuringExecution
```

            If this policy is used, the pod is preferentially deployed on a node that meets the requirements. If no nodes meet the requirements, the system ignores these requirements. RequiredDuringExecution indicates that if the tag of a node changes and meets the requirements after the pod is deployed on another node, the system deploys the pod on the node that meets the requirements.

        In the following example, the requiredDuringSchedulingIgnoredDuringExecution policy is used to ensure that the application runs on a node in the specified node pool.

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
                        operator: In      # The application runs on a node that has the pod: nginx label.
                        values:
                        - nginx
              containers:
              - name: nginx-with-affinity
                image: nginx:1.7.9
                ports:
                - containerPort: 80
        ```


All application pods in the preceding examples are scheduled to the xxx.xxx.0.74 node, which is the node that has the pod: nignx label.

![label](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/8535580061/p149116.png)

