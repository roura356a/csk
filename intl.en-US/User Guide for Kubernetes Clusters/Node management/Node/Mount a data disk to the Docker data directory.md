# Mount a data disk to the Docker data directory

This topic describes how to mount a data disk to the Docker data directory. The existing disk capacity may be insufficient to support an increase in the number of containers or images on a node. You can mount a data disk to the Docker data directory. This allows you to expand the disk capacity and provide more storage space for containers and images.

## Docker data directory

Docker data is stored in disks based on a union file system \(UnionFS\). The default container data and image data of Docker is stored in the /var/lib/docker directory. You can run the du command to view the disk size that is occupied by this directory.

```
# du -h --max-depth=0 /var/lib/docker
7.9G    /var/lib/docker
```

## Scenarios

A Docker image occupies a large amount of disk space. If you want to use multiple Docker images or a large number of containers, you must mount a data disk to the Docker data directory. This allows you to ensure that sufficient disk capacity is available.

## Mount a data disk

To mount a disk to the Docker data directory, perform the following steps:

1.  Create a data disk and mount the disk to the node for which you want to expand the disk capacity. For more information, see [Create a disk](/intl.en-US/Block Storage/Cloud disks/Create a cloud disk/Create a disk.md).

    1.  Log on to the [Elastic Compute Service \(ECS\) console](https://ecs.console.aliyun.com/) and create the required cloud disk.

    2.  In the left-side navigation pane, click **Instances**.

    3.  On the Instances page, click the ID of the ECS instance that you want to manage to go to the Instance Details page.

    4.  In the left-side navigation pane, click **Disks**.

    5.  On the Disks page, click **Attach Disk**.

    6.  In the dialog box that appears, select the created disk from the **Target Disk** drop-down list. Click **OK**.

    7.  Click **Attach** to attach the disk to the specified ECS instance, and record the mount point `/dev/xvd*` or `/dev/vd*`.

2.  Log on to the ECS instance and format the mounted disk.

    1.  In the command-line interface \(CLI\), enter ls -l /dev/xvd\* or ls -l /dev/vd\* to check whether the returned mount point is the same as that you have recorded.

    2.  In the CLI, enter fdisk to partition the disk, and then enter mkfs.ext4 to format the disk.

        ![Format the disk](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/5735359951/p38212.png)

3.  Migrate the Docker data to the disk.

    If you do not want to suspend the applications that run on the node, you must migrate the applications. For more information about how to migrate applications that run in a Swarm cluster, see [Schedule an application to specified nodes](/intl.en-US/User Guide/Applications/Schedule an application to specified nodes.md). For more information about how to migrate applications that run in a Kubernetes cluster, see [Safely Drain a Node while Respecting Application SLOs](https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/).

    1.  To ensure that data can be migrated, In the CLI, enter service docker stop to stop Docker daemon, and enter service kubelet stop to stop kubelet.

    2.  Migrate the Docker directory data to a backup directory, such as mv /var/lib/docker /var/lib/docker\_data.

    3.  Mount the formatted disk to the /var/lib/docker and /var/lib/kubelet directories. In the CLI, run the following command:

        ```
        echo "/dev/xvdb1    /var/lib/container/     ext4    defaults        0 0" >>/etc/fstab
        echo "/var/lib/container/kubelet /var/lib/kubelet none defaults,bind 0 0" >>/etc/fstab
        echo "/var/lib/container/docker /var/lib/docker none defaults,bind 0 0" >>/etc/fstab
        
        
        mkdir /var/lib/docker
        
        mount -a
        ```

    4.  Migrate the Docker data that has been backed up to the formatted disk, such as mv /var/lib/docker\_data/\* /var/lib/docker/.

4.  Start the Docker daemon and kubelet, and check the data location.

    1.  In the CLI, enter service docker start to start the Docker daemon, and enter service kubelet start to start kubelet.

    2.  In the CLI, enter df to check whether /var/lib/docker has been mounted to the disk. If you want to start the Kubernetes cluster, skip this step.

        ![Start the cluster](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/5735359951/p38133.png)

    3.  In the CLI, enter docker ps to check whether containers are lost. Restart containers as required. For example, you can restart a container to which the `restart:always` label is attached.

        ![Set labels](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/5735359951/p38134.png)

5.  If a container has been migrated to other nodes, you can schedule it back to the node to which you have attached the disk.

    For more information, see [Container Service for Kubernetes](https://www.alibabacloud.com/zh/product/container-service).


