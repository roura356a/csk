# Train TensorFlow distributed models {#concept_vzm_q2f_vdb .concept}

TensorFlow is the most popular deep learning framework in the industry. However, it is not easy to apply TensorFlow to the production environment. When you use TensorFlow, you may face the following challenges: resource isolation, application scheduling and deployment, GPU resource allocation, and training lifecycle management. In a large-scale distributed training scenario, managing TensorFlow by manual deployment and human O&M cannot meet the efficiency requirements. Especially you must specify the cluster specification of a distributed cluster to start each module.

You can train and evaluate TensorFlow models in Kubernetes clusters by using the Kubernetes capabilities of application scheduling, GPU resource allocation, and shared storage. These capabilities can help you schedule worker jobs and parameter server \(ps\) jobs, and manage the lifecycle. You can also view the rate of convergence by using shared storage and then perform the hyperparameter tuning.

However, configuring the parameters in a YAML file is too complicated for the end user. Alibaba Cloud Container Service offers an easy solution based on Helm to train TensorFlow distributed models.

-   Supports both GPU and non-GPU clusters.
-   You do not need to manually specify the cluster specification. Instead, you only need to specify the number of worker jobs and ps jobs, and the cluster specification is automatically generated.
-   The built-in TensorBoard can effectively monitor the convergence of training and facilitate the adjustment of the epoch, batchsize, and learning rate parameters.

This topic provides an example of end-to-end distributed model training by using Helm.

## Prerequisites {#section_epp_v2f_vdb .section}

Before running the model training task, make sure that the following tasks are completed:

