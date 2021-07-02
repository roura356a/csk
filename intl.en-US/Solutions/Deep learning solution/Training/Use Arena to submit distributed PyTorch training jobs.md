---
keyword: [PyTorch, distributed training, shared file system, Arena]
---

# Use Arena to submit distributed PyTorch training jobs

This topic describes how to use the Arena client to submit distributed PyTorch training jobs and use TensorBoard to visualize training results.

-   [A Container Service for Kubernetes \(ACK\) cluster that contains GPU-accelerated nodes is created](/intl.en-US/User Guide for Kubernetes Clusters/GPU/NPU/GPU scheduling/Use GPU scheduling for ACK clusters.md).
-   [Nodes in the cluster can access the Internet](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Access clusters/Access the Kubernetes API server over the Internet.md).
-   [Install the latest version of Arena](/intl.en-US/Solutions/Deep learning solution/Preparations/Install the latest version of Arena.md).
-   A persistent volume claim \(PVC\) is created for the ACK cluster and the datasets used in this topic are downloaded to the corresponding persistent volume \(PV\). For more information, see [Configure a shared NAS volume](/intl.en-US/Solutions/Deep learning solution/Preparations/Configure a shared NAS volume.md) or [Configure a shared CPFS volume](/intl.en-US/Solutions/Deep learning solution/Preparations/Configure a shared CPFS volume.md).

In this topic, the source training code is downloaded from a Git repository. The datasets are stored in a shared Apsara File Storage NAS \(NAS\) volume that is mounted by using a PV and a PVC. In this example, a PVC that is named **training-data** is created. The PVC uses a shared PV. The datasets are stored in the pytorch\_data directory of the shared PV.

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

