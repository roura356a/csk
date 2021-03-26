Run a GPU-based TensorFlow training job 
============================================================

In recent years, AI and machine learning have been widely applied in a large number of fields. Various training models have been developed, and more training jobs are run on clouds. However, it is not easy to continuously run training jobs in a cloud environment. Some of the pain points are as follows:

* Difficulties in environment configuration: For example, if you want to use a GPU instance, you must first purchase an instance and install a GPU driver. After you [prepare the images of training jobs](https://hub.docker.com/r/tensorflow/tensorflow/?spm=ata.13261165.0.0.4e0c9e6eiEsp0z), you also need to install the [GPU runtime hook](https://github.com/NVIDIA/nvidia-docker?spm=ata.13261165.0.0.4e0c9e6eiEsp0z). Such a procedure is demanding. With Alibaba Cloud Serverless Kubernetes and Elastic Container Instance (ECI), you do not need to worry about the environment configuration.

  

* Lack of flexibility: After the environment is configured and jobs are run, you may need to release idle resources to reduce costs. Then, you need to create instances and configure the environment again the next time you run training jobs. When compute nodes are insufficient, you need to scale out compute nodes. In this case, you have to configure the environment again. With Alibaba Cloud Serverless Kubernetes and ECI, you can use only required resources based on your business requirements, which are charged in the pay-as-you-go mode.

  




This topic describes how to use Alibaba Cloud Serverless Kubernetes and ECI to quickly run a GPU-based [TensorFlow training job](https://github.com/tensorflow/models?spm=ata.13261165.0.0.4e0c9e6eiEsp0z) from GitHub.

Procedure 
------------------------------

For more information about how to create and manage a serverless Kubernetes cluster, see [Use ECI in Serverless Kubernetes]().

* Log on to the [Container Service console](https://cs.console.aliyun.com/) and create a serverless Kubernetes cluster. When you create the cluster, you need to specify the region, Virtual Private Cloud (VPC), and VSwitch. If you need to pull images from the Internet or your training jobs need to access the Internet, specify a network address translation (NAT) gateway.

  ![](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/9255890951/p112194.png)

  

* After the cluster is created, find the cluster in the cluster list and click Manage in the Actions column. Then, install and configure the kubectl client in the cluster.

  

* ECI allows you to use an image cache to accelerate image pulling. ECI provides this feature through ImageCache, which is a standard Kubernetes Custom Resource Definition (CRD) that can be used through Virtual Kubelet. The time required to pull an image for the first time depends on the size of the image. Wait until the image is pulled.

  * Run the following command to create an image cache: `kubectl create -f imagecache.yaml`.

    
  
  * The content of the imagecache.yaml file is as follows:

        apiVersion: eci.alibabacloud.com/v1
        kind: ImageCache
        metadata:
        name: tensorflow
        spec:
        images:
        - registry-vpc.cn-zhangjiakou.aliyuncs.com/eci/tensorflow:1.0 # The image of the training job. We recommend that you store the image in a private repository that resides in a VPC.                                        

    
  
  * Run the following command to query the status of the image cache: `kubectl get imagecache tensorflow`.

    
  

  

* Run the following command to use the image cache to create a training job: `kubectl create -f gpu_pod.yaml`.

  * The content of the gpu_pod.yaml file is as follows:

    
  

  

              apiVersion: v1
              kind: Pod
              metadata:
        name: tensorflow
        annotations:
                      k8s.aliyun.com/eci-gpu-type: "P4" # The type of the GPU. You can also specify an instance type of Elastic Compute Service (ECS), for example, k8s.aliyun.com/eci-instance-type: "ecs.gn5i-c2g1.large".  
                      k8s.aliyun.com/eci-image-cache: "true" # Specifies whether to enable automatic image cache match.
              spec:
        containers:
                - image: registry-vpc.cn-zhangjiakou.aliyuncs.com/eci/tensorflow:1.0 # The image of the training job.
                      name: tensorflow
                      command:
                      - "sh"
                      - "-c"
                      - "python models/tutorials/image/imagenet/classify_image.py" # The script used to start the training job.
                      resources:
                        limits:
                              nvidia.com/gpu: "1" # The number of GPUs to be allocated to a container.
                      volumeMounts:
                      - name: nfs-pv
                        mountPath: /tmp/imagenet 
        volumes:
                - name: nfs-pv # The result of the training job is stored in an Apsara File Storage NAS file system for long-term storage.
                      nfs:
                        path: /share
                        server: 0912430d-1nsl.cn-zhangjiakou.extreme.nas.aliyuncs.com
        restartPolicy: OnFailure                

  
  * Run the following command to query the events and logs of the ECI:
    

    * kubectl describe pod tensorflow

      
    
    * kubectl logs tensorflow ![](http://docs-aliyun.cn-hangzhou.oss.aliyun-inc.com/assets/pic/145513/cn_zh/1575260820057/logs.png)

      
    

    
  
  * You can find the result and data of the training job in the Apsara File Storage NAS file system, as shown in the following figure.![](http://docs-aliyun.cn-hangzhou.oss.aliyun-inc.com/assets/pic/145513/cn_zh/1575260858201/nas.png)

    
  

  

  

* In the ECI console, you can find the ECI. The ECI is in the Succeeded state, which is its final status. When the ECI enters this state, its underlying computing resources are reclaimed and the ECI is no longer charged.

  




Summary 
----------------------------

You can use Alibaba Cloud Serverless Kubernetes and ECI to quickly run a GPU-based training job. The main procedure is as follows:

1. Obtain the image of the training job and prepare the storage for storing the data and result of the training job.

   

2. Create a serverless Kubernetes cluster.

   

3. Prepare the YAML files and create the corresponding objects in sequence in the cluster.

   




Running a training job in Alibaba Cloud Serverless Kubernetes and ECI has the following benefits:

1. Resources are charged in the pay-as-you-go mode, and you do not need to manage resources.

   

2. You only need to prepare the configurations once. Then, you can reuse the configurations without limits.

   

3. The image cache feature accelerates ECI creation. This brings extremely smooth experience during a scale-out.

   

4. The training data and training model are decoupled. The training data can be stored to a persistent storage, for example, Apsara File Storage NAS. ECI also allows you to store data to Alibaba Cloud disks.

   



