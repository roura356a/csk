---
keyword: [add labels to GPU-accelerated nodes, flexible scheduling]
---

# Use labels to schedule pods to GPU-accelerated nodes

To use Container Service for Kubernetes \(ACK\) clusters for GPU computing, you must schedule pods to GPU-accelerated nodes. ACK allows you to schedule pods to specific GPU-accelerated nodes by adding labels to the GPU-accelerated nodes.

-   An ACK cluster is created and GPU-accelerated nodes are added to the cluster. For more information, see [GPU scheduling for ACK clusters with GPU-accelerated nodes](/intl.en-US/User Guide for Kubernetes Clusters/GPU/NPU/GPU scheduling/GPU scheduling for ACK clusters with GPU-accelerated nodes.md).
-   You are connected to a master node. You can check information such as node labels on the master node. For more information, see [Use kubectl to connect to an ACK cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Access clusters/Use kubectl to connect to an ACK cluster.md).

When ACK deploys nodes with NVIDIA GPUs, the attributes of these GPUs are discovered and exposed as node labels. These labels have the following benefits:

-   You can use the labels to filter GPU-accelerated nodes.
-   The labels can be used as conditions to schedule pods.

1.  Log on to the [Container Service for Kubernetes \(ACK\) console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane, click **Clusters**.

3.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  In the left-side navigation pane of the details page, choose **Nodes** \> **Nodes**.

5.  On the **Nodes** page, find the GPU-accelerated node that you want to manage, and choose **More** \> **Details** in the **Actions** column.

    Check the labels of the GPU-accelerated node.

    ![Node details](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/7262869161/p21059.png)

    You can also log on to a master node and run the following command to view the labels of GPU-accelerated nodes.

    Run the following command:

    ```
    kubectl get nodes
    ```

    Response:

    ```
    NAME                                STATUS    ROLES     AGE       VERSION
    cn-beijing.i-2ze2dy2h9w97v65uuaft   Ready     master    2d        v1.11.2
    cn-beijing.i-2ze8o1a45qdv5q8a7luz   Ready     <none>    2d        v1.11.2             #Compare these nodes with the nodes that are displayed in the ACK console to identify GPU-accelerated nodes.
    cn-beijing.i-2ze8o1a45qdv5q8a7lv0   Ready     <none>    2d        v1.11.2
    cn-beijing.i-2ze9xylyn11vop7g5bwe   Ready     master    2d        v1.11.2
    cn-beijing.i-2zed5sw8snjniq6mf5e5   Ready     master    2d        v1.11.2
    cn-beijing.i-2zej9s0zijykp9pwf7lu   Ready     <none>    2d        v1.11.2
                        
    ```

    Select a GPU-accelerated node and run the following command to query the labels of the node:

    ```
    kubectl describe node cn-beijing.i-2ze8o1a45qdv5q8a7luz
    ```

    Response:

    ```
    Name:               cn-beijing.i-2ze8o1a45qdv5q8a7luz
    Roles:              <none>
    Labels:             aliyun.accelerator/nvidia_count=1                          #Pay attention to this field.
                        aliyun.accelerator/nvidia_mem=12209MiB
                        aliyun.accelerator/nvidia_name=Tesla-M40
                        beta.kubernetes.io/arch=amd64
                        beta.kubernetes.io/instance-type=ecs.gn4-c4g1.xlarge
                        beta.kubernetes.io/os=linux
                        failure-domain.beta.kubernetes.io/region=cn-beijing
                        failure-domain.beta.kubernetes.io/zone=cn-beijing-a
                        kubernetes.io/hostname=cn-beijing.i-2ze8o1a45qdv5q8a7luz
     ......
    ```

    In this example, the following labels are added to the GPU-accelerated node.

    |key|value|
    |---|-----|
    |`aliyun.accelerator/nvidia_count`|The number of GPU cores.|
    |`aliyun.accelerator/nvidia_mem`|The size of the GPU memory. Unit: MiB.|
    |`aliyun.accelerator/nvidia_name`|The name of the NVIDIA GPU.|

    GPU-accelerated nodes of the same type have the same GPU name. You can use this label to locate GPU-accelerated nodes.

    Run the following command:

    ```
    kubectl get no -l aliyun.accelerator/nvidia_name=Tesla-M40
    ```

    Response:

    ```
    
    NAME                                STATUS    ROLES     AGE       VERSION
    cn-beijing.i-2ze8o1a45qdv5q8a7luz   Ready     <none>    2d        v1.11.2
    cn-beijing.i-2ze8o1a45qdv5q8a7lv0   Ready     <none>    2d        v1.11.2
                        
    ```

6.  In the left-side navigation pane of the details page, choose **Workloads** \> **Deployments**. On the **Deployments** page, click **Create from YAML**.

    1.  Create a Deployment for a TensorFlow job. The Deployment schedules pods to a GPU-accelerated node.

        ![Create a Deployment](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/3935359951/p21074.png)

        The following YAML template is used to create the Deployment:

        ```
        ---
        # Define the tensorflow deployment
        apiVersion: apps/v1
        kind: Deployment
        metadata:
          name: tf-notebook
          labels:
            app: tf-notebook
        spec:
          replicas: 1
          selector: # define how the deployment finds the pods it mangages
            matchLabels:
              app: tf-notebook
          template: # define the pods specifications
            metadata:
              labels:
                app: tf-notebook
            spec:
              nodeSelector:                                                  #Pay attention.
                aliyun.accelerator/nvidia_name: Tesla-M40
              containers:
              - name: tf-notebook
                image: tensorflow/tensorflow:1.4.1-gpu-py3
                resources:
                  limits:
                    nvidia.com/gpu: 1                                        #Pay attention.
                ports:
                - containerPort: 8888
                  hostPort: 8888
                env:
                  - name: PASSWORD
                    value: mypassw0rdv
        ```

    2.  You can also exclude an application from GPU-accelerated nodes. The following example shows how to schedule a pod based on node affinity for an NGINX application. For more information, see the section that describes node affinity in [Use a Deployment to create a stateless application](/intl.en-US/User Guide for Kubernetes Clusters/Application management/Workloads/Use a Deployment to create a stateless application.md).

        The following YAML template is used to schedule the pod:

        ```
        apiVersion: v1
        kind: Pod
        metadata:
          name: not-in-gpu-node
        spec:
          affinity:
            nodeAffinity:
              requiredDuringSchedulingIgnoredDuringExecution:
                nodeSelectorTerms:
                - matchExpressions:
                  - key: aliyun.accelerator/nvidia_name
                    operator: DoesNotExist
          containers:
          - name: not-in-gpu-node
            image: nginx
        ```

7.  In the left-side navigation pane of the details page, choose **Workloads** \> **Pods**.

    On the Pods page, you can find that the pods in the preceding examples are scheduled to the desired nodes. This means that labels can be used to schedule pods to GPU-accelerated nodes.


