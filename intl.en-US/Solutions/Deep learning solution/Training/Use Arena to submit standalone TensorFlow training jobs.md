---
keyword: [TensorFlow, standalone training, shared file system, Arena]
---

# Use Arena to submit standalone TensorFlow training jobs

This topic describes how to use the Arena client to submit standalone TensorFlow training jobs and how to use TensorBoard to visualize training results.

-   [An ACK cluster that contains GPU-accelerated nodes is created](/intl.en-US/User Guide for Kubernetes Clusters/GPU/NPU management/GPU resource scheduling/Configure a Kubernetes GPU cluster to support GPU scheduling.md).
-   [Nodes in the cluster can access the Internet](/intl.en-US/User Guide for Kubernetes Clusters/Cluster management/Access clusters/Access the Kubernetes API server over the Internet.md).
-   [The latest versions of the ack-arena add-on and the Arena client are installed](/intl.en-US/Solutions/Deep learning solution/Preparations/Install the latest version of Arena.md).
-   A PVC is created for the target cluster of Alibaba Cloud Container Service for Kubernetes \(ACK\) and the datasets used in this topic are imported to the corresponding PV. For more information, see [Configure a shared NAS volume](/intl.en-US/Solutions/Deep learning solution/Preparations/Configure a shared NAS volume for training jobs.md) or [Configure a shared CPFS volume](/intl.en-US/Solutions/Deep learning solution/Preparations/Configure a shared CPFS volume.md).

In this topic, the source training code is downloaded from a Git repository.The datasets are stored in a shared NAS volume that is declared by a persistent volume \(PV\) and a persistent volume claim \(PVC\). In this example, a PVC that is named **training-data** is created. The PVC uses a shared PV. The datasets in this example are stored in the tf\_data directory of the shared PV.

1.  Run the following command to query the available GPU resources in the cluster:

    ```
    arena top node
    ```

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

    The output shows that three GPU-accelerated nodes can be used to run training jobs.

2.  Run the `arena submit tfjob/tf [--flag]` command to submit a standalone TensorFlow training job.

    The following sample code shows how to submit a standalone TensorFlow training job that runs on one node with one GPU.

    ```
      arena submit tf \
        --name=tf-git \
        --working-dir=/root \
        --gpus=1 \
        --image=tensorflow/tensorflow:1.5.0-devel-gpu \
        --sync-mode=git \
        --sync-source=https://code.aliyun.com/xiaozhou/tensorflow-sample-code.git \
        --data=training-data:/mnist_data \
        --tensorboard \
        --logdir=/mnist_data/tf_data/logs \
        "python code/tensorflow-sample-code/tfjob/docker/mnist/main.py --log_dir /mnist_data/tf_data/logs  --data_dir /mnist_data/tf_data/"
    ```

    ```
    configmap/tf-git-tfjob created
    configmap/tf-git-tfjob labeled
    service/tf-git-tensorboard created
    deployment.apps/tf-git-tensorboard created
    tfjob.kubeflow.org/tf-git created
    INFO[0000] The Job tf-git has been submitted successfully
    INFO[0000] You can run `arena get tf-git --type tfjob` to check the job status
    ```

    The following table lists the parameters in the preceding code block.

    |Parameter|Required|Description|Default|
    |---------|--------|-----------|-------|
    |--name|Yes|Specifies the name of the submitted job. The name must be globally unique.|N/A|
    |--working-dir|No|Specifies the directory where the command is executed.|/root|
    |--gpus|No|Specifies the number of GPUs that are used by the worker node where the standalone TensorFlow training job runs.|0|
    |--image|Yes|Specifies the address of the image that is used to deploy the runtime.|N/A|
    |--sync-mode|No|Specifies the synchronization mode. Valid values: git and rsync. The git-sync mode is used in this example.|N/A|
    |--sync-source|No|The address of the repository from which the source code is synchronized. This parameter is used in combination with the --sync-mode parameter. The git-sync mode is used in this example. Therefore, you must specify a Git repository address, such as the URL of a project on GitHub or Alibaba Cloud. The source code of the project is downloaded to the code/ directory under --working-dir. The directory is /root/code/tensorflow-sample-code in this example.|N/A|
    |--data|No|Mounts a shared PV to the runtime where the training job runs. The value of this parameter consists of two parts that are separated with a colon \(`:`\). Specify the name of the PVC on the left side of the colon. To query the name of the PVC, run the `arena data list` command. This command queries the PVCs that are available for the target cluster. Specify the path to which the PV claimed by the PVC is mounted on the right side of the colon. This way, your training job can retrieve the data stored in the corresponding PV claimed by the PVC.**Note:** Run the `arena data list` command to query the PVCs that are available for the target cluster.

    ```
NAME           ACCESSMODE     DESCRIPTION  OWNER  AGE
training-data  ReadWriteMany                      35m
    ```

If no PVC is available, create one. For more information, see [Configure a shared NAS volume](/intl.en-US/Solutions/Deep learning solution/Preparations/Configure a shared NAS volume for training jobs.md).

