---
keyword: [ack-autoscaling-placeholder, scale pods in seconds]
---

# Use ack-autoscaling-placeholder to scale pods in seconds

ack-autoscaling-placeholder provides a buffer for automatically scaling pods in a cluster. ack-autoscaling-placeholder is suitable for quickly launching pods for workloads without the need to worry about whether the node resources are sufficient. This topic describes how to use ack-autoscaling-placeholder to scale pods in seconds.

Auto Scaling \(ESS\) is enabled for your cluster of Container Service for Kubernetes \(ACK\). For more information, see [Auto scaling of nodes](/intl.en-US/User Guide for Kubernetes Clusters/Auto Scaling/Auto scaling of nodes.md).

## Procedure

1.  Log on to the [Container Service for Kubernetes \(ACK\) console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane, choose **Marketplace** \> **App Catalog**.

3.  On the App Catalog page, find and click **ack-autoscaling-placeholder**. On the App Catalog - ack-autoscaling-placeholder page, select the cluster and namespace to deploy the application and click **Create**.

4.  Configure ack-autoscaling-placeholder.

    1.  On the Clusters page, click the name of a cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

    2.  In the left-side navigation pane of the details page, click **Releases**.

    3.  On the page that appears, find **ack-autoscaling-placeholder** and click **Update** in the Actions column.

        ```
        nameOverride: ""
        fullnameOverride: ""
        ##
        priorityClassDefault:
          enabled: true
          name: default-priority-class
          value: -1
        
        ##
        deployments:
           - name: ack-place-holder
             replicaCount: 1
             containers:
               - name: placeholder
                 image: registry-vpc.cn-shenzhen.aliyuncs.com/acs/pause:3.1
                 pullPolicy: IfNotPresent
                 resources:
                   requests:
                     cpu: 4                  # Occupy 4 cores and 8 GB of memory.
                     memory: 8               
             imagePullSecrets: {}
             annotations: {}
             nodeSelector:                   # Select nodes.
               demo: "yes"  
             tolerations: []
             affinity: {}
             labels: {}
        ```

    4.  Create a PriorityClass for the workload.

        In this example, a PriorityClass that grants a high priority is created.

        ```
        kubectl apply -f priorityClass.yaml
        ```

        ```
        apiVersion: scheduling.k8s.io/v1
        kind: PriorityClass
        metadata:
          name: high-priority
        value: 1000000              # Specify the priority.
        globalDefault: false
        description: "This priority class should be used for XYZ service pods only."
        ```

    5.  Deploy the workload.

        ```
        kubectl apply -f workload.yaml
        ```

        ```
        apiVersion: apps/v1 
        kind: Deployment
        metadata:
          name: placeholder-test
          labels:
            app: nginx
        spec:
          replicas: 1
          selector:
            matchLabels:
              app: nginx
          template:
            metadata:
              labels:
                app: nginx
            spec:
              nodeSelector:                        # Select nodes.
                demo: "yes"
              priorityClassName: high-priority     # Specify the name of the PriorityClass that is created in Step 3.
              containers:
              - name: nginx
                image: nginx:1.7.9 
                ports:
                - containerPort: 80
                resources:       
                  requests:      
                    cpu: 3                         # Specify the amount of resources required by the workload.
                    memory: 5
        ```

        A PriorityClass that grants a higher priority than other pods is created for the workload, as shown in the following figure. When the node resources are insufficient, the placeholder pod placeHolder is evicted and its state changes to Pending. After the placeholder pod changes to the Pending state, a scale-out activity is triggered in the cluster because Auto Scaling is activated for the cluster. Consequently, **pods are scaled out in seconds** for the workload.

        ![pending](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/0854065061/p170802.png)

        ![run](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/1854065061/p170803.png)


## How it works

A placeholder pod with an extremely low priority \(a negative value\) is created to occupy a certain amount of computing resources for other pods with higher priorities. If the computing resources are insufficient, the placeholder pod is evicted to release the occupied computing resources for the workload. This way, pods can be launched in seconds. cluster-autoscaler is also used to scale nodes in the cluster.

