# TensorFlow模型开发 {#concept_imm_1z2_vdb .concept}

TensorFLow 是深度学习和机器学习最流行的开源框架，它最初是由 Google 研究团队开发的并致力于解决深度神经网络的机器学习研究，从2015年开源到现在得到了广泛的应用。特别是 Tensorboard 这一利器，对于数据科学家的工作非常有用。

Jupyter notebook 是强大的数据分析工具，它能够帮助快速开发并且实现机器学习代码的共享，是数据科学团队用来做数据实验和组内合作的利器，也是机器学习初学者入门这一个领域的好起点。

利用 Jupyter 开发 TensorFLow 是许多数据科学家的首选，但是如何能够快速从零搭建一套这样的环境，并且配置 GPU 的使用，同时支持最新的 TensorFLow 版本，对于数据科学家来说既复杂，同时也浪费精力。在阿里云的 Kubernetes 集群上，您可以通过简单的 Web 界面，一键式创建一套完整的 tensorFlow 实验环境，包括 Jupyter Notebook开发模型，利用 Tensorboard 调整模型。

## 准备Kubernetes环境 {#section_uln_jz2_vdb .section}

阿里云容器服务Kubernetes 1.9.3目前已经上线，但是购买按量付费的GPU计算型服务器需要申请ECS工单开通。创建包含适当数量弹性计算资源（GPU 服务器）的 Kubernetes 集群，可以参见[创建 GN5 型Kubernetes 集群](intl.zh-CN/解决方案/深度学习解决方案/基于helm chart的深度学习解决方案/创建 GN5 型Kubernetes 集群.md#)。

需要登录 Master节点，执行相关命令，参见[SSH访问Kubernetes集群](../../../../../intl.zh-CN/用户指南/Kubernetes集群/集群管理/SSH访问Kubernetes集群.md#)。

**准备数据**

1.  创建NAS文件存储，并且设置vpc内挂载点。可以参考[管理文件系统](https://www.alibabacloud.com/help/zh/doc-detail/27530.htm)。并且查看挂载点，这里假设挂载点为`xxxxxx.cn-shanghai.nas.aliyuncs.com`
2.  准备名字为/data的数据文件夹。

    ```
    mkdir /nfs
     mount -t nfs -o vers=4.0 xxxxxx.cn-hangzhou.nas.aliyuncs.com:/ /nfs
     mkdir -p /nfs/data
     umount /nfs
    ```


**创建 persistent volume**

以下为创建NAS的nas.yaml样例，实际上也可以创建云盘或者OSS等持久化存储

```
apiVersion: v1
kind: PersistentVolume
metadata:
  labels:
    train: mnist
  name: pv-nas-train
spec:
  persistentVolumeReclaimPolicy: Retain
  accessModes:
    - ReadWriteMany
  capacity:
    storage: 5Gi
  flexVolume:
    driver: alicloud/nas
    options:
      mode: "755"
      path: /data
      server: xxxxxx.cn-hangzhou.nas.aliyuncs.com
      vers: "4.0"
```

**说明：** 这里需要指定label为`model: mnist`，该标签对于 pvc 选择 pv 绑定非常重要。另外和NAS相关的具体配置可以参考[使用阿里云 NAS](../../../../../intl.zh-CN/用户指南/Kubernetes集群/存储管理/使用阿里云 NAS.md#)。

运行kubectl命令创建。

```
kubectl create -f nas.yaml
persistentvolume "pv-nas" created
```

## 通过应用目录部署 TensorFlow 应用 {#section_umg_pz2_vdb .section}

1.  登录 [容器服务控制台](https://cs.console.aliyun.com/)。
2.  在 Kubernetes 菜单下，单击左侧导航栏中的**市场** \> **应用目录**，进入应用目录列表。
3.  单击 **ack-tensorflow-dev**，进入对应的 chart 页面。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16632/15534935559108_zh-CN.png)

4.  单击**参数**，就可以通过 Web 界面配置参数，然后单击 **部署**，最后单击 **Kubernetes 控制台**。

    **说明：** 默认的配置下，是使用 CPU 进行计算。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16632/15534935559109_zh-CN.png)

    本示例使用 GPU 节点进行 tensorflow 开发，具体的配置如下：

    ```
    jupyter: 
       image: 
         pullPolicy: Always
         repository: registry.cn-hangzhou.aliyuncs.com/tensorflow-samples/jupyter
         tag: 1.5.0-devel-gpu
       password: tensorflow
       resources: 
         limits: 
           nvidia.com/gpu: 1
         requests: 
           nvidia.com/gpu: 1
     service: 
       type: LoadBalancer
     tensorboard: 
       image: 
         pullPolicy: Always
         repository: registry.cn-hangzhou.aliyuncs.com/tensorflow-samples/tensorboard
         tag: "1.1.0"
     persistence:
       enabled: true
       mountPath: /data
       pvc:
         matchLabels:
            train: mnist
         storage: 5Gi
    ```

    您也可以登录到 Kubernetes master 节点，运行以下命令进行部署。

    ```
    $ helm install --name ack-tensorflow-dev-default incubator/ack-tensorflow
    ```

5.  进入 Kubernetes Dashboard 页面，查看 tensorflow 应用启动的状态。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16632/15534935559110_zh-CN.png)

    至此，ack-tensorflow-dev 应用成功运行。


## 使用 TensorFlow 实验环境 {#section_qfs_p1f_vdb .section}

1.  首先通过 ssh 登录 Kubernetes 集群，查看 tensorflow 应用列表。

    ```
    $ helm list
    NAME                            REVISION        UPDATED                         STATUS          CHART                           NAMESPACE
    ack-tensorflow-dev-default      1               Tue Apr 17 16:05:31 2018        DEPLOYED        ack-tensorflow-dev-0.1.0        default
    ```

2.  利用 helm status 命令检查应用配置。

    ```
     $ helm status ack-tensorflow-dev-default
     LAST DEPLOYED: Tue Apr 17 16:05:31 2018
     NAMESPACE: default
     STATUS: DEPLOYED
     RESOURCES:
     ==> v1/Service
     NAME                        TYPE          CLUSTER-IP    EXTERNAL-IP    PORT(S)                      AGE
     ack-tensorflow-dev-default  LoadBalancer  172.21.7.225  120.55.137.46  6006:32203/TCP,80:30795/TCP  7m
     ==> v1beta2/Deployment
     NAME                        DESIRED  CURRENT  UP-TO-DATE  AVAILABLE  AGE
     ack-tensorflow-dev-default  1        1        1           1          7m
     NOTES:
     1. Get the application URL by running these commands:
          NOTE: It may take a few minutes for the LoadBalancer IP to be available.
                You can watch the status of by running 'kubectl get svc -w ack-tensorflow-dev-default'
       export SERVICE_IP=$(kubectl get svc --namespace default ack-tensorflow-dev-default -o jsonpath='{.status.loadBalancer.ingress[0].ip}')
       echo http://$SERVICE_IP:
    ```

    这里可以看到外部 SLB 的 IP 是 120.55.137.46， Jupyter Notebook 的端口为 80，Tensorboard 为 6006。

3.  通过 Jupyter 访问端点登录，本示例中 Jupyter 的访问地址是 http://120.55.137.46，输入前面设定的密码，单击 **Log in**， 在本示例中我们设定的是 tensorflow。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16632/15534935569111_zh-CN.png)

4.  单击 **New** \> **Terminal**，进入 Terminal 页面。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16632/15534935569112_zh-CN.png)

5.  在 bash 执行环境下，在 Terminal 内执行 nvidia-smi，可以看到 GPU 的配置。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16632/15534935569113_zh-CN.png)

