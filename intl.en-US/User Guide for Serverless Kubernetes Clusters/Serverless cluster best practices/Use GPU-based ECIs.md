# Use GPU-based ECIs

This topic describes how to use GPU-based elastic container instances \(ECIs\). In the example, TensorFlow is used to recognize images. This feature is applicable to serverless Kubernetes clusters and virtual nodes in the clusters.

Alibaba Cloud Serverless Kubernetes supports GPU-based ECIs. Users can quickly run AI computing tasks in a serverless manner. This facilitates the operations and maintenance of the AI platform and improves the computing efficiency.

AI computing depends on GPU instances. However, building a GPU cluster environment is a complex task that involves purchasing GPU specifications, preparing machines, and installing drivers and the container environment. The serverless delivery mode of GPU resources demonstrates the core advantages of going serverless. This mode provides standardized and immediate resource supply capabilities. Users do not need to purchase machines or log on to nodes to install the GPU drivers. This simplifies the deployment of the AI platform and allows you to focus on the development and maintenance of AI models and applications rather than the infrastructure of the AI platform. GPU and CPU resources are ready to use and easy to obtain. Compared with the subscription billing method, the pay-as-you-go billing method reduces both the costs and resource waste.

You can create a GPU-mounted pod in Alibaba Cloud Serverless Kubernetes, use an annotation to specify the GPU type, and specify the type and the number of GPU instances in resource.limits. Each pod exclusively occupies the GPU. The costs of the GPU instances are the same as those for ECS GPU resources and no extra charges are incurred.

**Note:** vGPU-mounted pods are not supported.

## Prerequisites

A serverless Kubernetes cluster is created or a virtual node is created in a Kubernetes cluster. For more information, see [Create a serverless Kubernetes cluster](/intl.en-US/User Guide for Serverless Kubernetes Clusters/Quick start/Create a serverless Kubernetes cluster.md) and [Virtual nodes](/intl.en-US/User Guide for Kubernetes Clusters/Virtual nodes and ECI/Virtual nodes.md).

The following describes how to recognize images by using TensorFlow in a serverless Kubernetes cluster.

![image](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/5397297951/p47460.png)

1.  Log on to the [Container Service for Kubernetes \(ACK\) console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane, click **Serverless Clusters**.

3.  On the Clusters page, click the name of a cluster or click **Details** in the **Actions** column.

4.  In the left-side navigation pane, click **Workload**.

5.  Click the **Deployments** tab. On the tab that appears, click **Create from Template**, select a sample template or customize a template, and then click **Create**.

    You can use the following YAML template to create a pod. In this example, the specified GPU type in the pod is P4 and the number of GPUs is 1.

    ```
    apiVersion: v1
    kind: Pod
    metadata:
      name: tensorflow
      annotations:
        k8s.aliyun.com/eci-gpu-type : "P4"
    spec:
      containers:
      - image: registry-vpc.cn-hangzhou.aliyuncs.com/ack-serverless/tensorflow
        name: tensorflow
        command:
        - "sh"
        - "-c"
        - "python models/tutorials/image/imagenet/classify_image.py"
        resources:
          limits:
            nvidia.com/gpu: "1"
      restartPolicy: OnFailure
    ```

6.  In the left-side navigation pane, choose Applications **Pods** to view the pod status.

7.  Click the pod that you want to view. On the Pods - tensorflow page, click the **Logs** tab. If the content in the following red square appears, the image is recognized.

    ![Pods](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/2350852061/p172832.png)


For information about how to use this feature on a virtual node in a Kubernetes cluster, see [Virtual nodes](/intl.en-US/User Guide for Kubernetes Clusters/Virtual nodes and ECI/Virtual nodes.md). You need to schedule the pod to the virtual node, or create the pod in a namespace that contains virtual-node-affinity-injection=enabled label. Then, use the following file to replace the YAML file in Step [5](#step_a0i_3au_ds9).

```
apiVersion: v1
kind: Pod
metadata:
  name: tensorflow
  annotations:
    k8s.aliyun.com/eci-gpu-type : "P4"
spec:
  containers:
  - image: registry-vpc.cn-hangzhou.aliyuncs.com/ack-serverless/tensorflow
    name: tensorflow
    command:
    - "sh"
    - "-c"
    - "python models/tutorials/image/imagenet/classify_image.py"
    resources:
      limits:
        nvidia.com/gpu: "1"
  restartPolicy: OnFailure
  nodeName: virtual-kubelet
```

**Note:** The virtual node-based method supports multiple in-depth learning frameworks, including Kubeflow, arena, and other custom resource definitions \(CRDs\).

