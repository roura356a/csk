---
keyword: [TensorFlow, distributed training, shared file system, Arena]
---

# Use Arena to submit distributed TensorFlow training jobs

This topic describes how to use the Arena client to submit distributed TensorFlow training jobs that run in the parameter server \(PS\)-worker architecture. You can use TensorBoard to visualize training results.

-   [A Container Service for Kubernetes \(ACK\) cluster that contains GPU-accelerated nodes is created](/intl.en-US/User Guide for Kubernetes Clusters/GPU/NPU/GPU scheduling/Use GPU scheduling for ACK clusters.md).
-   [Nodes in the cluster can access the Internet](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Access clusters/Access the Kubernetes API server over the Internet.md).
-   [Install the latest version of Arena](/intl.en-US/Solutions/Deep learning solution/Preparations/Install the latest version of Arena.md).
-   A persistent volume claim \(PVC\) is created for the ACK cluster and the datasets used in this topic are downloaded to the corresponding persistent volume \(PV\). For more information, see [Configure a shared NAS volume](/intl.en-US/Solutions/Deep learning solution/Preparations/Configure a shared NAS volume.md) or [Configure a shared CPFS volume](/intl.en-US/Solutions/Deep learning solution/Preparations/Configure a shared CPFS volume.md).

In this topic, the source training code is downloaded from a Git repository. The datasets are stored in a shared Apsara File Storage NAS \(NAS\) volume that is mounted by using a PV and a PVC. In this example, a PVC that is named **training-data** is created. The PVC uses a shared PV. The datasets are stored in the tf\_data directory of the shared PV.

1.  Run the following command to query the available GPU resources in the cluster:

    ```
    arena top node
    ```

    Expected output:

    ```
    NAME                       IPADDRESS     ROLE    STATUS  GPU(Total)  GPU(Allocated)
    cn-huhehaote.192.16x.x.xx  192.1xx.x.xx  master  ready   0           0
    cn-huhehaote.192.16x.x.xx  192.1xx.x.xx  master  ready   0           0
    cn-huhehaote.192.16x.x.xx  192.1xx.x.xx  master  ready   0           0
    cn-huhehaote.192.16x.x.xx  192.1xx.x.xx  <none>  ready   2           0
    cn-huhehaote.192.16x.x.xx  192.1xx.x.xx  <none>  ready   2           0
    cn-huhehaote.192.16x.x.xx  192.1xx.x.xx  <none>  ready   2           0
    -----------------------------------------------------------------------------------------
    Allocated/Total GPUs In Cluster:
    0/6 (0%)
    ```

    The output shows that three GPU-accelerated nodes can be used to run training jobs.

