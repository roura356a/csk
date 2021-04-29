---
keyword: [NAS共享存储, Arena, 分布式存储, 配置PV和PVC]
---

# 配置NAS共享存储

阿里云文件存储NAS（Network Attached Storage）是面向阿里云ECS实例、E-HPC和ACK等计算节点的文件存储服务。阿里云NAS服务具有无缝集成，共享访问，安全控制等特性，非常适合跨多个ECS、E-HPC或ACK实例部署的应用程序访问相同数据来源的应用场景。文本介绍如何配置NAS共享存储。

为了保留数据科学家的工作内容，或者读取同一份训练数据。建议您配置共享存储卷，并挂载到Arena提交作业的运行环境中，确保数据科学家的工作内容（代码、数据）得以保留，不会随着容器删除而丢失。

在团队开发中，建议分配一个共享的存储池，让数据和代码能够在团队中共享。在Arena提交作业使用--data参数时，如果您声明了配置共享存储，以及要挂载到运行环境的路径，共享存储将会被挂载到您指定的目录中，这样提交作业就可以复用这部分数据或者代码。

在Kubernetes中，通过存储卷（PV）和存储声明（PVC）描述存储对象。作为集群管理员，分配环境时您需要为每个数据科学家创建属于自己的存储声明。例如用户A和用户B，存储声明的后端可以挂载到相同的NAS或者CPFS，但是必须指定不同的子目录，保证他们工作环境隔离。

## 步骤一：创建NAS实例

有关创建NAS实例的具体操作步骤，请参见[创建通用型NAS文件系统]()。

**说明：**

创建NAS实例的参数配置说明如下：

-   文件系统类型设置为**通用型**。
-   地域设置为和ACK集群对应的地域。
-   协议类型设置为**NFS**。

## 步骤二：配置挂载点

有关配置挂载点的具体操作步骤，请参见[添加挂载点]()。

**说明：**

添加挂载点的参数配置说明如下：

-   挂载点类型设置为**专有网络**。
-   VPC网络和交换机设置为和ACK集群一致的VPC和交换机。

创建成功后，在**挂载点**页面，悬浮鼠标至![挂载点](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/3704359951/p134346.png)，查看NAS实例的挂载地址。

![挂载点信息](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/3704359951/p134349.png)

## 步骤三：配置ACK集群的存储卷（PV）和存储声明（PVC）

1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。

2.  创建存储卷（PV）。

    创建存储卷的具体步骤请参见[创建PV](/cn.zh-CN/Kubernetes集群用户指南/存储-CSI/NAS存储卷/使用NAS静态存储卷.md)。

    **说明：** 创建存储卷配置参数时，设置**挂载点域名**为**选择挂载点**，并选择NAS的挂载点地址。

3.  创建存储声明（PVC）。

    创建存储声明的具体步骤请参见[创建PVC](/cn.zh-CN/Kubernetes集群用户指南/存储-CSI/NAS存储卷/使用NAS静态存储卷.md)。

    完成创建后，在存储声明 \(PVC\) 的列表中，可以看到刚刚创建的存储声明 \(PVC\) 实例。


## 步骤四：给PVC填充数据

因为Kubernetes集群通过PVC访问各种共享数据（也就是本文在第一步中创建的NAS实例），所以您仅需要给PVC实例对应的NAS实例上填充数据即可。

1.  执行以下命令登录ACK任意一台ECS节点。

    ```
    ssh root@39.10x.xx.xx
    ```

    **说明：** ECS节点VPC网络需和创建的NAS实例VPC一致。

2.  在命令行终端，粘贴并执行NAS挂载命令。

    NAS挂载命令表示将NAS实例挂载到当前登录的ECS节点下的/mnt路径中。

    ![挂载命令](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/3704359951/p134539.png)

    ```
    sudo mount -t nfs -o xxxx.nas.aliyuncs.com:/ /mnt
    ```

3.  执行以下命令，通过挂载的这个目录为NAS实例创建两个目录tf\_data/和pytorch\_data/，分别存放TF mnist和Pytorch mnist的训练数据。

    ```
    cd /mnt/
    mkdir tf_data/
    mkdir pytorch_data/
    ```

4.  执行以下命令下载TF mnist的数据集。

    ```
    cd tf_data
    git clone https://code.aliyun.com/xiaozhou/tensorflow-sample-code.git
    mv tensorflow-sample-code/data/* ./ && rm -rf tensorflow-sample-code
    ```

5.  执行以下命令下载Pytorch mnist的数据集。

    ```
    cd pytorch_data
    git clone https://code.aliyun.com/370272561/mnist-pytorch.git
    mv mnist-pytorch/MNIST ./ && rm -rf mnist-pytorch
    ```


