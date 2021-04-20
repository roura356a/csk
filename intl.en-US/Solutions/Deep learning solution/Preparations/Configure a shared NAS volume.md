---
keyword: [shared NAS volumes, Arena, distributed storage, configure a PV and a PVC]
---

# Configure a shared NAS volume

Apsara File Storage NAS \(NAS\) is a file system service that is intended for computing services, such as Elastic Compute Service \(ECS\) instances, Elastic High Performance Computing \(E-HPC\) clusters, and Container Service for Kubernetes \(ACK\) clusters. NAS provides seamless integration, storage sharing, and security management. NAS is suitable for multi-cluster applications because the ECS instances, E-HPC clusters, or ACK clusters where the application is deployed may want to access the same data source. This topic describes how to configure a shared NAS volume.

Data scientists may want to persist their data or retrieve the same copy of training data. To meet this requirement, we recommend that you configure a shared NAS volume and mount it to the runtime where you use Arena to submit jobs. This prevents data loss because the code and data that are stored in the shared NAS volume are not deleted with containers.

We recommend that you allow the developers in a team to share the same storage pool. If you declare a shared NAS volume and specify the path that is used to mount the volume, the shared NAS volume is automatically mounted to the specified path. After the shared NAS volume is mounted, you can consume the data and code that are stored in the volume by specifying the --data parameter each time you submit a job.

In Kubernetes, storage volumes are declared by using persistent volumes \(PVs\) and persistent volume claims \(PVCs\). As the administrator of a Kubernetes cluster, you must create a PVC for each data scientist in your team. For example, User A and User B can mount their PVs to the same NAS volume or Cloud Paralleled File System \(CPFS\). However, the PVs must be mounted to different subdirectories to isolate the runtimes of User A and User B.

## Step 1: Create a NAS file system

For more information about how to create a NAS file system, see [Create a General-purpose NAS file system]().

**Note:**

Set the parameters based on the following descriptions:

-   Select **General Purpose NAS** as the file system type.
-   Select the region where the specified ACK cluster is deployed.
-   Select **NFS** as the protocol type.

## Step 2: Add a mount target

For more information about how to add a mount target, see [Create a mount target]().

**Note:**

Set the parameters based on the following descriptions:

-   Select **VPC** as the type of mount target.
-   Select the same virtual private cloud \(VPC\) and same vSwitches that are used in the specified ACK cluster.

After the mount target is created, go to the **Mount Target** page and move the pointer over the ![Mount target](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/2138263161/p134346.png) icon to check the address of the mount target.

![The information about the mount target](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/9876258951/p134349.png)

## Step 3: Create a PV and a PVC for the specified ACK cluster

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  Create a PV.

    For more information about how to create a PV, see [Create a PV](/intl.en-US/User Guide for Kubernetes Clusters/Storage management-CSI/NAS volumes/Mount a NAS file system as a static PV by using YAML files.md).

    **Note:** When you configure the PV, set **Mount Target Domain Name:** to **Select Mount Target**, and select the address of the mount target from the Select drop-down list.

3.  Create a PVC.

    For more information about how to create a PVC, see [Create a PVC](/intl.en-US/User Guide for Kubernetes Clusters/Storage management-CSI/NAS volumes/Mount a NAS file system as a static PV by using YAML files.md).

    After the PVC is created, you can find the created PVC in the PVC list.


## Step 4: Upload data to the PVC

Kubernetes clusters use PVCs to access shared data \(data stored in the NAS file system in this example\). Therefore, you need only to upload the data and code to the NAS file system.

1.  Run the following command to log on to an ECS instance in the specified ACK cluster:

    ```
    ssh root@39.10x.xx.xx
    ```

    **Note:** Make sure that the ECS instance and the NAS file system are deployed in the same VPC.

2.  Paste the mount command to the command-line interface and run the command to mount the NAS file system.

    The mount command is used to mount the NAS file system to the /mnt path of the ECS instance.

    ![Mount command](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/9876258951/p134539.png)

    ```
    sudo mount -t nfs -o xxxx.nas.aliyuncs.com:/ /mnt
    ```

3.  Run the following command to create two subdirectories for the mounted NAS file system: tf\_data/ for storing the training data of TF mnist and pytorch\_data/ for storing the training data of Pytorch mnist.

    ```
    cd /mnt/
    mkdir tf_data/
    mkdir pytorch_data/
    ```

4.  Run the following command to download the TF mnist dataset:

    ```
    cd tf_data
    git clone https://code.aliyun.com/xiaozhou/tensorflow-sample-code.git
    mv tensorflow-sample-code/data/* ./ && rm -rf tensorflow-sample-code
    ```

5.  Run the following command to download the Pytorch mnist dataset:

    ```
    cd pytorch_data
    git clone https://code.aliyun.com/370272561/mnist-pytorch.git
    mv mnist-pytorch/MNIST ./ && rm -rf mnist-pytorch
    ```