2.  Run the `arena submit tfjob/tf [--flag]` command to submit a distributed TensorFlow training job.

    The following sample code provides an example on how to submit a distributed TensorFlow training job that runs in the PS-worker architecture. The training job runs on one PS node and two worker nodes.

    ```
        arena submit tf --name=tf-dist         \
                  --working-dir=/root \
                  --gpus=1              \
                  --workers=2              \
                  --worker-image=tensorflow/tensorflow:1.5.0-devel-gpu  \
                  --sync-mode=git \
                  --sync-source=https://code.aliyun.com/xiaozhou/tensorflow-sample-code.git \
                  --ps=1              \
                  --ps-image=tensorflow/tensorflow:1.5.0-devel   \
                  --data=training-data:/mnist_data \
                  --tensorboard \
                  --logdir=/mnist_data/tf_data/logs \
                  "python code/tensorflow-sample-code/tfjob/docker/mnist/main.py --log_dir /mnist_data/tf_data/logs  --data_dir /mnist_data/tf_data/"
    ```

    Expected output:

    ```
    configmap/tf-dist-tfjob created
    configmap/tf-dist-tfjob labeled
    service/tf-dist-tensorboard created
    deployment.apps/tf-dist-tensorboard created
    tfjob.kubeflow.org/tf-dist created
    INFO[0000] The Job tf-dist has been submitted successfully
    INFO[0000] You can run `arena get tf-dist --type tfjob` to check the job status
    ```

    The following table describes the parameters in the preceding sample code block.

    |Parameter|Required|Description|Default|
    |---------|--------|-----------|-------|
    |--name|Yes|Specifies the name of the job that you want to submit. The name must be globally unique.|N/A|
    |--working-dir|No|Specifies the directory where the command is executed.|/root|
    |--gpus|No|Specifies the number of GPUs that are used by the worker nodes where the training job runs.|0|
    |--workers|No|Specifies the number of worker nodes.|1|
    |--image|This parameter is required if you do not specify --worker-image for worker nodes or --ps-image for PS nodes.|Specifies the address of the image that is used to deploy the runtime. If you do not specify --worker-image or --ps-image, both worker nodes and PS nodes use the same image address.|N/A|
    |--worker-image|This parameter is required if you do not specify --image.|Specifies the address of the image for worker nodes. If --image is also specified, this parameter overwrites the --image parameter.|N/A|
    |--sync-mode|No|Specifies the synchronization mode. Valid values: git and rsync. The git-sync mode is used in this example.|N/A|
    |--sync-source|No|The address of the repository from which the source code is synchronized. This parameter is used in combination with the --sync-mode parameter. The git-sync mode is used in this example. Therefore, you must specify a Git repository address, such as the URL of a project on GitHub or Alibaba Cloud. The source code is downloaded to the code/ directory under --working-dir. The directory is /root/code/tensorflow-sample-code in this example.|N/A|
    |--ps|This parameter is required for distributed TensorFlow training jobs.|Specifies the number of PS nodes.|0|
    |--ps-image|This parameter is required if you do not specify --image.|Specifies the image address for PS nodes. If --image is also specified, this parameter overwrites the --image parameter.|N/A|
    |--data|No|Mounts a shared PV to the runtime where the training job runs. The value of this parameter consists of two parts that are separated by a colon \(`:`\). Specify the name of the PVC on the left side of the colon. To obtain the name of the PVC, run the `arena data list` command. This command queries the PVCs that are available for the specified cluster. Specify the path to which the PV claimed by the PVC is mounted on the right side of the colon. This way, your training job can retrieve the data stored in the corresponding PV claimed by the PVC. **Note:** Run the `arena data list` command to query the PVCs that are available for the specified cluster.

    ```
NAME           ACCESSMODE     DESCRIPTION  OWNER  AGE
training-data  ReadWriteMany                      35m
    ```

If no PVC is available, you can create one. For more information, see [Configure a shared NAS volume](/intl.en-US/Solutions/Deep learning solution/Preparations/Configure a shared NAS volume.md) or [Configure a shared CPFS volume](/intl.en-US/Solutions/Deep learning solution/Preparations/Configure a shared CPFS volume.md).

|N/A|
    |--tensorboard|No|Specifies that TensorBoard is used to visualize training results. You can set the --logdir parameter to specify the path from which TensorBoard reads event files. If you do not specify this parameter, TensorBoard is not used.|N/A|
    |--logdir|No|Specifies the path from which TensorBoard reads event files. You must specify both this parameter and the --tensorboard parameter.|/training\_logs|

    **Note:** If you use a non-public Git repository, run the following command to submit a training job:

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

    Expected output:

    ```
    NAME     STATUS     TRAINER  AGE  NODE
    tf-dist  RUNNING    TFJOB    58s  192.1xx.x.xx
    tf-git   SUCCEEDED  TFJOB    2h   N/A
    ```

4.  Run the following command to query the GPU resources that are used by the jobs:

    ```
    arena top job
    ```

    Expected output:

    ```
    NAME     GPU(Requests)  GPU(Allocated)  STATUS     TRAINER  AGE  NODE
    tf-dist  2              2               RUNNING    tfjob    1m   192.1xx.x.x
    tf-git   1              0               SUCCEEDED  tfjob    2h   N/A
    
    
    Total Allocated GPUs of Training Job:
    2
    
    Total Requested GPUs of Training Job:
    3
    ```

5.  Run the following command to query the GPU resources in the cluster:

    ```
    arena top node
    ```

    Expected output:

    ```
    NAME                       IPADDRESS     ROLE    STATUS  GPU(Total)  GPU(Allocated)
    cn-huhehaote.192.1xx.x.xx  192.1xx.x.xx  master  ready   0           0
    cn-huhehaote.192.1xx.x.xx  192.1xx.x.xx  master  ready   0           0
    cn-huhehaote.192.1xx.x.xx  192.1xx.x.xx  master  ready   0           0
    cn-huhehaote.192.1xx.x.xx  192.1xx.x.xx  <none>  ready   2           1
    cn-huhehaote.192.1xx.x.xx  192.1xx.x.xx  <none>  ready   2           1
    cn-huhehaote.192.1xx.x.xx  192.1xx.x.xx  <none>  ready   2           0
    -----------------------------------------------------------------------------------------
    Allocated/Total GPUs In Cluster:
    2/6 (33%)
    ```

