Run a GPU-based TensorFlow training job 
============================================================

This topic describes how to use a Serverless Kubernetes (ASK) cluster and an elastic container instance to run a GPU-based TensorFlow training job. 

Background information 
-------------------------------------------

In recent years, artificial intelligence (AI) and machine learning have been widely applied in a large number of fields, and various training models have been developed. An increasing number of training jobs are run on the cloud. However, it is not easy to continuously run training jobs in a cloud environment. You may encounter the following difficulties:

* Difficulties in deploying environments: You must purchase a GPU-accelerated instance and install a GPU driver on the instance. After you prepare images for training jobs, you must install the GPU runtime hook.

  

* Lack of scalability: After you deploy an environment and run the training jobs, you may need to release idle resources to save costs. The next time you want to run training jobs, you must deploy an environment and create instances again. If compute nodes are insufficient, you must scale out the compute nodes. In this case, you must create instances and deploy the environment again.

  




To resolve the preceding difficulties, we recommend that you use ASK clusters and elastic container instances to run training jobs. This solution has the following benefits:

* You are charged on a pay-as-you-go basis and do not need to manage resources.

  

* You need only to prepare the configurations once. Then, you can reuse the configurations without limits.

  

* The image cache feature allows you to create pods and start training jobs in a more efficient manner.

  

* Training data is decoupled from training models. Training data can be persisted.

  




Preparations 
---------------------------------

1. Prepare a container image and training data for the training model. 

   In this example, a TensorFlow training job on GitHub is used. You can obtain the sample image eci/tensorflow from Alibaba Cloud Container Registry. For more information, see [TensorFlow Model Garden](https://github.com/tensorflow/models?spm=ata.13261165.0.0.4e0c9e6eiEsp0z).
   

2. Create an ASK cluster. 

   Create an ASK cluster in the [Container Service for Kubernetes console](https://cs.console.aliyun.com/?spm=a2c4g.11186623.2.16.61a036c8iieu29&amp;accounttraceid=caa7f456b82e4e998e461e8bee1a3cb6xetj#/k8s/cluster/list). For more information, see [Create an ASK cluster](/intl.en-US/User Guide for Serverless Kubernetes Clusters/Cluster/Create an ASK cluster.md). 
   **Note**

   If you want to pull an image from the Internet or if your training jobs need to access the Internet, you must configure a network address translation (NAT) gateway.

   You can use kubectl to manage and access the ASK cluster. You can use one of the following methods:
   * If you want to manage the cluster from your computer, install and configure the kubectl client. For more information, see [Use kubectl to connect to an ACK cluster](/intl.en-US/User Guide for Serverless Kubernetes Clusters/Cluster/Manage and access clusters/Use kubectl to connect to an ASK cluster.md).

     
   
   * Use kubectl on Cloud Shell to manage the cluster. For more information, see [Use kubectl on Cloud Shell to manage ACK clusters](/intl.en-US/User Guide for Serverless Kubernetes Clusters/Cluster/Manage and access clusters/Use kubectl on Cloud Shell to manage ASK clusters.md).

     
   

   

3. Create a network-attached storage (NAS) file system and add a mount target. 

   Create a NAS file system and add a mount target in the [NAS File System console](https://nasnext.console.aliyun.com/overview). For more information, see [Manage file systems]() and [Manage mount targets](). 
   **Note**

   The NAS file system and the ASK cluster must be in the same VPC.
   




Create an image cache 
------------------------------------------

The image cache feature has been integrated into Kubernetes CRD to accelerate the pulling of images. For more information, see [Use an image cache CRD to accelerate pod creation](). 

Perform the following operations:

1. Prepare the YAML file. 

   The following code shows an example of the imagecache.yaml file:

       apiVersion: eci.alibabacloud.com/v1
       kind: ImageCache
       metadata:
         name: tensorflow
       spec:
         images:
         - registry-vpc.cn-beijing.aliyuncs.com/eci/tensorflow:1.0 # The image of the training job. We recommend that you upload the image to Alibaba Cloud Container Registry. The endpoint of the VPC is used. Make sure that the VPC is the same as the one to which the cluster belongs.

   

2. Create an image cache. 

       kubectl create -f imagecache.yaml

   

   You must pull an image when you create an image cache. The amount of time required to pull an image is related to the image size. You can run the following command to view the creation progress of the image cache: 

       Kubectl get imagecache tensorflow

   

   A command output similar to the following one indicates that the image cache is created. 

       NAME         AGE   CACHEID                    PHASE   PROGRESS
       tensorflow   11m   imc-2ze1xczztv7tgesg****   Ready   100%

   




Create a training job 
------------------------------------------

You can use the image cache to create a training job. 

1. Prepare the YAML file. 

   The following code shows an example of the gpu_pod.yaml file:

       apiVersion: v1
       kind: Pod
       metadata:
         name: tensorflow
         annotations:
           k8s.aliyun.com/eci-use-specs: "ecs.gn6i-c4g1.xlarge"  # Specify the GPU-accelerated instance type that is used to create an elastic container instance. Example: k8s.aliyun.com/eci-instance-type: "ecs.gn5i-c2g1.large"
           k8s.aliyun.com/eci-image-cache: "true"             # Enable Automatically Match Image Cache.
       spec:
         containers:
         - name: tensorflow
           image: registry-vpc.cn-beijing.aliyuncs.com/eci/tensorflow:1.0 # The image of the training job.
           command:
             - "sh"
             - "-c"
             - "python models/tutorials/image/imagenet/classify_image.py" # The script used to start the training job.
           resources:
             limits:
               nvidia.com/gpu: "1"   # The number of GPUs required by the container.
           volumeMounts:
           - name: nfs-pv
             mountPath: /tmp/imagenet
         volumes:   
         - name: nfs-pv   #Persist the training results to NAS files.
           flexVolume:
               driver:  alicloud/nas
               fsType: nfs
               options:
                 server: 16cde4****-ijv**.cn-beijing.nas.aliyuncs.com     #The mount target of the NAS file system.
                 path: /         #The mount directory.
         restartPolicy: OnFailure

   

2. Run the following command to create a pod: 

       kubectl create -f gpu_pod.yaml

   

3. View the execution results. 

   You can view events or logs. 
   * View events

         kubectl describe pod tensorflow

     
   
   * View logs

         kubectl logs tensorflow

     
   

   




View the results 
-------------------------------------

You can view the results of the training job in the console. 

* You can view the storage capacity occupied by the training data in the [NAS File System console](https://nasnext.console.aliyun.com/overview).![nas ](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/8468059161/p238671.png)

  **Note**

  Training results are stored in the configured NAS file system. After the NAS file system is mounted, you can obtain the results in the corresponding path.
  

* You can view the elastic container instance that is successfully run in the [Elastic Container Instance console](https://eci.console.aliyun.com).![tensorflow](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/8468059161/p238638.png)

  **Note**

  If an elastic container instance is successfully run, containers in the instance are stopped. Then, the system reclaims the underlying computing resources, and the billing of pods is stopped.
  



