# Use labels to schedule pods to GPU nodes

To use Container Service for Kubernetes \(ACK\) clusters for GPU computing, you must schedule pods to GPU nodes. To enable flexible and efficient scheduling, you can add labels to the GPU nodes.

-   An ACK cluster is created and GPU nodes are added to the cluster. For more information, see [Configure a Kubernetes GPU cluster to support GPU scheduling](/intl.en-US/User Guide for Kubernetes Clusters/Scheduling/GPU scheduling/Configure a Kubernetes GPU cluster to support GPU scheduling.md).
-   A master node is connected. This provides an easy method for you to check node labels. For more information, see [Use kubectl to connect to an ACK cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Access clusters/Use kubectl to connect to an ACK cluster.md).

When the ACK cluster deploys nodes that are based on NVIDIA GPUs, the attributes of these GPUs are shown on node labels. These labels have the following benefits:

-   You can use the labels to filter GPU nodes.
-   The labels can be used as the conditions for pod scheduling.

1.  Log on to the [Container Service for Kubernetes \(ACK\) console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane, click **Clusters**.

3.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  On the **Nodes** page, find the GPU node that you want to manage, and choose **More** \> **Details** in the **Actions** column for the node.

    Check the labels of the GPU node.

    ![Node details](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/3935359951/p21059.png)

    You can also log on to a master node, and run the following command in the command-line interface \(CLI\) to view the labels on GPU nodes:

    Run the following command:

    ```
    kubectl get nodes
    ```

    Response:

    ```
    NAME                                STATUS    ROLES     AGE       VERSION
    cn-beijing.i-2ze2dy2h9w97v65uuaft   Ready     master    2d        v1.11.2
    cn-beijing.i-2ze8o1a45qdv5q8a7luz   Ready     <none>    2d        v1.11.2             # Compare these nodes with the nodes that are displayed in the console to identify GPU nodes.
    cn-beijing.i-2ze8o1a45qdv5q8a7lv0   Ready     <none>    2d        v1.11.2
    cn-beijing.i-2ze9xylyn11vop7g5bwe   Ready     master    2d        v1.11.2
    cn-beijing.i-2zed5sw8snjniq6mf5e5   Ready     master    2d        v1.11.2
    cn-beijing.i-2zej9s0zijykp9pwf7lu   Ready     <none>    2d        v1.11.2
                        
    ```

    Select a GPU node and run the following command to query the labels on the node:

    ```
    kubectl describe node cn-beijing.i-2ze8o1a45qdv5q8a7luz
    ```

    Response:

    ```
    Name:               cn-beijing.i-2ze8o1a45qdv5q8a7luz
    Roles:              <none>
    Labels:             aliyun.accelerator/nvidia_count=1                          # This field is important.
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

    In this example, the following labels are added to the GPU node:

    |key|value|
    |---|-----|
    |`aliyun.accelerator/nvidia_count`|The number of GPU cores.|
    |`aliyun.accelerator/nvidia_mem`|The size of the GPU memory. Unit: MiB.|
    |`aliyun.accelerator/nvidia_name`|The name of the NVIDIA graphics card.|

    GPU nodes of the same type have the same graphics card name. You can use this label to filter nodes.

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

5.  Go to the Cluster Information page. In the left-side navigation pane, click **Workload**. The **Deployments** tab appears. Click **Create from Template**.

    1.  Create a TensorFlow application and schedule this application to a GPU node.

        ![Create an application](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/3935359951/p21074.png)

        The following YAML template is used in this example:

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
              nodeSelector:                                                  # This field is important.
                aliyun.accelerator/nvidia_name: Tesla-M40
              containers:
              - name: tf-notebook
                image: tensorflow/tensorflow:1.4.1-gpu-py3
                resources:
                  limits:
                    nvidia.com/gpu: 1                                        # This field is important.
                ports:
                - containerPort: 8888
                  hostPort: 8888
                env:
                  - name: PASSWORD
                    value: mypassw0rdv
        ```

    2.  You can also allow a GPU node to repel specific applications. The following example shows how to deploy an NGINX pod and schedules the pod based on node affinity. For more information, see the section that describes node affinity in [Deploy a stateless application from an image](/intl.en-US/User Guide for Kubernetes Clusters/Application management/Workloads/Deploy a stateless application from an image.md).

        The following YAML template is used in this example:

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

6.  Click the **Pods** tab.

    On the Pods tab, the specified pods have been scheduled to the required nodes. You can use labels to schedule pods to specific GPU nodes with ease.


