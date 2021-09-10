---
keyword: [load-aware pod scheduling, professional Kubernetes cluster]
---

# Use load-aware pod scheduling

Load-aware pod scheduling is a scheduling policy that can be used by Container Service for Kubernetes \(ACK\) to monitor the loads on node resources based on the scheduling framework. When load-aware pod scheduling is enabled, the system reviews the historical statistics of loads on nodes. Then, the system schedules pods to nodes with lower loads to implement load balancing. This prevents applications or node crashes caused by an overloaded node. This topic describes how to use load-aware pod scheduling.

-   This feature is suitable only for professional Kubernetes clusters. For more information about how to create a professional Kubernetes cluster, see [Create a professional managed Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Professional Kubernetes clusters/Create a professional managed Kubernetes cluster.md).
-   The following table describes the versions that are required for the system components.

    |Component|Version|
    |---------|-------|
    |Kubernetes|≥ V1.18|
    |Helm|≥ V3.0|
    |Docker|v19.03.5|


## Procedure

You can specify whether to enable load-aware pod scheduling by adding an annotation.

```
      annotations:
        alibabacloud.com/loadAwareScheduleEnabled: "true" # Enable load-aware pod scheduling. 
```

## Install ack-slo-manager

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane of the ACK console, choose **Marketplace** \> **App Catalog**.

