# Set up the environment

Before you migrate application configurations from a Swarm cluster to a Kubernetes cluster, you must set up the environment for migration. This topic describes how to set up the environment for migration.

Kompose is an open source tool for converting Swarm compose files to Kubernetes resource files. We have optimized Kompose to support labels that are specific to Alibaba Cloud.

After Kubernetes resource files are generated, you can [Use kubectl to connect to an ACK cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster management/Access clusters/Use kubectl to connect to an ACK cluster.md) and deploy these files in a Kubernetes cluster.

Kompose and kubectl are required to migrate application configurations. We recommend that you install Kompose and kubectl on a dedicated Elastic Compute Service \(ECS\) instance.

1.  Install Kompose.

    Kompose is a tool for converting Swarm compose files to Kubernetes resource files. We have optimized Kompose to support labels that are specific to Alibaba Cloud. For more information about Kompose, see [AliyunContainerService/kompose](https://github.com/AliyunContainerService/kompose).

    Installation: Download the latest executable file [AliyunContainerService/kompose/releases](https://github.com/AliyunContainerService/kompose/releases?spm=a2c4g.11186623.2.12.813159f2UrFelU) from GitHub based on your operating system.

    ```
    kompose-linux-amd64
    ```

    ![kompose](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/6646858951/p47970.png)

2.  Set up the environment for kubectl.

    1.  Download the latest kubectl client from the [Kubernetes change log page](https://github.com/kubernetes/kubernetes/blob/master/CHANGELOG.md?spm=5176.2020520152.0.0.110416ddAr1dni&file=CHANGELOG.md).

    2.  Install and set up the kubectl client. For more information, see [Install and set up kubectl](https://kubernetes.io/docs/tasks/kubectl/install/?spm=5176.2020520152.0.0.110416ddAr1dni).

    3.  Configure cluster credentials.

        1.  [Log on to the Container Service for Kubernetes \(ACK\) console](https://cs.console.aliyun.com). In the left-side navigation pane, choose **Clusters** \> **Clusters**. On the Clusters page, find the cluster that you want to manage and click **Manage** in the Actions column.
        2.  On the **Basic Information** tab, copy and paste the KubeConfig content to $HOME/.kube/config on your on-premises machine.
    4.  After the setup is complete, run the following commands to check whether the installation succeeds and cluster credentials are correctly configured.

        ```
        kubectl version
        
        kubectl cluster-info
        ```


If the output is as shown in the following red box, it indicates that kubectl is installed.

![Environment is set up](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/6646858951/p47973.png)

