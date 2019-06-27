# Mount a disk to the Docker data directory {#concept_opp_flh_4gb .concept}

This topic describes how to mount a disk to the Docker data directory. If the number of containers or images that run on an ECS instance increases constantly, the ECS instance disk capacity may be insufficient. In this case, you can expand the Docker data directory by mounting a disk to the ECS instance.

## Docker data directory {#section_s2h_wlh_4gb .section}

Docker data is stored in disks through a union file system \(UnionFS\). The default container data and image data of Docker is stored in the /var/lib/docker directory. You can run the du command to view the disk space size occupied by this directory.

``` {#codeblock_vzv_kl1_00s}
# du -h --max-depth=0 /var/lib/docker
7.9G    /var/lib/docker
```

## Scenarios {#section_fwg_mvh_4gb .section}

Generally, a Docker image occupies a large amount of disk space. If you want to use multiple Docker images or a large number of containers, you must mount a disk to the Docker data directory to ensure sufficient disk capacity is available.

## Mount a disk {#section_ivb_xxh_4gb .section}

To mount a disk to the Docker data directory, follow these steps:

1.  Create a disk and mount it to the target ECS instance for which you want to expand the disk capacity.
    1.  Log on to the [ECS console](https://partners-intl.console.aliyun.com/#/ecs) to create a disk.
    2.  In the left-side navigation pane, click **Instances**.
    3.  Click the target ECS instance ID.
    4.  In the left-side navigation pane, click **Disks**.
    5.  In the upper-right corner, click **Mount**.
    6.  In the displayed dialog box, select the created disk from the target disk drop-down list, and then click **OK**.
    7.  Click **Mount** to mount the new disk to the target ECS instance, and record the new disk mounting point which is in the format of `/dev/xvd*` or `/dev/vd*`.
2.  Log on to the target ECS instance to format the new disk.
    1.  Run the ls -l /dev/xvd\* or ls -l /dev/vd\* command to verify whether a disk that has the recorded mounting point has been mounted to the ECS instance.
    2.  Run the fdisk command to partition the new disk, and then run the mkfs.ext4 command to format the new disk.

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/119956/156161409838212_en-US.png)

3.  Migrate the Docker data to the new disk.

    If you do not want to suspend the applications that run on the target ECS instance, you must migrate the applications. For how to migrate applications on a Swarm cluster, see [Schedule an application to specified nodes](../../../../reseller.en-US/User Guide/Applications/Schedule an application to specified nodes.md#). For how to migrate applications on a Kubernetes cluster, see [Safely drain a node while respecting application SLOs](https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/).

    1.  To ensure that data can be migrated, run the service docker stop command to stop Docker daemon, and run the service kubelet stop command to stop kubelet.
    2.  Migrate the Docker directory data to a backup directory. For example, mv /var/lib/docker /var/lib/docker\_data.
    3.  Mount the new disk to the /var/lib/docker and /var/lib/kubelet directories. For example,

        ``` {#codeblock_q6z_2am_j5b}
        echo "/dev/xvdb1    /var/lib/container/     ext4    defaults        0 0" >>/etc/fstab
        echo "/var/lib/container/kubelet /var/lib/kubelet none defaults,bind 0 0" >>/etc/fstab
        echo "/var/lib/container/docker /var/lib/docker none defaults,bind 0 0" >>/etc/fstab
        
        
        mkdir /var/lib/docker
        
        mount -a
        ```

    4.  Migrate the backed up Docker data to the new disk. For example, mv /var/lib/docker\_data/\* /var/lib/docker/.
4.  Start the Docker daemon and kubelet, and check the data location.
    1.  Run the service docker start command to start the Docker daemon, and run the service kubelet start command to start kubelet.
    2.  Run the df command to verify whether /var/lib/docker has been mounted to the new disk. If you need to start the Kubernetes cluster, skip this step.

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/119956/156161409838133_en-US.png)

    3.  Run the docker ps command to check whether containers are lost. Restart containers as needed. For example, you can restart a container that has not been set the `restart:always` label.

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/119956/156161409838134_en-US.png)

5.  If a container has been migrated to other nodes, you can schedule it back to the target node to which you mounted the new disk.

For more information, see [Container Service](https://www.alibabacloud.com/product/container-service).