3.  On the **App Catalog** page, click the **Alibaba Cloud Apps** tab. In the search box, enter **ack-slo-manager** and click the search icon.

    ![5](https://help-static-aliyun-doc.aliyuncs.com/assets/img/en-US/1137521361/p303819.png)

4.  Click **ack-slo-manager**. In the lower-right corner of the **View Details** page, click **Create**.

5.  After you click Create, run the following command to check whether **ack-slo-manager** is deployed in the cluster:

    ```
    helm get manifest ack-slo-manager -n kube-system | kubectl get -n kube-system -f -
    ```

    Expected output:

    ```
    NAME                               DATA   AGE
    configmap/ack-slo-manager-config   1      74s
    
    NAME                                SECRETS   AGE
    serviceaccount/ack-slo-controller   1         74s
    serviceaccount/ack-slo-agent        1         74s
    
    NAME                                                                               CREATED AT
    customresourcedefinition.apiextensions.k8s.io/nodemetrics.nodes.alibabacloud.com   2021-08-06T06:54:11Z
    customresourcedefinition.apiextensions.k8s.io/nodeslos.nodes.alibabacloud.com      2021-08-06T06:54:11Z
    
    NAME                                                               CREATED AT
    clusterrole.rbac.authorization.k8s.io/ack-slo-manager-cluserrole   2021-08-06T06:54:11Z
    
    NAME                                                                                 ROLE                                     AGE
    clusterrolebinding.rbac.authorization.k8s.io/ack-slo-agent-custom-rolebinding        ClusterRole/ack-slo-manager-cluserrole   74s
    clusterrolebinding.rbac.authorization.k8s.io/ack-slo-controller-custom-rolebinding   ClusterRole/ack-slo-manager-cluserrole   74s
    
    NAME                                                                 CREATED AT
    role.rbac.authorization.k8s.io/ack-slo-manager-role:leaderelection   2021-08-06T06:54:11Z
    
    NAME                                                                                      ROLE                                       AGE
    rolebinding.rbac.authorization.k8s.io/ack-slo-manager-custom-rolebinding:leaderelection   Role/ack-slo-manager-role:leaderelection   74s
    
    NAME                           DESIRED   CURRENT   READY   UP-TO-DATE   AVAILABLE   NODE SELECTOR   AGE
    daemonset.apps/ack-slo-agent   6         6         6       6            6           <none>          74s
    
    NAME                                 READY   UP-TO-DATE   AVAILABLE   AGE
    deployment.apps/ack-slo-controller   1/1     1            1           74s
    ```

    If the output is similar to the preceding output, it indicates that **ack-slo-manager** is deployed in the cluster.


## Use load-aware pod scheduling

The cluster used in this example contains three nodes. Each node has 4 CPU cores and 8 GiB of memory.

1.  Use the following YAML file to create a Job for video transcoding:

    ```
    apiVersion: batch/v1
    kind: Job
    metadata:
      generateName: ffmpeg
      namespace: default
    spec:
      backoffLimit: 1
      parallelism: 1
      template:
        metadata:
        spec:
          containers:
          - image: registry.cn-hangzhou.aliyuncs.com/kubernetes-image-hub/ffmpeg:latest
            name: ffmpeg
            resources:
              requests:
                cpu: 1
                memory: 2Gi
              limits:
                cpu: 2
                memory: 4Gi
            command: ["/bin/sh"]
            args: ["-c","while true;do ffmpeg -i /root/mp4/statefulset.mp4 -c:v libx264 -crf 100 -threads 200 destination.flv -y;sleep 1;done"]
          restartPolicy: OnFailure
    ```

2.  Run the following command to query the status and deployment of the pod:

    ```
    # The image size is large and it may require about 2 minutes to start the pod.
    kubectl get pods -o wide
    ```

    Expected output:

    ```
    NAME                READY   STATUS    RESTARTS   AGE    IP             NODE                   NOMINATED NODE   READINESS GATES
    ffmpegjjpv2-xcl6t   1/1     Running   0          2m6s   172.20.177.8   cn-beijing.10.0.0.46   <none>           <none>
    ```

    The preceding output indicates that the pod is running on the node `cn-beijing.10.0.0.46`.

3.  Run the following command to view the loads on all of the cluster nodes:

    ```
    kubectl top node
    ```

    Expected output:

    ```
    NAME                   CPU(cores)   CPU%   MEMORY(bytes)   MEMORY%
    cn-beijing.10.0.0.44   251m         6%     1531Mi          22%
    cn-beijing.10.0.0.45   352m         8%     1881Mi          27%
    cn-beijing.10.0.0.46   1802m        45%    1689Mi          25%
    ```

    The preceding output indicates that the load on the node `cn-beijing.10.0.0.46` is the highest.

4.  Use the following YAML file to create pods for which load-aware pod scheduling is disabled:

    ```
    apiVersion: apps/v1
    kind: Deployment
    metadata:
      name: nginx
      namespace: default
      labels:
        app: nginx
    spec:
      replicas: 6
      selector:
        matchLabels:
          app: nginx
      template:
        metadata:
          name: nginx
          labels:
            app: nginx
        spec:
          containers:
          - name: nginx
            image: nginx
            resources:
              limits:
                cpu: 500m
              requests:
                cpu: 500m
    ```

5.  Run the following command to view details of the pods:

    ```
    kubectl get pods | grep nginx
    ```

    Expected output:

    ```
    nginx-6976ccb95-96xmf   1/1     Running   0          108s    172.20.177.10    cn-beijing.10.0.0.46   <none>           <none>
    nginx-6976ccb95-b6xtp   1/1     Running   0          108s    172.20.177.70    cn-beijing.10.0.0.44   <none>           <none>
    nginx-6976ccb95-hmqcw   1/1     Running   0          108s    172.20.177.69    cn-beijing.10.0.0.44   <none>           <none>
    nginx-6976ccb95-l2gnn   1/1     Running   0          108s    172.20.177.9     cn-beijing.10.0.0.46   <none>           <none>
    nginx-6976ccb95-rzsfc   1/1     Running   0          108s    172.20.176.200   cn-beijing.10.0.0.45   <none>           <none>
    nginx-6976ccb95-shv52   1/1     Running   0          108s    172.20.177.71    cn-beijing.10.0.0.44   <none>           <none>
    ```

    The preceding output indicates that the pods are evenly scheduled to the nodes in the cluster. In this scenario, load-aware pod scheduling is disabled for the pods, and the cluster cannot monitor the loads on the nodes.

6.  Run the following command to delete the pods for which load-aware pod scheduling is disabled:

    ```
    kubectl delete deployment nginx
    ```

7.  Use the following YAML file to create pods that have load-aware pod scheduling enabled:

    ```
    apiVersion: apps/v1
    kind: Deployment
    metadata:
      name: nginx
      namespace: default
      labels:
        app: nginx
    spec:
      replicas: 6
      selector:
        matchLabels:
          app: nginx
      template:
        metadata:
          name: nginx
          labels:
            app: nginx
          annotations:
            alibabacloud.com/loadAwareScheduleEnabled: "true" # Enable load-aware pod scheduling. 
        spec:
          containers:
          - name: nginx
            image: nginx
            resources:
              limits:
                cpu: 500m
              requests:
                cpu: 500m
    ```

8.  Run the following command to view details of the pods:

    ```
    kubectl get pods | grep nginx
    ```

    Expected output:

    ```
    nginx-7759585d4c-hfblh   1/1     Running   0          72s     172.20.177.74    cn-beijing.10.0.0.44   <none>           <none>
    nginx-7759585d4c-hzzt9   1/1     Running   0          72s     172.20.176.203   cn-beijing.10.0.0.45   <none>           <none>
    nginx-7759585d4c-m4gmm   1/1     Running   0          72s     172.20.177.72    cn-beijing.10.0.0.44   <none>           <none>
    nginx-7759585d4c-v7wfb   1/1     Running   0          72s     172.20.176.201   cn-beijing.10.0.0.45   <none>           <none>
    nginx-7759585d4c-wx59r   1/1     Running   0          72s     172.20.177.73    cn-beijing.10.0.0.44   <none>           <none>
    nginx-7759585d4c-xbwh6   1/1     Running   0          72s     172.20.176.202   cn-beijing.10.0.0.45   <none>           <none>
    ```

    The preceding output indicates that after load-aware pod scheduling is enabled for the pods, the cluster can monitor the loads on the nodes and use the scheduling policy to schedule the pods to nodes other than the node `cn-beijing.10.0.0.46`.


