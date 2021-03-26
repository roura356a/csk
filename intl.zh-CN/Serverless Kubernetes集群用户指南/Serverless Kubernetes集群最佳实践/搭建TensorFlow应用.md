搭建TensorFlow应用 
===================================

本文介绍如何使用阿里云Serverless Kubernetes（ASK）和弹性容器实例（ECI），快速完成基于GPU的TensorFlow训练任务。

背景信息 
-------------------------

人工智能与机器学习已经被广泛应用到各个领域，近些年来各种各样的训练模型被提出，更多的训练任务运行到云上。然而上云之后，想要轻松、持久地运行训练任务，仍有一些痛点，例如：

* 环境搭建麻烦：您需要购买GPU实例并安装GPU驱动，即使您已经把训练任务容器化，还需要安装GPU runtime hook 。

  

* 使用缺乏弹性：运行完任务后，为了节约成本，您可能需要释放闲置资源，但在下次启动任务时您需要重新创建实例并配置环境；或者在计算节点资源不够的情况下，扩容需要您再次创建实例并配置环境。

  




针对上述痛点，推荐您使用ASK+ECI的方案来运行训练任务，可以帮助您轻松地创建和启动训练任务。该方案还具备以下优势：

* 按需付费，免运维。

  

* 一次配置，无限次复用。

  

* 镜像缓存功能加速Pod创建，训练任务启动快速。

  

* 数据与训练模型解耦，数据可以持久化存储。

  




准备工作 
-------------------------

1. 准备好训练模型的容器镜像和训练数据。

   本文以Github的一个TensorFlow训练任务为例，相关示例镜像（eci/tensorflow）您可以从阿里云容器镜像仓库中获取。更多信息，请参见[TensorFlow训练任务](https://github.com/tensorflow/models?spm=ata.13261165.0.0.4e0c9e6eiEsp0z)。
   

2. 创建ASK集群。

   在[容器服务管理控制台](https://cs.console.aliyun.com/?spm=a2c4g.11186623.2.16.61a036c8iieu29&amp;accounttraceid=caa7f456b82e4e998e461e8bee1a3cb6xetj#/k8s/cluster/list)上创建ASK集群。更多信息，请参见[创建Serverless Kubernetes集群](/intl.zh-CN/Serverless Kubernetes集群用户指南/集群/创建Serverless Kubernetes集群.md)。
   **说明**

   如果您需要从公网拉取镜像，或者训练任务需要访问公网，请配置NAT网关。

   您可以通过kubectl管理和访问ASK集群，相关操作如下：
   * 如果您需要从本地算机管理集群，请安装并配置kubectl客户端。具体操作，请参见[通过kubectl连接Kubernetes集群](/intl.zh-CN/Serverless Kubernetes集群用户指南/集群/管理和访问集群/通过kubectl连接Kubernetes集群.md)。

     
   
   * 您也可以在CloudShell上通过kubect管理集群。具体操作，请参见[在CloudShell上通过kubectl管理Kubernetes集群](/intl.zh-CN/Serverless Kubernetes集群用户指南/集群/管理和访问集群/在CloudShell上通过kubectl管理Kubernetes集群.md)。

     
   

   

3. 创建NAS文件系统，并添加挂载点。

   在[NAS文件系统控制台](https://nasnext.console.aliyun.com/overview)上创建文件系统，并添加挂载点。更多信息，请参见[管理文件系统]()和[管理挂载点]()。
   **说明**

   NAS文件系统需和ASK集群处于同一VPC。
   




创建镜像缓存 
---------------------------

镜像缓存功能已经集成到Kubernetes CRD中，可以加速镜像的拉取。更多信息，请参见[使用镜像缓存CRD加速创建Pod]()。

操作如下：

1. 准备yaml文件。

   示例imagecache.yaml的内容如下：

       apiVersion: eci.alibabacloud.com/v1
       kind: ImageCache
       metadata:
         name: tensorflow
       spec:
         images:
         - registry-vpc.cn-beijing.aliyuncs.com/eci/tensorflow:1.0 # 训练任务的镜像，建议您上传到阿里云容器镜像仓库中。此处使用VPC私网地址，请确保对应VPC为集群所属的VPC。

   

2. 创建镜像缓存。

       kubectl create -f imagecache.yaml

   

   创建镜像缓存时需要拉取镜像，受镜像大小影响，需要一定的时间。您可以通过以下命令查询镜像缓存的创建进度。

       Kubectl get imagecache tensorflow

   

   返回如下结果时，表示镜像缓存已经创建成功。

       NAME         AGE   CACHEID                    PHASE   PROGRESS
       tensorflow   11m   imc-2ze1xczztv7tgesg****   Ready   100%

   




创建训练任务 
---------------------------

您可以使用镜像缓存来创建训练任务。

1. 准备yaml文件。

   示例gpu_pod.yaml的内容如下：

       apiVersion: v1
       kind: Pod
       metadata:
         name: tensorflow
         annotations:
           k8s.aliyun.com/eci-use-specs: "ecs.gn6i-c4g1.xlarge"  # 指定GPU规格创建ECI实例，或者您也可以指定instance type创建。例如：k8s.aliyun.com/eci-instance-type: "ecs.gn5i-c2g1.large"
           k8s.aliyun.com/eci-image-cache: "true"             # 开启镜像缓存自动匹配
       spec:
         containers:
         - name: tensorflow
           image: registry-vpc.cn-beijing.aliyuncs.com/eci/tensorflow:1.0 # 训练任务的镜像
           command:
             - "sh"
             - "-c"
             - "python models/tutorials/image/imagenet/classify_image.py" # 触发训练任务的脚本
           resources:
             limits:
               nvidia.com/gpu: "1"   # 容器所需的GPU个数
           volumeMounts:
           - name: nfs-pv
             mountPath: /tmp/imagenet
         volumes:   
         - name: nfs-pv   #训练结果持久化，保存到NAS
           flexVolume:
               driver:  alicloud/nas
               fsType: nfs
               options:
                 server: 16cde4****-ijv**.cn-beijing.nas.aliyuncs.com     #NAS文件系统挂载点
                 path: /         #挂载目录
         restartPolicy: OnFailure

   

2. 执行命令创建Pod。

       kubectl create -f gpu_pod.yaml

   

3. 查看执行情况。

   您可以根据需要查看事件或日志。
   * 查看事件

         kubectl describe pod tensorflow

     
   
   * 查看日志

         kubectl logs tensorflow

     
   

   




查看结果 
-------------------------

您可以在控制台上查看训练任务的运行结果。

* 在[NAS文件系统控制台](https://nasnext.console.aliyun.com/overview)，您可以看到训练完成的数据已占用存储容量。![nas ](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/4758242161/p238671.png)

  **说明**

  训练结果存储在配置的NAS文件系统中，挂载NAS后，您可以在对应的路径下获取结果数据。
  

* 在[弹性容器实例控制台](https://eci.console.aliyun.com)，您可以看到运行成功的ECI实例。![tensorflow](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/4758242161/p238638.png)

  **说明**

  运行成功表示实例中的容器已经运行终止，此时系统会回收底层计算资源，不再对Pod进行计费。
  



