TensorFlow应用（GPU) 
======================================

人工智能与机器学习已经被广泛应用到各个领域，近些年来各种各样的训练模型被提出，更多的训练任务跑到了云上。然而上云之后，想要轻松、持久地运行训练任务，仍有一些痛点，例如：

* 环境搭建：例如如果需要GPU实例，你必须首先购买GPU实例并安装GPU驱动，即便你把训练任务[容器化](https://hub.docker.com/r/tensorflow/tensorflow/?spm=ata.13261165.0.0.4e0c9e6eiEsp0z)后，还需要安装 [gpu runtime hook](https://github.com/NVIDIA/nvidia-docker?spm=ata.13261165.0.0.4e0c9e6eiEsp0z) ，使用ASK + ECI，这些都不需要你操心。

  

* 缺乏弹性：环境准备好，任务跑完后，为了节约成本，也许你需要释放闲置资源，下次启动任务时再重新创建实例配置环境，或者是计算节点不够，需要扩容，同样需要再次配置环境，使用ASK+ECI，能真正做到让你根据自己的业务按需使用，按需付费。

  




本文将会介绍如何使用阿里云Serverless Kubernetes(ASK) + ECI，快速完成一个github上基于GPU的 [TensorFlow 训练任务](https://github.com/tensorflow/models?spm=ata.13261165.0.0.4e0c9e6eiEsp0z)。

具体步骤 
-------------------------

创建以及操作集群的详细步骤参见[容器服务（Serverless Kubernetes）使用ECI]()。

* 前往[容器服务控制台](https://cs.console.aliyun.com/)创建 Serverless Kubernetes集群，指定地域、vpc、vSwitch（如果你要走公网拉取镜像，或者训练任务需要出公网，请配置NAT网关）。

  ![](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/4554613061/p174772.png)

  

* 集群创建完成后，单击 **管理** ，按照提示完成 kubectl 的安装与配置。

  

* 为了加速 GPU 镜像的拉取，ECI 提供了 ImageCache 功能，并且通过virtual kubelet无缝集成到Kubernetes CRD 中。第一次需要拉取镜像，时间取决于镜像大小，请耐心等待。

  * 创建 ImageCache：`kubectl create -f imagecache.yaml`。

    
  
  * imagecache.yaml 内容如下：

        apiVersion: eci.alibabacloud.com/v1
        kind: ImageCache
        metadata:
        name: tensorflow
        spec:
        images:
        - registry-vpc.cn-zhangjiakou.aliyuncs.com/eci/tensorflow:1.0 # 训练任务的镜像，建议放到阿里云vpc私网仓库                                        

    
  
  * 可以通过命令查询进度：`kubectl get imagecache tensorflow`。

    
  

  

* 使用镜像缓存，创建训练任务：`kubectl create -f gpu_pod.yaml`。

  * gpu_pod.yaml 内容如下：

    
  

  

  

      apiVersion: v1
      kind: Pod
      metadata:
        name: tensorflow
        annotations:
          k8s.aliyun.com/eci-use-specs: "ecs.gn6i-c4g1.xlarge"  # GPU规格，或者你可以指定和ECS一样的instanceType创建。例如：k8s.aliyun.com/eci-instance-type: "ecs.gn5i-c2g1.large"
          k8s.aliyun.com/eci-image-cache: "true"  # 开启镜像缓存自动匹配
      spec:
        containers:
          image: registry-vpc.cn-zhangjiakou.aliyuncs.com/eci/tensorflow:1.0 # 训练任务的镜像
          name: tensorflow
          command:
            - "sh"
            - "-c"
            - "python models/tutorials/image/imagenet/classify_image.py" # 触发训练任务的脚本
          resources:
            limits:
              nvidia.com/gpu: "1"  # 容器所需的GPU个数
          volumeMounts:
            name: nfs-pv
            mountPath: /tmp/imagenet
        volumes:
          name: nfs-pv  # 训练结果持久化到NAS文件存储
          nfs:
            path: /share
            server: 0912430d-1nsl.cn-zhangjiakou.extreme.nas.aliyuncs.com
        restartPolicy: OnFailure

  

  * 使用以下命令查询Pod事件信息和日志。
    

    * kubectl describe pod tensorflow

      
    
    * kubectl logs tensorflow ![](http://docs-aliyun.cn-hangzhou.oss.aliyun-inc.com/assets/pic/145513/cn_zh/1575260820057/logs.png)

      
    

    
  
  * NAS存储里已经有训练完成的数据。![](http://docs-aliyun.cn-hangzhou.oss.aliyun-inc.com/assets/pic/145513/cn_zh/1575260858201/nas.png)

    
  

  

  

* 在 ECI 控制台可以看到运行成功的实例，运行成功表示实例已经到达终态，此时ECI会回收底层计算资源，不再对该Pod进行计费。![](http://docs-aliyun.cn-hangzhou.oss.aliyun-inc.com/assets/pic/145513/cn_zh/1575260950593/eci.png)

  




总结 
-----------------------

使用ASK + ECI，只需要以下几步，就可以轻松启动一个基于GPU实例的训练任务：

1. 准备好训练模型的容器镜像，准备好训练数据、结果的路径。

   

2. 创建ASK集群。

   

3. 准备好3个YAML文件，在集群中依次创建。

   




同时使用此方案的优势：

1. 按需付费，免运维。

   

2. 一次配置，无限次复用。

   

3. 镜像缓存给你带来极致弹性的体验。

   

4. 数据与训练模型解耦，训练数据持久化（ECI同时支持阿里云云盘）。

   