2.  Run the `arena submit tfjob/tf [--flag]` command to submit a distributed PyTorch training job.

    The following sample code provides an example on how to submit a distributed PyTorch training job that runs on three nodes with one GPU:

    ```
    arena submit pytorch \
            --name=pytorch-dist \
            --gpus=1 \
            --workers=3 \
            --working-dir=/root \
            --image=registry.cn-huhehaote.aliyuncs.com/lumo/pytorch-with-tensorboard:1.5.1-cuda10.1-cudnn7-runtime \
            --sync-mode=git \
            --sync-source=https://code.aliyun.com/370272561/mnist-pytorch.git \
            --data=training-data:/mnist_data \
            --tensorboard \
            --logdir=/mnist_data/pytorch_data/logs \
            "python /root/code/mnist-pytorch/mnist.py --epochs 10 --backend nccl --dir /mnist_data/pytorch_data/logs --data /mnist_data/pytorch_data/"
    ```

    Expected output:

    ```
    configmap/pytorch-dist-pytorchjob created
    configmap/pytorch-dist-pytorchjob labeled
    service/pytorch-dist-tensorboard created
    deployment.apps/pytorch-dist-tensorboard created
    pytorchjob.kubeflow.org/pytorch-dist created
    INFO[0000] The Job pytorch-dist has been submitted successfully
    INFO[0000] You can run `arena get pytorch-dist --type pytorchjob` to check the job status
    ```

    **Note:** Compared with the code that is used to submit a standalone PyTorch training job, the preceding code contains the --workers parameter. This parameter specifies the number of nodes on which the training job runs. A distributed training job runs on multiple nodes. The name of each node is specified in the following format: \[job\_name\]-\[role\_name\]-\[index\].

    -   \[job\_name\] specifies the name of the job.
    -   \[role\_name\] specifies the role of a node. In this example, the training job runs on one master node and two worker nodes. The name of the master node contains master, such as pytorch-dist-master-0. The nodes on which a distributed training job runs contain only one node whose name contains master.
    -   The rank of each worker node equals the value of \[index\] plus 1. The RANK environment variable is injected into each node. Therefore, you can use the RANK environment variable to obtain the rank of each worker node.
    The following table describes the parameters in the preceding code block.

    |Parameter|Required|Description|Default|
    |---------|--------|-----------|-------|
    |--name|Yes|Specifies the name of the job that you want to submit. The name must be globally unique.|N/A|
    |--working-dir|No|Specifies the directory where the command is executed.|/root|
    |--gpus|No|Specifies the number of GPUs that are used by the worker nodes where the distributed PyTorch training job runs.|0|
    |--workers|This parameter is required for distributed training jobs.|Specifies the number of worker nodes. The master node is included. For example, a value of 3 indicates that the training job runs on one master node and two worker nodes.|0|
    |--image|Yes|Specifies the address of the image that is used to deploy the runtime.|N/A|
    |--worker-image|This parameter is required if you do not specify --image.|Specifies the address of the image for worker nodes. If --image is also specified, this parameter overwrites the --image parameter.|N/A|
    |--sync-mode|No|Specifies the synchronization mode. Valid values: git and rsync. The git-sync mode is used in this example.|N/A|
    |--sync-source|No|The address of the repository from which the source code is synchronized. This parameter is used in combination with the --sync-mode parameter. The git-sync mode is used in this example. Therefore, you must specify a Git repository address, such as the URL of a project on GitHub or Alibaba Cloud. The source code is downloaded to the code/ directory under --working-dir. The directory is /root/code/mnist-pytorch in this example.|N/A|
    |--data|No|Mount a shared PV to the runtime where the training job runs. The value of this parameter consists of two parts that are separated by a colon \(`:`\). Specify the name of the PVC on the left side of the colon. To obtain the name of the PVC, run the `arena data list` command. This command queries the PVCs that are available for the cluster. Specify the path to which the PV claimed by the PVC is mounted on the right side of the colon. This way, your training job can retrieve the data stored in the corresponding PV claimed by the PVC. **Note:** Run the `arena data list` command to query the PVCs that are available for the specified cluster.

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
      arena --loglevel info submit pytorch \
            ...
            --sync-mode=git \
            --sync-source=https://code.aliyun.com/370272561/mnist-pytorch.git \
            --env=GIT_SYNC_USERNAME=yourname \
            --env=GIT_SYNC_PASSWORD=yourpwd \
            "python /root/code/mnist-pytorch/mnist.py --backend gloo"
    ```

    In the preceding code block, the Arena client synchronizes the source code in [git-sync](https://github.com/kubernetes/git-sync/blob/master/cmd/git-sync/main.go) mode. You can customize the environment variables that are defined in the git-sync code project.

3.  Run the following command to query the status of all submitted jobs:

    ```
    arena list
    ```

    Expected output:

    ```
    NAME          STATUS     TRAINER     AGE  NODE
    pytorch-dist  RUNNING    PYTORCHJOB  21s  192.16x.x.xx
    pytorch-git   SUCCEEDED  PYTORCHJOB  46m  N/A
    tf-dist       SUCCEEDED  TFJOB       14h  N/A
    tf-git        SUCCEEDED  TFJOB       17h  N/A
    ```

4.  Run the following command to query the GPU resources that are used by the jobs:

    ```
    arena top job
    ```

    Expected output:

    ```
    NAME          GPU(Requests)  GPU(Allocated)  STATUS     TRAINER     AGE  NODE
    pytorch-dist  3              3               RUNNING    pytorchjob  29s  192.16x.x.xx
    tf-dist       2              0               SUCCEEDED  tfjob       14h  N/A
    tf-git        1              0               SUCCEEDED  tfjob       17h  N/A
    pytorch-git   1              0               SUCCEEDED  pytorchjob  46m  N/A
    
    
    Total Allocated GPUs of Training Job:
    3
    
    Total Requested GPUs of Training Job:
    7
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
    cn-huhehaote.192.1xx.x.xx  192.1xx.x.xx  <none>  ready   2           2
    cn-huhehaote.192.1xx.x.xx  192.1xx.x.xx  <none>  ready   2           0
    cn-huhehaote.192.1xx.x.xx  192.1xx.x.xx  <none>  ready   2           1
    -----------------------------------------------------------------------------------------
    Allocated/Total GPUs In Cluster:
    3/6 (50%)
    ```

6.  Run the following command to query detailed information about a job:

    ```
    arena get pytorch-dist
    ```

    Expected output:

    ```
    STATUS: RUNNING
    NAMESPACE: default
    PRIORITY: N/A
    TRAINING DURATION: 57s
    
    NAME          STATUS   TRAINER     AGE  INSTANCE               NODE
    pytorch-dist  RUNNING  PYTORCHJOB  57s  pytorch-dist-master-0  192.168.0.33
    pytorch-dist  RUNNING  PYTORCHJOB  57s  pytorch-dist-worker-0  192.168.0.31
    pytorch-dist  RUNNING  PYTORCHJOB  57s  pytorch-dist-worker-1  192.168.0.31
    
    Your tensorboard will be available on:
    http://192.16x.x.xx:30131
    ```

    **Note:** TensorBoard is used in this example. Therefore, you can find the URL of TensorBoard in the last two rows of the job information. If TensorBoard is not used, the last two rows are not returned.

    The output shows that the job runs on one master node \(a rank-0 node\) and two worker nodes \(non-rank-0 nodes\).

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

    Copy the URL \(http://192.1xx.x.xx:31870\) that is obtained in Step 6 into the address bar of your browser and press Enter. The TensorBoard page appears.

    ![PyTorch](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/0286258951/p135105.png)

    **Note:** The source code that is used to submit the distributed PyTorch job in this topic indicates that training results are written into events after every 10 epochs. If you want to modify the value of --epochs, set the value to a multiple of 10. Otherwise, the training results cannot be visualized in TensorBoard.

8.  Run the following command to print the log of the job:

    ```
    arena logs pytorch-dist
    ```

    Expected output:

    ```
    WORLD_SIZE: 3, CURRENT_RANK: 0
    args: Namespace(backend='nccl', batch_size=64, data='/mnist_data/pytorch_data/', dir='/mnist_data/pytorch_data/logs', epochs=10, log_interval=10, lr=0.01, momentum=0.5, no_cuda=False, save_model=False, seed=1, test_batch_size=1000)
    Using CUDA
    ...
    Train Epoch: 10 [57600/60000 (96%)] loss=0.0026
    Train Epoch: 10 [58240/60000 (97%)] loss=0.0101
    Train Epoch: 10 [58880/60000 (98%)] loss=0.0106
    Train Epoch: 10 [59520/60000 (99%)] loss=0.0051
    
    accuracy=0.9904
    ```

    After you run the preceding code, the log of rank-0 nodes \(nodes whose names contain master\) is printed by default. To print the log of a specified node, you can obtain the name of the specified node from the job information and run the `arena logs $job_name -i $instance_name` command to print the log.

    The following sample code provides an example on how to print the log of a specified node:

    ```
    arena get pytorch-dist
    ```

    Expected output:

    ```
    # Output:
    STATUS: SUCCEEDED
    NAMESPACE: default
    PRIORITY: N/A
    TRAINING DURATION: 3m
    
    NAME          STATUS     TRAINER     AGE  INSTANCE               NODE
    pytorch-dist  SUCCEEDED  PYTORCHJOB  4m   pytorch-dist-master-0  192.16x.x.xx
    pytorch-dist  SUCCEEDED  PYTORCHJOB  4m   pytorch-dist-worker-0  192.16x.x.xx
    pytorch-dist  SUCCEEDED  PYTORCHJOB  4m   pytorch-dist-worker-1  192.16x.x.xx
    
    Your tensorboard will be available on:
    http://192.16x.x.xx:30131
    ```

    Run the following command to print the log of the job:

    ```
    arena logs pytorch-dist -i pytorch-dist-worker-0
    ```

    Expected output:

    ```
    WORLD_SIZE: 3, CURRENT_RANK: 1
    args: Namespace(backend='nccl', batch_size=64, data='/mnist_data/pytorch_data/', dir='/mnist_data/pytorch_data/logs', epochs=10, log_interval=10, lr=0.01, momentum=0.5, no_cuda=False, save_model=False, seed=1, test_batch_size=1000)
    Using CUDA
    ...
    Train Epoch: 10 [57600/60000 (96%)] loss=0.0026
    Train Epoch: 10 [58240/60000 (97%)] loss=0.0101
    Train Epoch: 10 [58880/60000 (98%)] loss=0.0106
    Train Epoch: 10 [59520/60000 (99%)] loss=0.0051
    
    accuracy=0.9904
    ```

    You can run the `arena logs $job_name -f` command to print the log of the job in real time and run the `arena logs $job_name -t N` command to print N lines from the bottom of the log. You can also run the `arena logs --help` command to query parameters for printing log.

    The following sample code provides an example on how to print N lines from the bottom of the log:

    ```
    arena logs pytorch-dist -t 5
    ```

    Expected output:

    ```
    Train Epoch: 10 [58880/60000 (98%)] loss=0.0106
    Train Epoch: 10 [59520/60000 (99%)] loss=0.0051
    
    accuracy=0.9904
    ```


