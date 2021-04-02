---
keyword: [Arena, machine learning, lightweight solution]
---

# Install the latest version of Arena

Arena is a lightweight client that is designed to manage Kubernetes-based machine learning tasks. Arena allows you to streamline data preparation, model development, model training, and model prediction throughout a complete lifecycle of machine learning. This improves the work efficiency of data scientists. Arena is also deeply integrated with the basic services of Alibaba Cloud. It supports GPU sharing and Cloud Paralleled File System \(CPFS\). Arena can run in deep learning frameworks optimized by Alibaba Cloud. This maximizes the performance and utilization of heterogeneous computing resources provided by Alibaba Cloud.

-   A cluster of Alibaba Cloud Container Service for Kubernetes \(ACK\) that contains GPU-accelerated nodes is created. For more information, see [Create a managed Kubernetes cluster with GPU-accelerated nodes](/intl.en-US/User Guide for Kubernetes Clusters/GPU/NPU/Create heterogeneous computing clusters/Create a managed Kubernetes cluster with GPU-accelerated nodes.md) or [Create a dedicated Kubernetes cluster with GPU-accelerated nodes](/intl.en-US/User Guide for Kubernetes Clusters/GPU/NPU/Create heterogeneous computing clusters/Create a dedicated Kubernetes cluster with GPU-accelerated nodes.md).
-   Nodes in the cluster can access the Internet.

## Step 1: Install the Arena client

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane of the ACK console, click **Clusters**.

3.  On the Clusters page, find the cluster that you want to manage and choose **More** \> **Manage System Components** in the **Actions** column.

4.  Find **ack-arena** and click **Install**.


## Step 2: Configure the Arena client

If you use a dedicated Kubernetes cluster, use an SSH key pair to log on to a master node in the cluster and run the arena command. For more information, see [Use SSH to connect to an ACK cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Access clusters/Use SSH to connect to an ACK cluster.md).

If you use a managed Kubernetes cluster, you must use the kubectl command-line tool to connect to the cluster. This ensures that the kubeconfig file is stored in the $HOME/.kube/config directory. For more information, see [Use kubectl to connect to an ACK cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Access clusters/Use kubectl to connect to an ACK cluster.md). Then, perform the following steps to install and configure the Arena client:

**Note:** You can run the `kubectl get nodes` command to check whether the configuration in the kubeconfig file is correct.

1.  Download the Arena client.

    -   [Arena for Linux](http://kubeflow.oss-cn-beijing.aliyuncs.com/arena-installer-0.5.0-e22162d-linux-amd64.tar.gz)
    -   [Arena for macOS](http://kubeflow.oss-cn-beijing.aliyuncs.com/arena-installer-0.5.0-e22162d-darwin-amd64.tar.gz)
2.  Decompress the package.

    -   If you want to install the Arena client in a Linux operating system, run the following command to decompress the package:

        ```
        tar -xvf arena-installer-0.5.0-e22162d-linux-amd64.tar.gz
        ```

    -   If you want to install the Arena client in a macOS operating system, run the following command to decompress the package:

        ```
        tar -xvf arena-installer-0.5.0-e22162d-darwin-amd64.tar.gz
        ```

3.  Run the following command to install the Arena client:

    ```
    cd arena-installer
    ./install.sh
    ```

4.  Install Bash auto completion.

    The auto completion feature of Bash can automatically fill in partially typed commands.

    -   Run the following command to install Bash auto completion on CentOS or Alibaba Cloud Linux 2:

        ```
        yum install bash-completion -y
        ```

    -   Run the following command to install Bash auto completion on Debian or Ubuntu:

        ```
         apt-get install bash-completion
        ```

    -   Run the following command to install Bash auto completion on macOS:

        ```
        brew install bash-completion@2
        ```

5.  Run the following command to add the Bash auto completion feature to the profile file.

    -   Linux

        ```
        echo "source <(arena completion bash)" >> ~/.bashrc
        chmod u+x ~/.bashrc
        ```

    -   MacOS

        ```
        echo "source $(brew --prefix)/etc/profile.d/bash_completion.sh" >> ~/.bashrc
        ```

    Then, you can press **Tab** in the Arena client to enable the system to complete a partially typed command for you.


## Step 3: Test whether Arena works as expected

You can perform the following steps to check whether Arena works as expected:

1.  Run the following command to check the available GPU resources in the cluster:

    ```
    arena top node
    ```

    The output shows information about the nodes and GPUs. This indicates that Arena is working as expected.

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

2.  Use Arena to submit a training job. The output shows that the job is submitted.

    ```
    arena submit tf \
          --name=firstjob \
          --gpus=1 \
          --image=registry.cn-hangzhou.aliyuncs.com/tensorflow-samples/tf-mnist-standalone:gpu \
          "python /app/main.py"
    ```

    ```
    configmap/firstjob-tfjob created
    configmap/firstjob-tfjob labeled
    tfjob.kubeflow.org/firstjob created
    INFO[0001] The Job firstjob has been submitted successfully
    INFO[0001] You can run `arena get firstjob --type tfjob` to check the job status
    ```

3.  Run the `arena list` command to query all jobs.

    Sample output:

    ```
    NAME      STATUS   TRAINER  AGE  NODE
    firstjob  RUNNING  TFJOB    5s   192.1xx.x.xxx
    ```

4.  Run the following command to check the status of the submitted job:

    ```
    arena get firstjob
    ```

    ```
    STATUS: SUCCEEDED
    NAMESPACE: default
    PRIORITY: N/A
    TRAINING DURATION: 52s
    NAME      STATUS     TRAINER  AGE  INSTANCE          NODE
    firstjob  SUCCEEDED  TFJOB    14m  firstjob-chief-0  192.168.0.118
    ```

5.  Run the following command to view the log entries of a job:

    ```
    arena logs --tail=10 firstjob
    ```

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


