---
keyword: [PyTorch, 单机训练, 共享存储系统, Arena]
---

# PyTorch单机训练

本文展示如何使用Arena提交PyTorch的单机训练作业，并通过TensorBoard可视化查看训练作业。

-   [创建包含GPU的Kubernetes集群](/cn.zh-CN/Kubernetes集群用户指南/GPU/NPU/GPU资源调度/Kubernetes GPU集群支持GPU调度.md)。
-   [集群节点可以访问公网](/cn.zh-CN/Kubernetes集群用户指南/集群/连接集群/通过公网访问集群API Server.md)。
-   [已经安装最新版的Arena](/cn.zh-CN/解决方案/AI解决方案/环境准备/通过组件安装最新版的Arena.md)。
-   已给集群配置了Arena使用的PVC，并且PVC已填充本文使用的数据集，详情请参见[配置NAS共享存储](/cn.zh-CN/解决方案/AI解决方案/环境准备/配置NAS共享存储.md)（或者[配置CPFS共享存储](/cn.zh-CN/解决方案/AI解决方案/环境准备/配置CPFS共享存储.md)）。

本文示例从Git URL下载源代码，数据集放在共享存储系统（基于NAS的PV和PVC）中。示例假设您已经获得了一个名称为**training-data**的PVC实例（一个共享存储），里面存在一个目录pytorch\_data，其存放了示例所使用的数据集。

1.  执行以下命令检查可用的GPU资源。

    ```
    arena top node
    ```

    预期输出：

    ```
    NAME                       IPADDRESS     ROLE    STATUS  GPU(Total)  GPU(Allocated)
    cn-huhehaote.192.1xx.x.xx  192.1xx.x.xx  master  ready   0           0
    cn-huhehaote.192.1xx.x.xx  192.1xx.x.xx  master  ready   0           0
    cn-huhehaote.192.1xx.x.xx  192.1xx.x.xx  master  ready   0           0
    cn-huhehaote.192.1xx.x.xx  192.1xx.x.xx  <none>  ready   2           0
    cn-huhehaote.192.1xx.x.xx  192.1xx.x.xx  <none>  ready   2           0
    cn-huhehaote.192.1xx.x.xx  192.1xx.x.xx  <none>  ready   2           0
    -----------------------------------------------------------------------------------------
    Allocated/Total GPUs In Cluster:
    0/6 (0%)
    ```

    由上看出，有3个包含GPU的节点可用于运行训练作业。

2.  执行`arena submit tfjob/tf [--flag] command`形式命令提交PyTorch作业。

    通过以下示例代码提交一个单机单卡的PyTorch作业。

    ```
      arena submit pytorch \
        --name=pytorch-git \
        --gpus=1 \
        --working-dir=/root \
        --image=registry.cn-huhehaote.aliyuncs.com/lumo/pytorch-with-tensorboard:1.5.1-cuda10.1-cudnn7-runtime \
        --sync-mode=git \
        --sync-source=https://code.aliyun.com/370272561/mnist-pytorch.git \
        --data=training-data:/mnist_data \
        --tensorboard \
        --logdir=/mnist_data/pytorch_data/logs \
        "python /root/code/mnist-pytorch/mnist.py --epochs 10 --backend nccl --dir /mnist_data/pytorch_data/logs --data /mnist_data/pytorch_data/"
    ```

    预期输出：

    ```
    configmap/pytorch-git-pytorchjob created
    configmap/pytorch-git-pytorchjob labeled
    service/pytorch-git-tensorboard created
    deployment.apps/pytorch-git-tensorboard created
    pytorchjob.kubeflow.org/pytorch-git created
    INFO[0000] The Job pytorch-git has been submitted successfully
    INFO[0000] You can run `arena get pytorch-git --type pytorchjob` to check the job status
    ```

    参数解释如下表。

    |参数|是否必选|解释|默认值|
    |--|----|--|---|
    |--name|必选|指定提交的作业名字，全局唯一，不能重复。|无|
    |--working-dir|可选|指定当前执行命令所在的目录。|/root|
    |--gpus|可选|指定作业Worker节点需要使用的GPU有卡数。|0|
    |--image|必选|指定训练环境的镜像地址。|无|
    |--sync-mode|可选|同步代码的模式，您可以指定git、rsync。本文使用git模式。|无|
    |--sync-source|可选|同步代码的仓库地址，需要和--sync-mode一起使用，本文示例使用git模式，该参数可以为任何github项目地址。阿里云code项目地址等支持git的代码托管地址。项目代码将会被下载到--working-dir下的code/目录中。本文示例即为：/root/code/mnist-pytorch。|无|
    |--data|可选|挂载共享存储卷PVC到运行环境中。它由两部分组成，通过分号`:`分割。分号左侧是您已经准备好的PVC名称。您可以通过`arena data list`查看当前集群可用的PVC列表；分号右侧是您想将PVC的挂载到运行环境中的路径，也是您训练代码要读取数据的本地路径。这样通过挂载的方式，您的代码就可以访问PVC的数据。**说明：** 执行`arena data list`查看本文示例当前集群可用的PVC列表。

    ```
NAME           ACCESSMODE     DESCRIPTION  OWNER  AGE
training-data  ReadWriteMany                      35m
    ```

