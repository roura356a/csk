---
keyword: [Docker, add a data disk]
---

# Mount a data disk to the Docker data directory

This topic describes how to mount a data disk to the Docker data directory. When the disk space is insufficient to support the increase in the number of containers or images on a node, you can mount a data disk to the Docker data directory. This provides more storage space to create containers and store images.

## Docker data directory

Docker data is stored in a union file system \(UnionFS\) on a disk. The default container data and image data of Docker is stored in the /var/lib/docker directory. You can run the du command to view the disk space that is occupied by this directory:

```
du -h --max-depth=0 /var/lib/docker
```

Possible output:

```
7.9G    /var/lib/docker
```

## Mount a data disk

A Docker image may be large in size. A few images may occupy a large amount of disk space. The disk capacity may be insufficient if you store large-sized images or create a large number of containers. In this case, you must mount a data disk to the Docker data directory. This provides storage space for these images and containers.

1.  Create a data disk and mount it to the node for which you want to expand the disk. For more information, see [Create a disk](/intl.en-US/Block Storage/Cloud disks/Create a cloud disk/Create a disk.md).

    1.  Log on to the [Elastic Compute Service \(ECS\) console](https://ecs.console.aliyun.com/) and create a disk.

    2.  In the left-side navigation pane, choose **Instances & Images** \> **Instances**.

    3.  On the Instances page, click the ID of the ECS instance that you want to manage. Then, you are directed to the instance details page.

    4.  Click the **Cloud Disk** tab.

    5.  On the Cloud Disk tab, click **Attach Disk** in the upper-right corner of the page.

    6.  In the Attach Disk dialog box, select the created disk from the **Target Disk** drop-down list. Click **OK**.

    7.  Click **Attach** to attach the disk to the ECS instance. Record the mount point `/dev/xvd*` or `/dev/vd*`.

2.  Log on to the ECS instance and format the mounted disk.

    1.  Run the ls -l /dev/xvd\* or ls -l /dev/vd\* command to check whether the returned mount point is the same as that you have recorded.

    2.  Run the fdisk command to partition the disk and run the mkfs.ext4 command to format the disk.

        ![Format the disk](https://help-static-aliyun-doc.aliyuncs.com/assets/img/en-US/0059987261/p38212.png)

3.  Migrate the Docker data to the disk.

    If you do not want to suspend the applications that run on the node, you must migrate the applications. For more information about how to migrate applications that run in a Container Service for Swarm cluster, see [Schedule an application to specified nodes](/intl.en-US/User Guide/Applications/Schedule an application to specified nodes.md). For more information about how to migrate applications that run in a Container Service for Kubernetes \(ACK\) cluster, see [Safely Drain a Node while Respecting Application SLOs](https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/).

    1.  To ensure data integrity during the migration process, stop the Docker daemon and kubelet. You can run the service docker stop command to stop the Docker daemon and run the service kubelet stop command to stop kubelet.

    2.  Move the Docker data directory to a backup directory, such as mv /var/lib/docker /var/lib/docker\_data.

    3.  Mount the formatted disk to the /var/lib/docker and /var/lib/kubelet directories. Example:

        ```
        echo "/dev/xvdb1    /var/lib/container/     ext4    defaults        0 0" >>/etc/fstab
        echo "/var/lib/container/kubelet /var/lib/kubelet none defaults,bind 0 0" >>/etc/fstab
        echo "/var/lib/container/docker /var/lib/docker none defaults,bind 0 0" >>/etc/fstab
        mkdir /var/lib/docker
        mount -a
        ```

    4.  Migrate the Docker data that has been backed up to a directory of the formatted disk, such as mv /var/lib/docker\_data/\* /var/lib/docker/.

4.  Start the Docker daemon and kubelet, and check where the data is stored.

    1.  Run the service docker start command to start the Docker daemon and run the service kubelet start command to start kubelet.

    2.  Run the df command to check whether /var/lib/docker has been mounted to the disk. If you want to start the Kubernetes cluster, skip this step.

        ![Start the cluster](https://help-static-aliyun-doc.aliyuncs.com/assets/img/en-US/0059987261/p38133.png)

    3.  Run the docker ps command to check whether containers are lost. Restart containers as required. For example, you can restart containers that do not have the `restart:always` label.

        ![Set labels](https://help-static-aliyun-doc.aliyuncs.com/assets/img/en-US/0059987261/p38134.png)

5.  If a container has been migrated to other nodes, you can schedule it back to the node to which you have attached the disk.

    For more information, see [Container Service for Kubernetes](https://www.alibabacloud.com/zh/product/container-service).