|N/A|
    |--tensorboard|No|Specifies that TensorBoard is used to visualize training results. You can set this parameter in combination with the --logdir parameter to specify the path from which TensorBoard reads event files. If you do not specify this parameter, TensorBoard is not used.|N/A|
    |--logdir|No|Specifies the path from which TensorBoard reads event files. It must be used in combination with --tensorboard.|/training\_logs|

    **Note:** If you use a non-public Git repository, run the following command to submit a standalone TensorFlow training job.

    ```
     arena submit tf \
            ...
            --sync-mode=git \
            --sync-source=https://code.aliyun.com/xiaozhou/tensorflow-sample-code.git \
            --env=GIT_SYNC_USERNAME=yourname \
            --env=GIT_SYNC_PASSWORD=yourpwd \
            "python code/tensorflow-sample-code/tfjob/docker/mnist/main.py
    ```

    In the preceding code block, the Arena client synchronizes the source code in [git-sync](https://github.com/kubernetes/git-sync/blob/master/cmd/git-sync/main.go) mode. You can customize the environment variables that are defined in the git-sync code project.

3.  Run the following command to query the status of all submitted jobs:

    ```
    arena list
    ```

    ```
    NAME    STATUS   TRAINER  AGE  NODE
    tf-git  RUNNING  TFJOB    20s  192.1xx.x.xx
    ```

4.  Run the following command to query the GPU resources used by these jobs:

    ```
    arena top job
    ```

    ```
    NAME    GPU(Requests)  GPU(Allocated)  STATUS   TRAINER  AGE  NODE
    tf-git  1              1               RUNNING  tfjob    1m   192.1xx.x.xx
    
    
    Total Allocated GPUs of Training Job:
    1
    
    Total Requested GPUs of Training Job:
    1
    ```

5.  Run the following command to query the GPU resources in the cluster:

    ```
    arena top node
    ```

    ```
    NAME                       IPADDRESS     ROLE    STATUS  GPU(Total)  GPU(Allocated)
    cn-huhehaote.192.1xx.x.xx  192.1xx.x.xx  master  ready   0           0
    cn-huhehaote.192.1xx.x.xx  192.1xx.x.xx  master  ready   0           0
    cn-huhehaote.192.1xx.x.xx  192.1xx.x.xx  master  ready   0           0
    cn-huhehaote.192.1xx.x.xx  192.1xx.x.xx  <none>  ready   2           1
    cn-huhehaote.192.1xx.x.xx  192.1xx.x.xx  <none>  ready   2           0
    cn-huhehaote.192.1xx.x.xx  192.1xx.x.xx  <none>  ready   2           0
    -----------------------------------------------------------------------------------------
    Allocated/Total GPUs In Cluster:
    1/6 (16%)
    ```

6.  Run the following command to query detailed information about a job:

    ```
    arena get tf-git
    ```

    ```
    STATUS: SUCCEEDED
    NAMESPACE: default
    PRIORITY: N/A
    TRAINING DURATION: 1m
    
    NAME    STATUS     TRAINER  AGE  INSTANCE        NODE
    tf-git  SUCCEEDED  TFJOB    18m  tf-git-chief-0  192.16x.x.xx
    
    Your tensorboard will be available on:
    http://192.16x.x.xx:31619
    ```

    **Note:** TensorBoard is used in this example. Therefore, you can find the URL of TensorBoard in the last two rows of the job information. If TensorBoard is not used, the last two rows are not returned.

7.  Use a browser to view the training results in TensorBoard

    In the preceding section, you can find the URL of TensorBoard. The ACK cluster is deployed in another region. Therefore, you must use sshuttle to view the visualized training results in your browser.

    The following sample code shows how to use sshuttle as a proxy:

    ```
    # you can install sshuttle==0.74 in your mac with python2.7
    pip install sshuttle==0.74
    # 0/0 -> 0.0.0.0/0
    sshuttle -r root@39.104.xx.xxx  0/0
    ```

    **Note:** 39.104.xx.xxx is the public IP address of the ACK cluster. In addition, you must check whether port 22 is opened in your security group. By default, port 22 is opened.

    Copy the URL \(http://192.16x.x.xx:31619\) that is obtained in Step 6 to the address bar of your browser and press Enter. The TensorBoard page appears.

    ![tensorboard](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/1088263161/p134899.png)

8.  Run the following command to view the job logs:

    ```
    arena logs tf-git
    ```

    ```
    WARNING:tensorflow:From code/tensorflow-sample-code/tfjob/docker/mnist/main.py:120: softmax_cross_entropy_with_logits (from tensorflow.python.ops.nn_ops) is deprecated and will be removed in a future version.
    Instructions for updating:
    ...
    Accuracy at step 9970: 0.9834
    Accuracy at step 9980: 0.9828
    Accuracy at step 9990: 0.9816
    Adding run metadata for 9999
    Total Train-accuracy=0.9816
    ```

    You can run the `arena logs $job_name -f` command to print the job log in real time and run the `arena logs $job_name -t N` command to print N lines from the bottom of the log. You can also run the `arena logs --help` command to query parameters for printing logs.

    The following sample code shows how to print N lines from the bottom of the log.

    ```
    arena logs tf-git -t 5
    ```

    ```
    Accuracy at step 9970: 0.9834
    Accuracy at step 9980: 0.9828
    Accuracy at step 9990: 0.9816
    Adding run metadata for 9999
    Total Train-accuracy=0.9816
    ```