6.  Run the following command to query detailed information about a job:

    ```
    arena get tf-dist
    ```

    Expected output:

    ```
    STATUS: RUNNING
    NAMESPACE: default
    PRIORITY: N/A
    TRAINING DURATION: 1m
    
    NAME     STATUS   TRAINER  AGE  INSTANCE          NODE
    tf-dist  RUNNING  TFJOB    1m   tf-dist-ps-0      192.1xx.x.xx
    tf-dist  RUNNING  TFJOB    1m   tf-dist-worker-0  192.1xx.x.xx
    tf-dist  RUNNING  TFJOB    1m   tf-dist-worker-1  192.1xx.x.xx
    
    Your tensorboard will be available on:
    http://192.1xx.x.xx:31870
    ```

    **Note:** TensorBoard is used in this example. Therefore, you can find the URL of TensorBoard in the last two rows of the job information. If TensorBoard is not used, the last two rows are not returned.

7.  Use a browser to view the training results in TensorBoard.

    In the preceding section, you can obtain the URL of TensorBoard from the job information. The ACK cluster is deployed in another region. Therefore, you must use sshuttle to view the visualized training results in your browser.

    The following sample code provides an example on how to use sshuttle as a proxy:

    ```
    # you can install sshuttle==0.74 in your mac with python2.7
    pip install sshuttle==0.74
    # 0/0 -> 0.0.0.0/0
    sshuttle -r root@39.104.xx.xxx  0/0
    ```

    **Note:** 39.104.xx. xxx is the public IP address of the ACK cluster. In addition, you must check whether port 22 is open in your security group. By default, port 22 is open.

    Copy the URL \(http://192.1xx.x.xx:31870\) that is obtained in Step 6 to the address bar of your browser and press Enter. The TensorBoard page appears.

    ![tf](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/8086258951/p135005.png)

8.  Run the following command to print the log of the job:

    ```
    arena logs tf-dist
    ```

    Expected output:

    ```
    WARNING:tensorflow:From code/tensorflow-sample-code/tfjob/docker/mnist/main.py:120: softmax_cross_entropy_with_logits (from tensorflow.python.ops.nn_ops) is deprecated and will be removed in a future version.
    Instructions for updating:
    ...
    Accuracy at step 960: 0.9691
    Accuracy at step 970: 0.9677
    Accuracy at step 980: 0.9687
    Accuracy at step 990: 0.968
    Adding run metadata for 999
    Total Train-accuracy=0.968
    ```

    After you run the preceding command, the log of worker-0 is printed by default. To print the log of a specified node, you can obtain the name of the specified node from the job information and run the `arena logs $job_name -i $instance_name` command to print the log.

    The following sample code provides an example on how to print the log of a specified node:

    ```
    arena get tf-dist
    ```

    Expected output:

    ```
    STATUS: SUCCEEDED
    NAMESPACE: default
    PRIORITY: N/A
    TRAINING DURATION: 1m
    
    NAME     STATUS     TRAINER  AGE  INSTANCE          NODE
    tf-dist  SUCCEEDED  TFJOB    5m   tf-dist-ps-0      192.16x.x.xx
    tf-dist  SUCCEEDED  TFJOB    5m   tf-dist-worker-0  192.16x.x.xx
    tf-dist  SUCCEEDED  TFJOB    5m   tf-dist-worker-1  192.16x.x.xx
    
    Your tensorboard will be available on:
    http://192.16x.x.xx:31870
    ```

    Run the following command to print the log of the job:

    ```
    arena logs tf-dist -i tf-dist-worker-1
    ```

    Expected output:

    ```
    WARNING:tensorflow:From code/tensorflow-sample-code/tfjob/docker/mnist/main.py:120: softmax_cross_entropy_with_logits (from tensorflow.python.ops.nn_ops) is deprecated and will be removed in a future version.
    Instructions for updating:
    ...
    Accuracy at step 970: 0.9676
    Accuracy at step 980: 0.968
    Accuracy at step 990: 0.967
    Adding run metadata for 999
    Total Train-accuracy=0.967
    ```

    You can run the `arena logs $job_name -f` command to print the job log in real time and run the `arena logs $job_name -t N` command to print N lines from the bottom of the log. You can also run the `arena logs --help` command to query parameters for printing logs.

    The following sample code provides an example on how to print N lines from the bottom of the log:

    ```
    arena logs tf-dist -t 5
    ```

    Expected output:

    ```
    Accuracy at step 9970: 0.9834
    Accuracy at step 9980: 0.9828
    Accuracy at step 9990: 0.9816
    Adding run metadata for 9999
    Total Train-accuracy=0.9816
    ```