-   Create a Kubernetes cluster that contains an appropriate number of elastic computing resources, such as ECS and Elastic GPU Service instances. For more information about how to create a Kubernetes cluster, see [Create a gn5 Kubernetes cluster](reseller.en-US/solution/Deep learning solutions/Deep learning solutions based on Helm charts/Create a gn5 Kubernetes cluster.md#).
-   If you want to use the Apsara File Storage NAS file system to store data for model training, you must create an Apsara File Storage NAS file system. Then, use the same account to create a persistent volume \(PV\) in the Kubernetes cluster and dynamically generate a persistent volume claim \(PVC\). The PVC is mounted as a local directory to the container that runs the training tasks. For more information, see [Create an Apsara File Storage NAS data volume](reseller.en-US/solution/Deep learning solutions/Deep learning solutions based on Helm charts/Create an Apsara File Storage NAS data volume.md#).
-   Use an SSH client to connect to the master node. For more information, see [Access Kubernetes clusters by using SSH](../../../../reseller.en-US//Access a Kubernetes cluster by using SSH.md#).

## Step 1: Mount data {#section_wgx_z2f_vdb .section}

1.  Add a mount point with the VPC type to the Apsara File Storage NAS file system that has been created. For more information, see [Create an Apsara File Storage NAS data volume](reseller.en-US/solution/Deep learning solutions/Deep learning solutions based on Helm charts/Create an Apsara File Storage NAS data volume.md#). In this example, the mount point is `xxxxxxxx.cn-hangzhou.nas.aliyuncs.com`.
2.  Configure a data directory named /data.

    ```
     mkdir /nfs
     mount -t nfs -o vers=4.0 xxxxxxxx.cn-hangzhou.nas.aliyuncs.com:/ /nfs
     mkdir -p /nfs/data
     umount /nfs
    ```


## Step 2: Create a PV {#section_vsh_pff_vdb .section}

1.  The following section is an example of nas.yaml to create a PV of Apsara File Storage NAS. You can also create PVs based on cloud disks or OSS buckets. For more information, see Create a Kubernetes cluster.

    You must set the label to `train:mnist`, which is used to attach the PV to your pods through the PVC. For more information about other configurations related to Apsara File Storage NAS, see [Overview](../../../../reseller.en-US//Overview.md#).

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
         Driver: alicloud/nas
         options:
           mode: "755"
           path: /data
           server: XXXX.cn-hangzhou.nas.aliyuncs.com
           vers: "4.0"
    ```

2.  Connect to the master node through an SSH client and run the kubectl command to create a PV.

    ```
     $ kubectl create -f nas.yaml
     persistentvolume "pv-nas-train" created
    ```

3.  After the PV is created, you can view the running status through the Container Service for Kubernetes console.

    ![](images/9150_en-US.png)


## Step 3: Use Helm to deploy TensorFlow distributed training applications {#section_aql_ggf_vdb .section}

1.  Log on to the [Container Service console](https://cs.console.aliyun.com/).
2.  In the left-side navigation pane, select Container Service for Kubernetes and choose **Marketplace** \> **App Catalog** to open the corresponding page.
3.  Click **ack-tensorflow-training** to go to the chart page.

    ![](images/9152_en-US.png)

4.  On the page, click the **Parameters** tab, configure the parameters for ack-tensorflow-training, and click **Create**.

    **Note:** By default, GPU clusters are used for model training.

    ![](images/9154_en-US.png)

    The following section is an example of the YAML file that supports GPU model training by customizing configuration parameters.

    ```
    worker:
       number: 2
       gpuCount: 1
       image: registry.cn-hangzhou.aliyuncs.com/tensorflow-samples/tf-mnist-k8s:gpu
       imagePullPolicy: IfNotPresent
       port: 8000
     ps:
       number: 2
       image: registry.cn-hangzhou.aliyuncs.com/tensorflow-samples/tf-mnist-k8s:cpu
       imagePullPolicy: IfNotPresent
       port: 9000
     hyperparams:
       epochs: 100
       batchsize: 20
       learningrate: 0.001
     tensorboard:
       image: registry.cn-hangzhou.aliyuncs.com/tensorflow-samples/tensorboard:1.1.0
       serviceType: LoadBalancer
     mountPath: /data
     persistence:
        pvc:
           storage: 5Gi
           matchLabels: 
             train: mnist                             ## This must be the same as the label created to attach the PV to your pods.
    ```

    If the Kubernetes cluster does not contain GPU instances as nodes, you can configure the following YAML file to deploy the TensorFlow model training.

    ```
    worker:
    number: 2
    gpuCount: 0
    # if you'd like to choose the customized docker image
    image: registry.cn-hangzhou.aliyuncs.com/tensorflow-samples/tf-mnist-k8s:cpu
    imagePullPolicy: IfNotPresent
    ps:
    number: 2
    # if you'd like to choose the customized docker image
    image: registry.cn-hangzhou.aliyuncs.com/tensorflow-samples/tf-mnist-k8s:cpu
    imagePullPolicy: IfNotPresent
    tensorboard:
    image: registry.cn-hangzhou.aliyuncs.com/tensorflow-samples/tensorboard:1.1.0
    serviceType: LoadBalancer
    hyperparams:
    epochs: 100
    batchsize: 20
    learningrate: 0.001
    persistence:
    mountPath: /data
    pvc:
      matchLabels:
        train: mnist  ## This must be the same as the label created to attach the PV to your pods.
      storage: 5Gi
    ```

    For more information about the code reference, visit [https://github.com/cheyang/tensorflow-sample-code](https://github.com/cheyang/tensorflow-sample-code).

    You can also run the Helm command to deploy the TensorFlow applications.

    ```
    helm install --values values.yaml --name mnist incubator/acs-tensorflow-tarining
     helm install --debug --dry-run --values values.yaml --name mnist incubator/acs-tensorflow-tarining
    ```

5.  After the deployment is complete, click **Dashboard** in the Actions column. On the Overview page that appears, you can view the application running status.

    ![](images/9155_en-US.png)


## Step 4: Use the Helm command to view the deployment information {#section_lgn_hhf_vdb .section}

1.  Connect to the master node of Kubernetes and run the helm list command to view the list of deployed applications.

    ```
     # helm list
     NAME              REVISION  UPDATED                   STATUS    CHART                         NAMESPACE
     mnist-dist-train  1         Mon Mar 19 15:23:51 2018  DEPLOYED  acs-tensorflow-training-0.1.0 default
    ```

2.  To check the configuration of a specific application, run the following helm status command:

    ```
     # helm status mnist-dist-train
     LAST DEPLOYED: Mon Mar 19 15:23:51 2018
     NAMESPACE: default
     STATUS: DEPLOYED
     RESOURCES:
     ==> v1/ConfigMap
     NAME             DATA  AGE
     tf-cluster-spec  1     7m
     ==> v1/Service
     NAME         TYPE       CLUSTER-IP     EXTERNAL-IP   PORT(S)   AGE
     worker-0     ClusterIP  None           <none>        8000/TCP  7m
     ps-1         ClusterIP  None           <none>        9000/TCP  7m
     tensorboard  ClusterIP  172.19.13.242  106.1.1.1 80/TCP    7m
     ps-0         ClusterIP  None           <none>        9000/TCP  7m
     worker-1     ClusterIP  None           <none>        8000/TCP  7m
     ==> v1beta1/Deployment
     NAME         DESIRED  CURRENT  UP-TO-DATE  AVAILABLE  AGE
     tensorboard  1        1        1           1          7m
     ==> v1/Job
     NAME      DESIRED  SUCCESSFUL  AGE
     ps-1      1        0           7m
     worker-0  1        0           7m
     ps-0      1        0           7m
     worker-1  1        0           7m
     ==> v1/Pod(related)
     NAME                          READY  STATUS   RESTARTS  AGE
     tensorboard-5c785fbd97-7cwk2  1/1    Running  0         7m
     ps-1-lkbtb                    1/1    Running  0         7m
     worker-0-2mpmb                1/1    Running  0         7m
     ps-0-ncxch                    1/1    Running  0         7m
     worker-1-4hngw                1/1    Running  0         7m
    ```

    In this example, the public IP address of the TensorBoard is 106.1.1.1. You can use this IP address to view the rate of convergence of the cost function.

3.  Check the running status of worker jobs. Make sure that all the worker jobs are in the running state.

    ```
    # kubectl get job
     NAME       DESIRED   SUCCESSFUL   AGE
     ps-0       1         0            5m
     ps-1       1         0            5m
     worker-0   1         0            5m
     worker-1   1         0            5m
     # kubectl get po
     NAME                          READY     STATUS    RESTARTS   AGE
     ps-0-jndpd                    1/1       Running   0          6m
     ps-1-b8zgz                    1/1       Running   0          6m
     tensorboard-f78b4d57b-pm2nf   1/1       Running   0          6m
     worker-0-rqmvl                1/1       Running   0          6m
     worker-1-7pgx6                1/1       Running   0          6m
    ```

4.  Check the training log.

    ```
     # kubectl logs --tail=10 worker-0-rqmvl
     Step: 124607,  Epoch: 24,  Batch: 1600 of 2750,  Cost: 0.8027,  AvgTime: 6.79ms
     Step: 124800,  Epoch: 24,  Batch: 1700 of 2750,  Cost: 0.7805,  AvgTime: 6.10ms
    ```

5.  Run the kubectl get jobs --watch command to monitor the job status.

    ```
     # kubectl get job
     NAME       DESIRED   SUCCESSFUL   AGE
     ps-0       1         0            1h
     ps-1       1         0            1h
     worker-0   1         1            1h
     worker-1   1         1            1h
    ```

6.  Check the training log and find that the training is complete.

    ```
    # kubectl logs --tail=10 -f worker-0-rqmvl
     Step: 519757,  Epoch: 100,  Batch: 2300 of 2750,  Cost: 0.1770,  AvgTime: 6.45ms
     Step: 519950,  Epoch: 100,  Batch: 2400 of 2750,  Cost: 0.2142,  AvgTime: 6.33ms
     Step: 520142,  Epoch: 100,  Batch: 2500 of 2750,  Cost: 0.1940,  AvgTime: 6.02ms
     Step: 520333,  Epoch: 100,  Batch: 2600 of 2750,  Cost: 0.5144,  AvgTime: 6.21ms
     Step: 520521,  Epoch: 100,  Batch: 2700 of 2750,  Cost: 0.5694,  AvgTime: 5.80ms
     Step: 520616,  Epoch: 100,  Batch: 2750 of 2750,  Cost: 0.5333,  AvgTime: 2.94ms
     Test-Accuracy: 0.89
     Total Time: 1664.68s
     Final Cost: 0.5333
     done
    ```


## Step 5: View the training results through the TensorBoard {#section_nrp_v2f_vdb .section}

View the training results through the TensorBoard. You can directly access http://106.1.1.1/ through a web browser to view the training results. Note that the 106.1.1.1 is the public IP address of the TensorBoard.

![](images/9156_en-US.png)

