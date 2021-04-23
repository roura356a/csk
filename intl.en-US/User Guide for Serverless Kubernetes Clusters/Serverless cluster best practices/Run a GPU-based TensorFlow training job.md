# Run a GPU-based TensorFlow training job

In recent years, AI and machine learning have been applied to a wide array of fields. Various training models have been developed, and a growing number of training jobs are running on clouds. However, it is not easy to continuously run training jobs in a cloud environment. Some of the difficulties include:

-   Difficulties in deploying environments: If you want to use a GPU-accelerated instance, you must first purchase an instance and install a GPU driver. After you [prepare the images of training jobs](https://hub.docker.com/r/tensorflow/tensorflow/?spm=ata.13261165.0.0.4e0c9e6eiEsp0z), you must install the [GPU runtime hook](https://github.com/NVIDIA/nvidia-docker?spm=ata.13261165.0.0.4e0c9e6eiEsp0z). This procedure is time-consuming. To simplify the procedure, Alibaba Cloud provides serverless Kubernetes \(ASK\) clusters and elastic container instances.
-   Lack of scalability: After you have deployed an environment and run jobs, you may need to release idle resources to save costs. The next time you want to run training jobs, you must deploy an environment and create instances again. If compute nodes are insufficient, you must scale out the compute nodes. In this case, you must configure the environment again. ASK clusters and elastic container instances allow you to use only required resources. You are charged on a pay-as-you-go basis.

This topic describes how to use an ASK cluster and an elastic container instance to quickly run a GPU-based [TensorFlow training job](https://github.com/tensorflow/models?spm=ata.13261165.0.0.4e0c9e6eiEsp0z).

## Procedure

For more information about how to create and manage an ASK cluster, see [Use ECI in ASK clusters]().

-   Log on to the [Container Service for Kubernetes \(ACK\) console](https://cs.console.aliyun.com/) and create an ASK cluster. When you create the cluster, you must specify the region, Virtual Private Cloud \(VPC\), and vSwitch. If you want to pull images from the Internet or your training jobs need to access the Internet, specify a network address translation \(NAT\) gateway.

    ![](../images/p174772.png)

-   After the cluster is created, find the cluster in the cluster list and click **Manage** in the Actions column. Then, install and configure the kubectl client in the cluster.

-   Elastic Container Instance provides the ImageCache feature that allows you to use a cached image instead of pulling an image to accelerate image pulling. This feature is a standard Kubernetes Custom Resource Definition \(CRD\) that can be used through Virtual Kubelet. The time required to pull an image for the first time is based on the size of the image. Wait until the image is pulled.

    -   Run the following command to cache an image: `kubectl create -f imagecache.yaml`.
    -   The content of the imagecache.yaml file is:

        ```
        apiVersion: eci.alibabacloud.com/v1
        kind: ImageCache
        metadata:
        name: tensorflow
        spec:
        images:
        - registry-vpc.cn-zhangjiakou.aliyuncs.com/eci/tensorflow:1.0 # The image of the training job. We recommend that you store the image in a private repository that resides in a VPC.					
        ```

    -   You can run the following command to query the status of the image: `kubectl get imagecache tensorflow`.
-   Run the following command to use the cached image to create a training job: `kubectl create -f gpu_pod.yaml`.

    -   The content of the gpu\_pod.yaml file is:
    ```
    apiVersion: v1
    kind: Pod
    metadata:
      name: tensorflow
      annotations:
        k8s.aliyun.com/eci-use-specs: "ecs.gn6i-c4g1.xlarge"  # The GPU specification. You can also specify an ECS instance type, for example, k8s.aliyun.com/eci-instance-type: "ecs.gn5i-c2g1.large".
        k8s.aliyun.com/eci-image-cache: "true" # Specify whether to enable automatic image match.
    spec:
      containers:
        image: registry-vpc.cn-zhangjiakou.aliyuncs.com/eci/tensorflow:1.0 # The image of the training job.
        name: tensorflow
        command:
          - "sh"
          - "-c"
          - "python models/tutorials/image/imagenet/classify_image.py" # The script used to start the training job.
        resources:
          limits:
            nvidia.com/gpu: "1" # The number of GPUs to be allocated to the container.
        volumeMounts:
          name: nfs-pv
          mountPath: /tmp/imagenet
      volumes:
        name: nfs-pv # The result of the training job is persisted to an Apsara File Storage NAS file system.
        nfs:
          path: /share
          server: 0912430d-1nsl.cn-zhangjiakou.extreme.nas.aliyuncs.com
      restartPolicy: OnFailure
    ```

    -   Run the following command to query the events and logs of the pod:
        -   kubectl describe pod tensorflow
        -   kubectl logs tensorflow ![](http://docs-aliyun.cn-hangzhou.oss.aliyun-inc.com/assets/pic/145513/cn_zh/1575260820057/logs.png)
    -   You can find the result and data of the training job in the Apsara File Storage NAS file system, as shown in the following figure.![](http://docs-aliyun.cn-hangzhou.oss.aliyun-inc.com/assets/pic/145513/cn_zh/1575260858201/nas.png)
-   In the Elastic Container Instance console, you can find the elastic container instance. The elastic container instance is in the Succeeded state, which is its final state. When the elastic container instance enters this state, its underlying computing resources are reclaimed and the elastic container instance is no longer billed.![](http://docs-aliyun.cn-hangzhou.oss.aliyun-inc.com/assets/pic/145513/cn_zh/1575260950593/eci.png)


## Conclusion

You can use an ASK and an elastic container instance to quickly run a GPU-based training job. The procedure is:

1.  Obtain the image of the training job and prepare the storage for storing the data and result of the training job.
2.  Create an ASK cluster.
3.  Prepare the YAML files and create the corresponding objects in sequence in the cluster.

ASK clusters and elastic container instances have the following benefits:

1.  You are charged for resources on a pay-as-you-go basis. Maintenance is not required.
2.  After an environment is deployed, it can be reused.
3.  You can use cached images to accelerate the creation of instances.
4.  The training data and training model are decoupled. The training data can be persisted, for example, in Apsara File Storage NAS. Elastic Container Instance also allows you to store data on disks.

