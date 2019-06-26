# Use Alibaba Cloud Kubernetes GPU node labels for scheduling {#task_ryg_clw_pfb .task}

When you implement GPU computing through a Kubernetes cluster, you can schedule an application to the node installed with GPU devices as needed by using GPU node labels.

-   You have created a Kubernetes cluster that has GPU nodes. For more information, see [Configure a Kubernetes GPU cluster to support GPU scheduling](reseller.en-US/User Guide/Kubernetes cluster/Cluster management/Configure a Kubernetes GPU cluster to support GPU scheduling.md#).
-   You have connected to the Master node, which makes it easier to view node labels and other information. For more information, see [Connect to a Kubernetes cluster by using kubectl](reseller.en-US/User Guide/Kubernetes cluster/Cluster management/Connect to a Kubernetes cluster by using kubectl.md#).

When deploying NVIDIA GPU nodes, Kubernetes that runs on Alibaba Cloud discovers the GPU attribute and exposes it as the node label information. Node labels provide the following benefits:

1.  Node labels help you filter GPU nodes.
2.  Node labels can be used as the scheduling conditions for application deployment.

1.  Log on to the [Container Service console](https://partners-intl.console.aliyun.com/#/cs). 
2.   In the left-side navigation pane under Container Service-Kubernetes, choose **Clusters** \> **Nodes**. 

    **Note:** In this example, the cluster has three Worker nodes of which two Worker nodes are mounted with GPU devices. You need to view the node IP addresses for verification.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/40309/156154305121050_en-US.png)

3.   Select a GPU node, and choose **More** \> **Details** in the action column. Then, you can view the GPU node label on the Kubernetes dashboard. 

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/40309/156154305121059_en-US.png)

    You can also log on to a Master node and run the following command to view the GPU node label:

    ```
    # kubectl get nodes
    NAME                                STATUS    ROLES     AGE       VERSION
    cn-beijing.i-2ze2dy2h9w97v65uuaft   Ready     master    2d        v1.11.2
    cn-beijing.i-2ze8o1a45qdv5q8a7luz   Ready     <none>    2d        v1.11.2             #Compare this node with the node displayed in the console to determine the GPU node.
    cn-beijing.i-2ze8o1a45qdv5q8a7lv0   Ready     <none>    2d        v1.11.2
    cn-beijing.i-2ze9xylyn11vop7g5bwe   Ready     master    2d        v1.11.2
    cn-beijing.i-2zed5sw8snjniq6mf5e5   Ready     master    2d        v1.11.2
    cn-beijing.i-2zej9s0zijykp9pwf7lu   Ready     <none>    2d        v1.11.2
    					
    ```

    Select a GPU node and run the following command to view the GPU node label:

    ```
    # kubectl describe node cn-beijing.i-2ze8o1a45qdv5q8a7luz
    Name:               cn-beijing.i-2ze8o1a45qdv5q8a7luz
    Roles:              <none>
    Labels:             aliyun.accelerator/nvidia_count=1                          #This field is important.
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

    In this example, the GPU node contains the following three node labels:

    |Key|Value|
    |---|-----|
    | `aliyun.accelerator/nvidia_count` |Number of GPU cores|
    | `aliyun.accelerator/nvidia_mem` |GPU memory in MiB|
    | `aliyun.accelerator/nvidia_name` |Name of the GPU computing card of the NVIDIA device|

    The GPU cloud servers of the same type share the same GPU computing card name. Therefore, you can use this label to filter nodes.

    ```
    # kubectl get no -l aliyun.accelerator/nvidia_name=Tesla-M40
    NAME                                STATUS    ROLES     AGE       VERSION
    cn-beijing.i-2ze8o1a45qdv5q8a7luz   Ready     <none>    2d        v1.11.2
    cn-beijing.i-2ze8o1a45qdv5q8a7lv0   Ready     <none>    2d        v1.11.2
    					
    ```

4.   Return to the Container Service console home page. Then, in the left-side navigation pane, choose **Applications** \> **Deployments**, and click **Create by Template** in the upper-right corner. 
    1.   Create a TensorFlow application and schedule this application to the GPU node. 

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/40309/156154305221074_en-US.png)

        In this example, the YAML template is orchestrated as follows:

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
          selector: # define how the deployment finds the pods it manages
            matchLabels:
              app: tf-notebook
          template: #Define the pod specifications.
            metadata:
              labels:
                app: tf-notebook
            spec:
              nodeSelector:                                                  #This field is important.
                aliyun.accelerator/nvidia_name: Tesla-M40
              containers:
              - name: tf-notebook
                image: tensorflow/tensorflow:1.4.1-gpu-py3
                resources:
                  limits:
                    nvidia.com/gpu: 1                                        #This field is important.
                ports:
                - containerPort: 8888
                  hostPort: 8888
                env:
                  - name: PASSWORD
                    value: mypassw0rdv
        ```

    2.   You can also avoid deploying an application to a GPU node. The following deploys an Nginx pod and schedules it by using the node affinity feature. For more information about node affinity, see [Create a deployment application by using an image](reseller.en-US/User Guide/Kubernetes cluster/Application management/Create a deployment application by using an image.md#). 

        The example YAML template is orchestrated as follows:

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

5.   In the left-side navigation pane, choose **Applications** \> **Pods**, and select the target cluster and namespace. 

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/40309/156154305221094_en-US.png)


In the pod list, you can see that the two example pods have been scheduled to the target nodes, indicating you have implemented flexible scheduling by using GPU node labels.

