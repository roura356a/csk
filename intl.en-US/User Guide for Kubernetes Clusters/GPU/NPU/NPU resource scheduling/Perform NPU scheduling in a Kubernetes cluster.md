---
keyword: [NPU scheduling, NPU resources]
---

# Perform NPU scheduling in a Kubernetes cluster

This topic describes how to create and use a Container Service for Kubernetes \(ACK\) cluster with neural processing unit \(NPU\) resources.

ACK and Resource Access Management \(RAM\) services are activated.

Compared with CPUs, the most obvious advantage of NPUs is fast data processing in complex algorithmic models. Different from the von Neumann architecture used by CPUs, NPUs adopt the data-driven parallel computing architecture. This architecture significantly increases computing capabilities and reduces power consumption by using data streams. NPUs are the most suitable option in scenarios where large amounts of data are processed, such as video and image processing. Compared with CPUs, NPUs offer 100 to 1,000 times faster processing speeds and also significantly lower power consumption.

You can create ACK clusters and use Hanguang NPU to run compute-intensive tasks, such as machine learning and image processing. You can deploy workloads and dynamically scale resources to meet your business requirements.

**Note:** For more information about Hanguang NPU, visit [this website](https://www.t-head.cn/).

This topic describes how to use NPU resources in a Kubernetes cluster. In the following example, an ACK cluster is created and an ecs.ebman1.26xlarge instance is added to the cluster.

ACK performs the following operation when it creates the cluster:

-   Create Elastic Compute Service \(ECS\) instances, configures a public key to enable Secure Shell \(SSH\) logon from master nodes to other nodes, and then configures the ACK cluster by using cloud-init.
-   Create a security group that allows access to a virtual private cloud \(VPC\) over Internet Control Message Protocol \(ICMP\).
-   If you do not specify an existing VPC, ACK creates a VPC and a vSwitch and creates SNAT rules for the vSwitch.
-   Add route entries to the VPC.
-   Create a NAT gateway and an elastic IP address \(EIP\).
-   Create a RAM user and grants it permissions to query, create, and delete ECS instances, and permissions to add and delete disks. The RAM user is also granted all permissions on Server Load Balancer \(SLB\), CloudMonitor, VPC, Log Service, and Apsara File Storage NAS. ACK also creates an AccessKey pair for the RAM user. ACK automatically creates SLB instances, disks, and VPC route entries based on your configurations.
-   Create an internal-facing SLB instance and open port 6443.
-   Create an Internet-facing SLB instance and open ports 6443, 8443, and 22. If you enable SSH logon when you create the cluster, port 22 is open. Otherwise, port 22 is not open.

## Use NPU resources

To allow a pod to use NPU resources, set the `aliyun.com/npu` parameter in `resources.limits`.

```
apiVersion: v1
kind: Pod
metadata:
  name: <The name of the pod>
spec:
  containers:
    - name: <The name of the container>
      image: <The name of the image>
      resources:
        limits:
          aliyun.com/npu: <The amount of NPU resources>
```

## Set up an NPU environment to run TensorFLow

You can use NPU resources to train TensorFlow models. In the following example, a pod is started to perform model training with NPU resources.

1.  Connect to the ACK cluster. For more information, see [Use kubectl on Cloud Shell to manage ACK clusters](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Access clusters/Use kubectl on Cloud Shell to manage ACK clusters.md).

    Run the following command in Cloud Shell:

    ```
    cat > test-pod.yaml <<- EOF
    apiVersion: v1
    kind: Pod
    metadata:
      name: test-npu-pod
    spec:
      restartPolicy: Never
      imagePullSecrets:
        - name: regsecret
      containers:
        - name: resnet50-npu
          image: registry.cn-shanghai.aliyuncs.com/hgai/tensorflow:v1_resnet50-tensorflow1.9.0-toolchain1.0.2-centos7.6
          resources:
            limits:
              aliyun.com/npu: 1 # requesting  NPUs
    EOF
    ```

2.  Run the following command to create a pod:

    ```
    kubectl apply -f test-pod.yaml
    ```

3.  Run the following command to query the pod state:

    ```
    kubectl get po test-npu-pod
    ```

    **Note:** If the pod state is Error, run the `kubectl logs test-npu-pod` command to check the pod log and troubleshoot the error.


Wait a few minutes and query the pod state again.

```
kubectl get po test-npu-pod
```

If the pod state is Completed, check the pod log again.

```
kubectl logs test-npu-pod
```

The following output indicates that the training is complete.

```
2019-10-30 12:10:50.389452: I tensorflow/core/platform/cpu_feature_guard.cc:141] Your CPU supports instructions that this TensorFlow binary was not compiled to use: AVX2 AVX512F FMA
100%|##########| 98/98 [00:26<00:00,  3.67it/s]
resnet_v1_50, result =  {'top_5': 0.9244321584701538, 'top_1': 0.7480267286300659}
```

