# Predict TensorFlow models {#concept_qwx_njf_vdb .concept}

TensorFlow Serving is an open source serving system provided by Google for machine learning models. It simplifies and accelerates the process from model building to application production. TensorFlow Serving helps you deploy the trained machine-learning models online and allows you to call operations through a gRPC API. TensorFlow Serving also provides model updates and version management without any downtime. This significantly reduces the complexity of online management for model providers. Therefore, the model providers can focus on model optimization.

TensorFlow Serving is an online service, which requires you to consider issues in the following aspects: installation and configuration during deployment, load balancing, auto scaling, high availability, and rolling upgrade. Kubernetes provides rich experiences to solve these issues. You can utilize the built-in automation ability to reduce the operations and maintenance cost for TensorFlow Serving applications.

This topic describes how to use the official Kubernetes package management tool Helm to prepare models, deploy TensorFlow Serving, and scale out the containers in Alibaba Cloud Container Service.

## Prerequisites {#section_hhx_dkf_vdb .section}

Before running the model prediction tasks, make sure that the following tasks are finished:

-   Create a Kubernetes cluster that contains an appropriate number of elastic computing resources, such as ECS and Elastic GPU Service instances. For more information about how to create a Kubernetes cluster, see [Create a gn5 Kubernetes cluster](reseller.en-US/solution/Deep learning solutions/Deep learning solutions based on Helm charts/Create a gn5 Kubernetes cluster.md#).
-   If you want to use Apsara File Storage NAS to store data for model training, you must create an Apsara File Storage NAS file system. Then, use the same account to create a persistent volume \(PV\) in the Kubernetes cluster and dynamically generate a persistent volume claim \(PVC\). The PVC is mounted as a local directory to the container that runs the training tasks. For more information, see [Create an Apsara File Storage NAS data volume](reseller.en-US/solution/Deep learning solutions/Deep learning solutions based on Helm charts/Create an Apsara File Storage NAS data volume.md#).
-   Use an SSH client to connect to the master node. For more information, see [Access Kubernetes clusters by using SSH](../../../../reseller.en-US//Access a Kubernetes cluster by using SSH.md#).

## Step 1: Prepare models {#section_jhx_dkf_vdb .section}

You must prepare a persistent volume \(PV\) for TensorFlow Serving to load the prediction models. In Alibaba Cloud Container Service, you can create PVs based on an Apsara File Storage NAS file system, OSS bucket, and cloud disk. For more information, see [Overview](../../../../reseller.en-US//Overview.md#).

This section uses the Apsara File Storage NAS file system as an example to describe how to import data models.

1.  Create an Apsara File Storage NAS file system and add a mount point that is in a VPC. For more information, see the "Create a mount point" section of the [Mount a file system on an ECS instance running Windows](https://help.aliyun.com/document_detail/60431.html) topic. In this example, the mount point is`xxxxxx.cn-hangzhou.nas.aliyuncs.com`.
2.  Use an Alibaba Cloud ECS instance in the cluster to prepare model data. First, create a folder.

    ```
     mkdir /nfs
     mount -t nfs -o vers=4.0 xxxxxx.cn-hangzhou.nas.aliyuncs.com:/ /nfs
     mkdir -p /nfs/serving
     umount /nfs
    ```

3.  Download the prediction model and save it to Apsara File Storage NAS.

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

4.  Then, you can view the content of the prediction model in an intuitive way. Make sure that the data is uploaded and then `umount` the mount point for high data security.

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


## Step 2: Create a PV {#section_thx_dkf_vdb .section}

The following example uses the nas.yaml file to create an Apsara File Storage NAS file system.

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

You must set the label to `model: mnist`, which is more important for your PVC to select the PV. For more information about the Apsara File Storage NAS configuration, see [Use Alibaba Cloud Apsara File Storage NAS](https://help.aliyun.com/document_detail/63956.html).

**Create a volume in the console**

Log on to the ACK console. In the left-side navigation pane, select Persistent Volumes. On the page that appears, click Create to create a persistent volume \(PV\).

After the PV is created, you can view it on the Persistent Volumes page.

**Create a volume by running command lines**

You can also run the kubectl command to create a PV.

```
kubectl create -f nas.yaml
persistentvolume "pv-nas" created
```

## Step 3: Use Helm to deploy TensorFlow Serving applications {#section_ggl_xkf_vdb .section}

1.  Log on to the [Container Service console](https://cs.console.aliyun.com/).
2.  In the left-side navigation pane, select Container Service for Kubernetes and choose **Marketplace** \> **App Catalog** to open the corresponding page.
3.  Click **ack-tensorflow-serving** to enter the chart page.

    ![](images/9159_en-US.png)

4.  On the page, click the **Parameters** tab, configure the parameters for ack-tensorflow-serving, and click **Create**.

    ![](images/9161_en-US.png)

    The following example provides the code to deploy GPU model prediction by customizing configuration parameters.

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

    The following example provides the code to deploy non-GPU model prediction by customizing configuration parameters.

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
     image: "registry.cn-hangzhou.aliyuncs.com/tensorflow-samples/tensorflow-serving:1.4.0-devel-gpu"
     imagePullPolicy: "IfNotPresent"
     mountPath: /serving/model/mnist
     persistence: 
       mountPath: /serving/model/mnist
       pvc: 
         matchLabels: 
           model: mnist
         storage: 5Gi
    ```

    You can also connect to the Kubernetes master node and run the following command.

    ```
    helm install --values serving.yaml --name mnist incubator/acs-tensorflow-serving
    ```


## Step 4: View the deployment of the TensorFlow Serving application {#section_u3x_dkf_vdb .section}

Connect to the Kubernetes master node and run the helm command to view the deployments.

```
# helm list
NAME          REVISION  UPDATED                   STATUS    CHART                         NAMESPACE
mnist-deploy  1         Fri Mar 16 19:24:35 2018  DEPLOYED  acs-tensorflow-serving-0.1.0  default
```

To check the configuration of a specific application, run the following helm status command:

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

In this example, the name of the TensorFlow Servicing deployment is mnist-deploy-serving, the public IP address of the deployment is 139.195.1.216, and the corresponding port number is 9090. This information will be used when you scale out containers in the TensorFlow Serving deployment.

View the pod logs of the TensorFlow Serving deployment. Make sure that the mnist model is loaded into the memory and the GPU cluster is running as expected.

```
# kubectl logs mnist-deploy-serving-665fc69d84-pk9bk
```

## Step 5: Start the client for debugging {#section_d1f_bmf_vdb .section}

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

## Step 6: Scale out containers in the TensorFlow Serving deployment {#section_ejx_dkf_vdb .section}

You must use the native kubectl command to scale out the containers in the TensorFlow Serving deployment because the helm command does not provide the ability to scale out the containers. To scale out the containers and check the number of deployments, you must run the following two commands: kubectl scale and helm status.

```
# kubectl scale --replicas 2 deployment/mnist-deploy-serving
deployment "mnist-deploy-serving" scaled
```

Run the `helm status mnist-deploy` command to check the number of the TensorFlow Serving deployments. In the following example, the number is 2.

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

