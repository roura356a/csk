---
keyword: [GPU topology-aware scheduling, Kubernetes scheduling, deep learning]
---

# Activate GPU topology-aware scheduling in distributed training scenarios

Based on the scheduling framework, Container Service for Kubernetes \(ACK\) supports GPU topology-aware scheduling. You can use this feature to select a combination of GPUs on GPU nodes and provide the optimal training speed. This topic describes how to activate GPU topology-aware scheduling and compares the examples before and after GPU topology-aware scheduling is activated. GPU topology-aware scheduling can be used to improve user experience.

## Limits

-   All the pods that submit jobs must request the same resources, such as CPU and GPU resources.
-   The resources that are requested by pods must meet specific requirements before the pods can be created to submit and start jobs. Otherwise, the requests remain in the state of waiting for resources.

## Configure nodes

You can run the following command to set the node label and explicitly activate GPU topology-aware scheduling for nodes:

```
kubectl label node <Your Node Name>  ack.node.gpu.schedule=topology
```

**Note:** When GPU topology-aware scheduling is activated on nodes, common GPU resource scheduling is no longer supported. You can run the following command to change the label and resume common GPU resource scheduling.

```
kubectl label node <Your Node Name>  ack.node.gpu.schedule=default --overwrite
```

## Example 1: GPU topology-aware scheduling is not activated

**Note:** You must install [Arena](https://github.com/kubeflow/arena) or deploy only [pytorch-operator](https://github.com/kubeflow/pytorch-operator) before you test the following example.

The following example shows PyTorch when GPU topology-aware scheduling is not activated.

```
apiVersion: "kubeflow.org/v1"
kind: "PyTorchJob"
metadata:
  name: "pytorch-dist-nccl"
spec:
  pytorchReplicaSpecs:
    Master:
      replicas: 1
      restartPolicy: OnFailure
      template:
        metadata:
          annotations:
            sidecar.istio.io/inject: "false"
        spec:
          containers:
            - name: pytorch
              image: registry.cn-hangzhou.aliyuncs.com/kubernetes-image-hub/pytorch_dist_mnist:1.3-gpu-py3
              command:
              - python
              args: ["/opt/mnist/src/mnist.py", "--backend", "nccl"]
              resources: 
                limits:
                  nvidia.com/gpu: 1
              env:
                - name: NCCL_DEBUG
                  value: "INFO"
                - name: NCCL_DEBUG_SUBSYS
                  value: "GRAPH"
    Worker:
      replicas: 1
      restartPolicy: OnFailure
      template:
        metadata:
          annotations:
            sidecar.istio.io/inject: "false"
        spec:
          containers:
            - name: pytorch
              image: registry.cn-hangzhou.aliyuncs.com/kubernetes-image-hub/pytorch_dist_mnist:1.3-gpu-py3
              command:
              - python
              args: ["/opt/mnist/src/mnist.py", "--backend", "nccl"]
              resources: 
                limits:
                  nvidia.com/gpu: 1
              env:
                - name: NCCL_DEBUG
                  value: "INFO"
                - name: NCCL_DEBUG_SUBSYS
                  value: "GRAPH"
```

1.  After you create a job, run the following command to check the job status.

    ```
    kubectl get pods
    ```

    ```
    NAME                         READY   STATUS      RESTARTS   AGE
    pytorch-dist-nccl-master-0   1/1     Running   0          1m29s
    pytorch-dist-nccl-worker-0   1/1     Running   0          1m29s
    ```

2.  Run the following command to view logs. The following figure shows that GPUs in different pods do not use NVLinks to communicate with each other.

    ```
    kubectl logs -f pytorch-dist-nccl-master-0
    ```

    ![image 6](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/5727528061/p183761.png)


## Example 2: Configure a job when GPU topology-aware scheduling is activated

The following example shows PyTorch when GPU topology-aware scheduling is activated.

```
apiVersion: "kubeflow.org/v1"
kind: "PyTorchJob"
metadata:
  name: "pytorch-dist-nccl"
spec:
  pytorchReplicaSpecs:
    Master:
      replicas: 1
      restartPolicy: OnFailure
      template:
        metadata:
          labels:
            gpu-topology: pytorch-dist-nccl # Identifies the name of the distributed training job.
            gpu-topology-replica: "2" # Specifies the total number of distributed training replicas.
          annotations:
            sidecar.istio.io/inject: "false"
        spec:
          hostIPC: true  # Enables hostIPC.
          hostNetwork: true # Enables hostNetwork.
          hostPID: true # Enables hostPID.
          dnsPolicy: ClusterFirstWithHostNet # Sets dnsPolicy to ClusterFirstWithHostNet.
          containers:
            - name: pytorch
              image: registry.cn-hangzhou.aliyuncs.com/kubernetes-image-hub/pytorch_dist_mnist:1.3-gpu-py3
              command:
              - python
              args: ["/opt/mnist/src/mnist.py", "--backend", "nccl"]
              resources: 
                limits:
                  aliyun.com/gpu: 1
    Worker:
      replicas: 1
      restartPolicy: OnFailure
      template:
        metadata:
          labels:
            gpu-topology: pytorch-dist-nccl # Identifies the name of the distributed training job.
            gpu-topology-replica: "2" # Specifies the total number of distributed training replicas.
          annotations:
            sidecar.istio.io/inject: "false"
        spec:
          hostIPC: true  # Enables hostIPC.
          hostNetwork: true # Enables hostNetwork.
          hostPID: true # Enables hostPID.
          dnsPolicy: ClusterFirstWithHostNet # Sets dnsPolicy to ClusterFirstWithHostNet.
          containers:
            - name: pytorch
              image: registry.cn-hangzhou.aliyuncs.com/kubernetes-image-hub/pytorch_dist_mnist:1.3-gpu-py3
              command:
              - python
              args: ["/opt/mnist/src/mnist.py", "--backend", "nccl"]
              resources: 
                limits:
                  aliyun.com/gpu: 1 # Changes the resource request name to aliyun.com/gpu.
```

1.  Run the following command to set the values of gpu-topology and gpu-topology-replica in the labels section:

    ```
    labels:
        gpu-topology: pytorch-dist-nccl
        gpu-topology-replica: "2"
    ```

    -   gpu-topology: the name of a distributed training job.
    -   gpu-topology-replica: the total number of replicas on both master nodes and workers nodes for distributed training.
2.  Run the following commands to enable hostIPC, hostPID, and hostNetwork, and set dnsPolicy to ClusterFirstWithHostNet:

    ```
    spec:
      hostIPC: true 
      hostNetwork: true
      hostPID: true
      dnsPolicy: ClusterFirstWithHostNet
    ```

3.  Run the following command to change the request name from nvidia.com/gpu to aliyun.com/gpu:

    ```
    resources: 
      limits:
        aliyun.com/gpu: 1
    ```

4.  Test the job.

    1.  Run the following command to check the status of the created job:

        ```
        kubectl get pods
        ```

        ```
        NAME                         READY   STATUS    RESTARTS   AGE
        pytorch-dist-nccl-master-0   1/1     Running   0          55s
        pytorch-dist-nccl-worker-0   1/1     Running   0          55s
        ```

    2.  Run the following command to view logs. The following figure shows that GPUs in different pods use NVLinks to communicate with each other.

        ```
        kubectl logs -f pytorch-dist-nccl-master-0
        ```

        ![GPUNVL](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/5727528061/p184817.png)


[Overview]()

[Install the ack-ai-installer component](/intl.en-US/User Guide for Kubernetes Clusters/Introduction/GPU topology-aware scheduling/Install the ack-ai-installer component.md)

