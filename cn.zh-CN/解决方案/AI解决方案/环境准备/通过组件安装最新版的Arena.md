---
keyword: [Arena, 机器学习, 轻量级解决方案]
---

# 通过组件安装最新版的Arena

Arena是基于Kubernetes的机器学习轻量级解决方案，支持数据准备，模型开发，模型训练，模型预测的完整生命周期，提升数据科学家工作效率。同时和阿里云的基础云服务深度集成，支持GPU共享、CPFS等服务，可以运行阿里云优化的深度学习框架，最大化使用阿里云异构设备的性能和成本的效益。

-   创建包含GPU的Kubernetes集群。具体操作，请参见[创建托管GPU集群](/cn.zh-CN/Kubernetes集群用户指南/GPU/NPU/创建异构计算集群/创建托管GPU集群.md)或[创建专有GPU集群](/cn.zh-CN/Kubernetes集群用户指南/GPU/NPU/创建异构计算集群/创建专有GPU集群.md)。
-   集群节点可以访问公网。

## 步骤一：安装Arena组件

1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。

2.  在控制台左侧导航栏中，单击**集群**。

3.  在集群列表页面，选择目标集群，并在目标集群右侧**操作**列下，选择**更多** \> **系统组件管理**。

4.  找到组件**ack-arena**，单击**安装**。


## 步骤二：配置Arena客户端

如果您使用的是专有版集群，以SSH方式登录到专有版集群的管理节点上，然后直接执行arena命令。有关使用SSH方式登录集群具体步骤，请参见[通过SSH访问Kubernetes集群](/cn.zh-CN/Kubernetes集群用户指南/集群/连接集群/通过SSH访问Kubernetes集群.md)。

如果您使用的是托管版集群，需使用kubectl命令行工具先连接集群，以确保kubeconfig文件在机器上的正确位置是$HOME/.kube/config，具体步骤请参见[通过kubectl连接Kubernetes集群](/cn.zh-CN/Kubernetes集群用户指南/集群/连接集群/通过kubectl连接Kubernetes集群.md)。然后配置Arena客户端，具体操作步骤如下。

**说明：** 您可以通过执行命令`kubectl get nodes`判断kubeconfig文件是否正确配置。

1.  下载Arena客户端。

    -   [Linux版](http://kubeflow.oss-cn-beijing.aliyuncs.com/arena-installer-0.5.0-e22162d-linux-amd64.tar.gz)
    -   [Mac版](http://kubeflow.oss-cn-beijing.aliyuncs.com/arena-installer-0.5.0-e22162d-darwin-amd64.tar.gz)
2.  解压安装包。

    -   对于Linux版系统，执行以下命令解压安装包。

        ```
        tar -xvf arena-installer-0.5.0-e22162d-linux-amd64.tar.gz
        ```

    -   对于Mac版系统，执行以下命令解压安装包。

        ```
        tar -xvf arena-installer-0.5.0-e22162d-darwin-amd64.tar.gz
        ```

3.  执行以下命令安装Arena。

    ```
    cd arena-installer
    ./install.sh
    ```

4.  安装自动补齐软件。

    安装自动补齐软件支持命令参数自动提示功能，帮助您避免记忆命令行。

    -   执行以下命令安装CentOS或Alibaba Cloud Linux 2。

        ```
        yum install bash-completion -y
        ```

    -   执行以下命令安装Debian或Ubuntu。

        ```
         apt-get install bash-completion
        ```

    -   执行以下命令安装MacOS。

        ```
        brew install bash-completion@2
        ```

5.  执行以下命令在profile文件中增加自动补齐功能。

    -   Linux

        ```
        echo "source <(arena completion bash)" >> ~/.bashrc
        chmod u+x ~/.bashrc
        ```

    -   MacOS

        ```
        echo "source $(brew --prefix)/etc/profile.d/bash_completion.sh" >> ~/.bashrc
        ```

    在命令行终端通过**Tab**键即可自动补齐命令。


## 步骤三：验证Arena

您可以执行以下步骤验证Arena是否正常工作。

1.  执行以下命令检查集群的可用GPU资源。

    ```
    arena top node
    ```

    看到输出为节点和GPU卡的信息，代表输出正确。

    ```
    NAME                        IPADDRESS      ROLE    STATUS  GPU(Total)  GPU(Allocated)
    cn-huhehaote.192.1xx.x.xx7  192.1xx.x.xx7  <none>  ready   8           0
    cn-huhehaote.192.1xx.x.xx8  192.168.0.118  <none>  ready   8           0
    cn-huhehaote.192.1xx.x.xx9  192.168.0.119  <none>  ready   8           0
    cn-huhehaote.192.1xx.x.xx0  192.168.0.120  <none>  ready   8           0
    -----------------------------------------------------------------------------------------
    Allocated/Total GPUs In Cluster:
    0/32 (0%)
    ```

2.  通过arena提交一个训练作业， 看到任务被成功提交。

    ```
    arena submit tf \
          --name=firstjob \
          --gpus=1 \
          --image=registry.cn-hangzhou.aliyuncs.com/tensorflow-samples/tf-mnist-standalone:gpu \
          "python /app/main.py"
    ```

    预期输出：

    ```
    configmap/firstjob-tfjob created
    configmap/firstjob-tfjob labeled
    tfjob.kubeflow.org/firstjob created
    INFO[0001] The Job firstjob has been submitted successfully
    INFO[0001] You can run `arena get firstjob --type tfjob` to check the job status
    ```

3.  执行`arena list`命令列出所有作业。

    输出如下。

    ```
    NAME      STATUS   TRAINER  AGE  NODE
    firstjob  RUNNING  TFJOB    5s   192.1xx.x.xxx
    ```

4.  执行以下命令查看提交作业的状态。

    ```
    arena get firstjob
    ```

    预期输出：

    ```
    STATUS: SUCCEEDED
    NAMESPACE: default
    PRIORITY: N/A
    TRAINING DURATION: 52s
    NAME      STATUS     TRAINER  AGE  INSTANCE          NODE
    firstjob  SUCCEEDED  TFJOB    14m  firstjob-chief-0  192.168.0.118
    ```

5.  执行以下命令查看作业日志。

    ```
    arena logs --tail=10 firstjob
    ```

    预期输出：

    ```
    Accuracy at step 910: 0.9694
    Accuracy at step 920: 0.9687
    Accuracy at step 930: 0.9676
    Accuracy at step 940: 0.9678
    Accuracy at step 950: 0.9704
    Accuracy at step 960: 0.9692
    Accuracy at step 970: 0.9721
    Accuracy at step 980: 0.9696
    Accuracy at step 990: 0.9675
    Adding run metadata for 999
    ```


