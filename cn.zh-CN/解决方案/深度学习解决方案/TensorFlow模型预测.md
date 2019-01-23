# TensorFlow模型预测 {#concept_qwx_njf_vdb .concept}

TensorFlow Serving 是由 Google 开源的机器学习模型预测系统，能够简化并加速从模型到生产应用的过程。它可以将训练好的机器学习模型部署到线上，使用 gRPC 作为接口接受外部调用。更给人惊喜后的是，它还提供了不宕机的模型更新和版本管理。这大大降低了模型提供商在线上管理的复杂性，可以将注意力都放在模型优化上。

TensorFlow Serving 本质上也是一个在线服务，我们需要考虑它的部署时刻的安装配置，运行时刻的负载均衡，弹性伸缩，高可用性以及滚动升级等问题，幸运的是这正是 Kubernetes 擅长的地方。利用 Kubernetes 的内置自动化能力，将极大地降低 TensorFLow Serving 应用运维的成本。

本文将介绍如何利用 Kubernetes 的官方包管理工具 Helm 在阿里云容器服务上准备模型，部署 TensorFlow Serving，并且进行手动扩容。

## 准备工作 {#section_hhx_dkf_vdb .section}

在运行模型预测任务之前，请确认以下工作已经完成：

-   创建包含适当数量弹性计算资源（ECS 或 EGS）的 Kubernetes 集群。创建步骤请参考[创建 GN5 型Kubernetes 集群](cn.zh-CN/解决方案/深度学习解决方案/创建 GN5 型Kubernetes 集群.md#)。
-   如果您需要使用 NAS 文件系统保存用于模型训练的数据，您需要使用相同账号创建 NAS；然后在上面的 Kubernetes 集群中创建持久化数据卷（PV），动态生成 PVC 作为本地目录挂载到执行训练任务的容器内。参见[创建 NAS 数据卷](cn.zh-CN/解决方案/深度学习解决方案/创建 NAS 数据卷.md#)。
-   SSH 连接到 Master 节点连接地址，参见 [SSH访问Kubernetes集群](../../../../../cn.zh-CN/用户指南/Kubernetes 集群/集群管理/SSH访问Kubernetes集群.md#)。

## 步骤1 准备模型 {#section_jhx_dkf_vdb .section}

由于 TensorFLow Serving 需要用持久化存储加载预测模型，这里就需要准备相应的存储。在阿里云容器服务里，您可以选择 NAS、OSS和 云盘，具体可以参考[概述](../../../../../cn.zh-CN/用户指南/Kubernetes 集群/存储管理/概述.md#)。

本文以 NAS 存储为例介绍如何导入数据模型。

1.  创建 NAS 文件存储，并且设置 VPC 内挂载点。可以参考 [添加挂载点](https://help.aliyun.com/document_detail/60431.html)，查看添加挂载点，这里假设挂载点为 `xxxxxx.cn-hangzhou.nas.aliyuncs.com`。
2.  利用集群内的一台阿里云 ECS 云服务器准备模型数据，首先创建文件夹。

    ```
     mkdir /nfs
     mount -t nfs -o vers=4.0 xxxxxx.cn-hangzhou.nas.aliyuncs.com:/ /nfs
     mkdir -p /nfs/serving
     umount /nfs
    ```

3.  下载预测模型并且保存到 NAS 里。

    ```
     mkdir /serving
     mount -t nfs -o vers=4.0  xxxxxx.cn-hangzhou.nas.aliyuncs.com:/serving /serving
     mkdir -p /serving/model
     cd /serving/model
     curl -O http://tensorflow-samples.oss-cn-shenzhen.aliyuncs.com/exports/mnist-export.tar.gz
     tar -xzvf mnist-export.tar.gz
     rm -rf mnist-export.tar.gz
     cd /
    ```

4.  这样你可以就可以很直观的看到预测模型的内容，检查后可以 `umount` 挂载点。

    ```
    tree /serving/model/mnist
     /serving/model/mnist
     └── 1
         ├── saved_model.pb
         └── variables
             ├── variables.data-00000-of-00001
             └── variables.index
     umount /serving
    ```


## 步骤2 创建持久化数据卷 {#section_thx_dkf_vdb .section}

以下为创建 NAS 的 nas.yaml 样例。

```
apiVersion: v1
kind: PersistentVolume
metadata: 
  labels: 
    model: mnist
  name: pv-nas
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
      path: /serving/model/mnist
      server: xxxxxx.cn-hangzhou.nas.aliyuncs.com
      vers: "4.0"
```

注意这里需要指定 label 为 `model: mnist`，该标签对于 pvc 选择 pv 绑定非常重要。另外和 NAS 相关的具体配置可以参考 [使用阿里云NAS](https://help.aliyun.com/document_detail/63956.html)。

**通过控制台创建数据卷**

在 Kubernetes Dashboard，选择持久化存储卷，进行创建。

稍等片刻后，可以看到持久化存储卷已经创建成功。

**通过命令行创建数据卷**

当然也可以运行 kubectl 命令创建。

```
kubectl create -f nas.yaml
persistentvolume "pv-nas" created
```

## 步骤3 通过 Helm 部署 TensorFlow Serving 应用 {#section_ggl_xkf_vdb .section}

1.  登录 [容器服务控制台](https://cs.console.aliyun.com/)。
2.  在 Kubernetes 菜单下，单击左侧导航栏中的 **市场** \> **应用目录**，进入应用目录列表。
3.  单击 **acs-tensorflow-serving**，进入对应的 chart 页面。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16634/15482175089159_zh-CN.png)

4.  单击 **参数**，对 acs-tensorflow-serving 的参数进行配置，最后单击 **创建**。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16634/15482175089161_zh-CN.png)

    创建支持 GPU 的自定义配置参数：

    ```
     serviceType: LoadBalancer
     ## expose the service to the grpc client
     port: 9090
     replicas: 1
     image: "registry.cn-hangzhou.aliyuncs.com/tensorflow-samples/tensorflow-serving:1.4.0-devel-gpu"
     imagePullPolicy: "IfNotPresent"
     ## the gpu resource to claim, for cpu, change it to 0
     gpuCount: 1
     ## The command and args to run the pod
     command: ["/usr/bin/tensorflow_model_server"]
     args: [ "--port=9090", "--model_name=mnist", "--model_base_path=/serving/model/mnist"]  
     ## the mount path inside the container
     mountPath: /serving/model/mnist
     persistence:
     ## The request and label to select the persistent volume
        pvc:
           storage: 5Gi
           matchLabels: 
             model: mnist
    ```

    创建支持非 GPU 的自定义配置参数：

    ```
    serviceType: LoadBalancer
     ## expose the service to the grpc client
     port: 9090
     replicas: 1
     command: 
       - /usr/bin/tensorflow_model_server
     args: 
       - "--port=9090"
       - "--model_name=mnist"
       - "--model_base_path=/serving/model/mnist"
     image: "registry.cn-hangzhou.aliyuncs.com/tensorflow-samples/tensorflow-serving:1.4.0-devel"
     imagePullPolicy: "IfNotPresent"
     mountPath: /serving/model/mnist
     persistence: 
       mountPath: /serving/model/mnist
       pvc: 
         matchLabels: 
           model: mnist
         storage: 5Gi
    ```

    也可以登录到 Kubernetes master 运行以下命令。

    ```
    helm install --values serving.yaml --name mnist incubator/acs-tensorflow-serving
    ```


## 步骤4 查看TensorFlow-serving的应用部署 {#section_u3x_dkf_vdb .section}

登录到 Kubernetes 的 master 上利用 helm 命令查看部署应用的列表。

```
# helm list
NAME          REVISION  UPDATED                   STATUS    CHART                         NAMESPACE
mnist-deploy  1         Fri Mar 16 19:24:35 2018  DEPLOYED  acs-tensorflow-serving-0.1.0  default
```

利用 helm status 命令检查具体应用的配置。

```
# helm status mnist-deploy
LAST DEPLOYED: Fri Mar 16 19:24:35 2018
NAMESPACE: default
STATUS: DEPLOYED
RESOURCES:
==> v1/Service
NAME                                 TYPE          CLUSTER-IP    EXTERNAL-IP    PORT(S)         AGE
mnist-deploy-acs-tensorflow-serving  LoadBalancer  172.19.0.219  139.195.1.216  9090:32560/TCP  5h
==> v1beta1/Deployment
NAME                  DESIRED  CURRENT  UP-TO-DATE  AVAILABLE  AGE
mnist-deploy-serving  1        1        1           1          5h
==> v1/Pod(related)
NAME                                   READY  STATUS   RESTARTS  AGE
mnist-deploy-serving-665fc69d84-pk9bk  1/1    Running  0         5h
```

TensoFlow Serving 的对外服务地址是 EXTERNAL-IP: 139.195.1.216，端口为 9090 对应部署的 deployment 是 mnist-deploy-serving，这个信息在扩容时刻是需要的。

查看 tensorflow-serving 的下 pod 的日志，发现 mnist 的模型已经加载到内存里，并且 GPU 已经正常启动。

```
# kubectl logs mnist-deploy-serving-665fc69d84-pk9bk
```

## 步骤5 启动客户端进行调试 {#section_d1f_bmf_vdb .section}

```
# docker run -it --rm registry.cn-beijing.aliyuncs.com/tensorflow-samples/tf-mnist:grpcio_upgraded /serving/bazel-bin/tensorflow_serving/example/mnist_client --num_tests=1000 --server=139.195.1.216:9090
Successfully downloaded train-images-idx3-ubyte.gz 9912422 bytes.
Extracting /tmp/train-images-idx3-ubyte.gz
Successfully downloaded train-labels-idx1-ubyte.gz 28881 bytes.
Extracting /tmp/train-labels-idx1-ubyte.gz
Successfully downloaded t10k-images-idx3-ubyte.gz 1648877 bytes.
Extracting /tmp/t10k-images-idx3-ubyte.gz
Successfully downloaded t10k-labels-idx1-ubyte.gz 4542 bytes.
Extracting /tmp/t10k-labels-idx1-ubyte.gz
...............
Inference error rate: 10.4%
```

## 步骤6 扩容TensoFlow Serving {#section_ejx_dkf_vdb .section}

因为 helm 命令无法实现扩容的能力，这里需要使用 kubectl 原生命令。输入的命令有两个：一个是扩容目标2，另一个是通过 helm status 查询 Deployment 数量。

```
# kubectl scale --replicas 2 deployment/mnist-deploy-serving
deployment "mnist-deploy-serving" scaled
```

通过 `helm status mnist-deploy` 查询到目前的 TensoFlow Serving 实例数为 2。

```
# helm status mnist-deploy
LAST DEPLOYED: Fri Mar 16 19:24:35 2018
NAMESPACE: default
STATUS: DEPLOYED
RESOURCES:
==> v1/Service
NAME                                 TYPE          CLUSTER-IP    EXTERNAL-IP    PORT(S)         AGE
mnist-deploy-acs-tensorflow-serving  LoadBalancer  172.19.0.219  139.196.1.217  9090:32560/TCP  5h
==> v1beta1/Deployment
NAME                  DESIRED  CURRENT  UP-TO-DATE  AVAILABLE  AGE
mnist-deploy-serving  2        2        2           2          5h
==> v1/Pod(related)
NAME                                   READY  STATUS   RESTARTS  AGE
mnist-deploy-serving-665fc69d84-7sfvn  1/1    Running  0         9m
mnist-deploy-serving-665fc69d84-pk9bk  1/1    Running  0         5h
```