如果没有可用的PVC，您可创建PVC。详情请参见[配置NAS共享存储](/cn.zh-CN/解决方案/AI解决方案/环境准备/配置NAS共享存储.md)（或者[配置CPFS共享存储](/cn.zh-CN/解决方案/AI解决方案/环境准备/配置CPFS共享存储.md)）。

|无|
    |--tensorboard|可选|为训练任务开启一个TensoBoard服务，用作数据可视化，您可以结合--logdir指定TensorBoard要读取的event路径。不指定该参数，则不开启TensorBoard服务。|无|
    |--logdir|可选|需要结合--tensorboard一起使用，该参数表示TensorBoard需要读取event数据的路径。|/training\_logs|

    **说明：** 如果您使用非公开Git代码库，则可以使用以下命令。

    ```
      arena --loglevel info submit pytorch \
            ...
            --sync-mode=git \
            --sync-source=https://code.aliyun.com/370272561/mnist-pytorch.git \
            --env=GIT_SYNC_USERNAME=yourname \
            --env=GIT_SYNC_PASSWORD=yourpwd \
            "python /root/code/mnist-pytorch/mnist.py --backend gloo"
    ```

    arena命令使用[git-sync](https://github.com/kubernetes/git-sync/blob/master/cmd/git-sync/main.go)同步源代码。您可以设置在git-sync项目中定义的环境变量。

3.  执行以下命令查看当前通过Arena提交的所有作业。

    ```
    arena list
    ```

    预期输出：

    ```
    NAME         STATUS     TRAINER     AGE  NODE
    pytorch-git  RUNNING    PYTORCHJOB  19s  192.1xx.x.xx
    tf-dist      SUCCEEDED  TFJOB       13h  N/A
    tf-git       SUCCEEDED  TFJOB       16h  N/A
    ```

4.  执行以下命令检查作业所使用的GPU资源。

    ```
    arena top job
    ```

    预期输出：

    ```
    NAME         GPU(Requests)  GPU(Allocated)  STATUS     TRAINER     AGE  NODE
    tf-dist      2              0               SUCCEEDED  tfjob       13h  N/A
    tf-git       1              0               SUCCEEDED  tfjob       16h  N/A
    pytorch-git  1              1               RUNNING    pytorchjob  25s  192.1xx.x.xx
    
    
    Total Allocated GPUs of Training Job:
    1
    
    Total Requested GPUs of Training Job:
    4
    ```

5.  执行以下命令检查集群所使用的GPU资源。

    ```
    arena top node
    ```

    预期输出：

    ```
    NAME                       IPADDRESS     ROLE    STATUS  GPU(Total)  GPU(Allocated)
    cn-huhehaote.192.1xx.x.xx  192.1xx.x.xx  master  ready   0           0
    cn-huhehaote.192.1xx.x.xx  192.1xx.x.xx  master  ready   0           0
    cn-huhehaote.192.1xx.x.xx  192.1xx.x.xx  master  ready   0           0
    cn-huhehaote.192.1xx.x.xx  192.1xx.x.xx  <none>  ready   2           0
    cn-huhehaote.192.1xx.x.xx  192.1xx.x.xx  <none>  ready   2           0
    cn-huhehaote.192.1xx.x.xx  192.1xx.x.xx  <none>  ready   2           1
    -----------------------------------------------------------------------------------------
    Allocated/Total GPUs In Cluster:
    1/6 (16%)
    ```

6.  执行以下命令获取作业详情。

    ```
    arena get pytorch-git
    ```

    预期输出：

    ```
    STATUS: SUCCEEDED
    NAMESPACE: default
    PRIORITY: N/A
    TRAINING DURATION: 2m
    
    NAME         STATUS     TRAINER     AGE  INSTANCE              NODE
    pytorch-git  SUCCEEDED  PYTORCHJOB  3m   pytorch-git-master-0  192.16x.x.xx
    
    Your tensorboard will be available on:
    http://192.16x.x.xx:30171
    ```

    **说明：** 本文示例因为开启TensorBoard，在上述作业详情中最后两行，可以看到TensorBoard的web访问地址；如果没有开启TensorBoard，最后两行信息不存在。

7.  通过浏览器查看TensorBoard。

    从上述步骤的作业详情中，可以看到TensorBoard的web服务地址。由于集群为远端部署，因此需要利用sshuttle代理才能在您的电脑中通过浏览器查看训练可视化的信息。

    使用sshuttle代理示例代码如下。

    ```
    # you can install sshuttle==0.74 in your mac with python2.7
    pip install sshuttle==0.74
    # 0/0 -> 0.0.0.0/0
    sshuttle -r root@39.104.xx.xxx  0/0
    ```

    **说明：** 39.104.xx.xxx为ACK集群对外暴露的公网IP地址。此外，您还需检查您的安全组是否开启了22端口，一般默认开启。

    将步骤6中获取的TensorBoard的web服务地址http://192.16x.x.xx:30171，拷贝至浏览器地址栏，显示效果如下图。

    ![pytorch单机](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/7704359951/p135067.png)

    **说明：** 本文PyTorch示例代码，默认每10次epoch写入event信息。如果您修改了--epochs参数，请修改为10的整数倍，否则无法在TensorBoard上看到数据。

8.  执行以下命令获取作业日志信息。

    ```
    arena logs pytorch-git
    ```

    预期输出：

    ```
    WORLD_SIZE: 1, CURRENT_RANK: 0
    args: Namespace(backend='nccl', batch_size=64, data='/mnist_data/data', dir='/mnist_data/logs', epochs=1, log_interval=10, lr=0.01, momentum=0.5, no_cuda=False, save_model=False, seed=1, test_batch_size=1000)
    Using CUDA
    ...
    Train Epoch: 10 [58240/60000 (97%)] loss=0.0128
    Train Epoch: 10 [58880/60000 (98%)] loss=0.0098
    Train Epoch: 10 [59520/60000 (99%)] loss=0.0051
    
    accuracy=0.9904
    ```

    您还可以通过命令`arena logs $job_name -f`实时查看作业的日志输出，通过命令`arena logs $job_name -t N`查看尾部N行的日志，以及通过`arena logs --help`查询更多参数使用情况。

    查看尾部N行的日志示例代码如下。

    ```
    arena logs pytorch-git -t 5
    ```

    预期输出：

    ```
    Train Epoch: 10 [58880/60000 (98%)] loss=0.0098
    Train Epoch: 10 [59520/60000 (99%)] loss=0.0051
    
    accuracy=0.9904
    ```


