---
keyword: [nvidia-container-runtime, upgrade, shared GPU, cGPU]
---

# Upgrade the Docker runtime of a GPU node

To isolate GPU resources shared by multiple nodes in a Kubernetes cluster, Docker 19.03.5 and its nvidia-container-runtime binary must be used. If the Docker runtime version is earlier than 19.03.5, you must upgrade the Docker runtime to Docker 19.03.5. This topic describes how to upgrade the Docker runtime and its nvidia-container-runtime binary on these nodes to ensure GPU sharing among these nodes.

The nvidia-container-runtime binary allows you to build and run GPU-accelerated Docker containers. The binary automatically configures the containers. This ensures that the containers can use NVIDIA GPU resources.

## Procedure

**Note:** The steps described in this topic apply to only CentOS and Alibaba Cloud Linux 2.

Before you perform the following steps, you must use the command-line interface \(CLI\) to connect to your Container Service for Kubernetes \(ACK\) cluster. For more information, see [Use kubectl to connect to an ACK cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster management/Access clusters/Use kubectl to connect to an ACK cluster.md).

1.  Run the following command on the master node to disconnect a specified node from the cluster.

    You must label the node as unschedulable. This prevents pods from being scheduled to the node during the upgrade.

    ```
    kubectl cordon <NODE_NAME>
    ```

    **Note:** <NODE\_NAME\> specifies the name of the node on which the Docker runtime will be upgraded. You can run the kubectl get nodes command to query node names.

2.  Run the following command on the master node to migrate the pods on the node.

    After the node is disconnected from the cluster, you must migrate the pods on the node to other available nodes.

    ```
    kubectl drain <NODE_NAME> --ignore-daemonsets --delete-local-data --force
    ```

    **Note:** <NODE\_NAME\> specifies the name of the node on which the Docker runtime will be upgraded.

3.  Run the following commands to stop kubelet and the Docker runtime.

    Stop kubelet and the Docker runtime before you upgrade the Docker runtime on the node.

    ```
    service kubelet stop
    docker rm -f $(docker ps -aq)
    service docker stop
    ```

4.  Run the following commands to remove the Docker runtime and nvidia-container-runtime.

    Before you upgrade the Docker runtime and nvidia-container-runtime on the node, the current versions must be removed.

    ```
    yum remove -y docker-ce docker-ce-cli containerd
    yum remove -y nvidia-container-runtime*  libnvidia-container*
    ```

5.  Run the following commands to back up and remove the daemon.json file:

    ```
    cat /etc/docker/daemon.json
    {
        "default-runtime": "nvidia",
        "runtimes": {
            "nvidia": {
                "path": "/usr/bin/nvidia-container-runtime",
                "runtimeArgs": []
            }
        },
        "exec-opts": ["native.cgroupdriver=systemd"],
        "log-driver": "json-file",
        "log-opts": {
            "max-size": "100m",
            "max-file": "10"
        },
        "bip": "169.254.123.1/24",
        "oom-score-adjust": -1000,
        "storage-driver": "overlay2",
        "storage-opts":["overlay2.override_kernel_check=true"],
        "live-restore": true
    }
    mv /etc/docker/daemon.json /tmp
    ```

6.  Run the following command to install the Docker runtime.

    Download the Docker installation package to the node on which you want to upgrade the Docker runtime.

    ```
    VERSION=19.03.5 
    URL=http://aliacs-k8s-cn-beijing.oss-cn-beijing.aliyuncs.com/public/pkg/docker/docker-${VERSION}.tar.gz 
    curl -ssL $URL -o /tmp/docker-${VERSION}.tar.gz  
    cd /tmp
    tar -xf docker-${VERSION}.tar.gz
    cd /tmp/pkg/docker/${VERSION}/rpm
    yum localinstall -y $(ls .)
    ```

7.  Run the following command to install nvidia-container-runtime on the node:

    ```
    cd /tmp
    yum install -y unzip
    wget http://kubeflow.oss-cn-beijing.aliyuncs.com/nvidia.zip
    unzip nvidia.zip
    yum -y -q --nogpgcheck localinstall /tmp/nvidia/*
    ```

8.  Run the following command to configure the daemon.json file.

    Overwrite the /etc/docker/daemon.json file with the specified daemon.json file to make the original configurations take effect.

    ```
    mv /tmp/daemon.json  /etc/docker/daemon.json 
    cat /etc/docker/daemon.json
    {
        "default-runtime": "nvidia",
        "runtimes": {
            "nvidia": {
                "path": "/usr/bin/nvidia-container-runtime",
                "runtimeArgs": []
            }
        },
        "exec-opts": ["native.cgroupdriver=systemd"],
        "log-driver": "json-file",
        "log-opts": {
            "max-size": "100m",
            "max-file": "10"
        },
        "bip": "169.254.123.1/24",
        "oom-score-adjust": -1000,
        "storage-driver": "overlay2",
        "storage-opts":["overlay2.override_kernel_check=true"],
        "live-restore": true
    }
    ```

9.  Run the following commands to restart the Docker runtime and kubelet:

    ```
    service docker start
    service kubelet start
    ```

10. Run the following command to connect the node to the cluster.

    After the Docker runtime of the node is upgraded, the node is changed to the schedulable state in the cluster.

    ```
    kubectl uncordon <NODE_NAME>
    ```

    **Note:** <NODE\_NAME\> specifies the name of the node on which the Docker runtime has been upgraded.

11. Run the following command to restart the GPU installer on the node:

    ```
    docker ps |grep cgpu-installer | awk '{print $1}' | xargs docker rm -f
    ```


**Related topics**  


[Overview](/intl.en-US/User Guide for Kubernetes Clusters/GPU/NPU management/Shared GPU scheduling/Overview.md)

[Install a shared GPU](/intl.en-US/User Guide for Kubernetes Clusters/GPU/NPU management/Shared GPU scheduling/Install a shared GPU.md)