6.  通过 git 命令下载tensorflow样例代码。

    ```
    $ git clone https://code.aliyun.com/kubernetes/Tensorflow-Examples.git
     Clone into  "Tensorflow-Examples"...
     remote: Counting objects: 885, done.
     remote: Total 885 (delta 532), reused 885 (delta 532)
     Receiving objects: 100% (885/885), 17.89 MiB | 0 bytes/s, done.
     Resolving deltas: 100% (532/532), done.
     Checking connectivity... done.
     root@ack-tensorflow-dev-default-84c7
    ```

7.  查看mount进来的/data路径。

    ```
    ls /data
    ```

8.  返回 Jupyter 主页您就能看到 Tensorflow-Examples 已经下载到您的工作目录。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16632/15534935569114_zh-CN.png)

9.  进入 /Tensorflow-Examples/notebooks/4\_Utils/tensorboard\_basic.ipynb 目录，运行程序。

    **说明：** 如果您需要用Tensorboard观测训练效果，请将日志记录到/output/training\_logs下。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16632/15534935569116_zh-CN.png)

10. 以下为训练结果输出。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16632/15534935569117_zh-CN.png)

11. 这时您可以登录 Tensorboard 查看训练效果， 本示例中Tensorboard的地址为 http://120.55.137.46:6006。这里您可以看到模型的定义和训练的收敛趋势。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16632/15534935569118_zh-CN.png)

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16632/15534935569119_zh-CN.png)


