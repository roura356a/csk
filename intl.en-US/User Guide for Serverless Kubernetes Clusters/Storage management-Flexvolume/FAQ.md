# FAQ

This topic provides answers to some commonly asked questions about the use of persistent volumes \(PVs\).

## What can I do if a PV cannot be mounted?

**Check whether FlexVolume is installed**

Run the following command on a master node to obtain the pod information:

```
kubectl get pod -n kube-system | grep flexvolume
```

The following output is returned:

```
flexvolume-4wh8s            1/1       Running   0          8d
flexvolume-65z49            1/1       Running   0          8d
flexvolume-bpc6s            1/1       Running   0          8d
flexvolume-l8pml            1/1       Running   0          8d
flexvolume-mzkpv            1/1       Running   0          8d
flexvolume-wbfhv            1/1       Running   0          8d
flexvolume-xf5cs            1/1       Running   0          8d   
```

Check whether the pod on which FlexVolume is installed is in the running state. Check whether the number of the running FlexVolume pods in a cluster is the same as the number of nodes that run these pods in the cluster.

If FlexVolume is not installed, install the plug-in. For more information, see [t15789.md\#](/intl.en-US/User Guide for Kubernetes Clusters/Storage management-Flexvolume/Install plug-ins.md).

If the FlexVolume pods are not in the running state, troubleshoot the issue based on the logs of the FlexVolume plug-in.

**Check whether the alicloud-disk-controller plug-in is installed**

To use a cloud disk as a dynamically provisioned PV, you must install the alicloud-disk-controller plug-in. Run the following command to view the pod information:

```
kubectl get pod -n kube-system | grep alicloud-disk
```

The following output is returned:

```
alicloud-disk-controller-8679c9fc76-lq6zb     1/1 Running   0   7d           
```

If the plug-in is not installed, install the plug-in. For more information, see [t15789.md\#](/intl.en-US/User Guide for Kubernetes Clusters/Storage management-Flexvolume/Install plug-ins.md).

If a pod is not in the running state, troubleshoot the issue based on the logs of the plug-in.

## How can I view storage logs?

**Run the specified commands on Master node 1 to view FlexVolume logs**

Run the following command to view the pod on which an error occurs:

```
kubectl get pod -n kube-system | grep flexvolume
```

Run the following command to view the logs of the pod that has an error:

```
kubectl logs flexvolume-4wh8s -n kube-system
kubectl describe pod flexvolume-4wh8s -n kube-system
```

**Note:** The last several entries of the returned pod information indicate the pod state. You can analyze the error based on the response.

View the logs of the cloud disk, NAS, and Object Storage Service \(OSS\) drivers.

Run the following command to view the PV logs on the host node: If a PV failed to be mounted to a pod, check the address of the node where the pod is located.

```
kubectl describe pod nginx-97dc96f7b-xbx8t | grep Node
```

The following output is returned:

```
Node: cn-hangzhou.i-bp19myla3uvnt6zihejb/192.168.XX.XX
Node-Selectors:  <none>
```

Log on to the node and view the logs of the cloud disk, NAS, and OSS drivers.

```
ssh 192.168.XX.XX
ls /var/log/alicloud/flexvolume*
```

The following output is returned:

```
flexvolume_disk.log  flexvolume_nas.log  flexvolume_o#ss.log
```

**Run the specified commands on Master node 1 to view the logs of the provisioner plug-in**

Run the following command to view the pod on which an error occurs:

```
kubectl get pod -n kube-system | grep alicloud-disk
```

Run the following command to view the logs of the pod that has an error:

```
kubectl logs alicloud-disk-controller-8679c9fc76-lq6zb -n kube-system
kubectl describe pod alicloud-disk-controller-8679c9fc76-lq6zb -n kube-system
```

**Note:** The last several entries of the returned pod information indicate the pod state. You can analyze the error based on the response.

**View Kubelet logs**

If a PV failed to be mounted to a pod, check the address of the node where the pod is located.

```
kubectl describe pod nginx-97dc96f7b-xbx8t | grep Node
```

The following output is returned:

```
Node: cn-hangzhou.i-bp19myla3uvnt6zihejb/192.168.XX.XX
Node-Selectors:  <none>
```

Log on to the node to view kubelet logs.

```
ssh 192.168.XX.XX
journalctl -u kubelet -r -n 1000 &> kubelet.log
```

**Note:** In the preceding command, -n specifies the required number of log entries to return.

The preceding steps describe how to obtain the error logs of the FlexVolume, provisioner, and Kubelet plug-ins. If the issue cannot be fixed based on the logs, contact the technical support team of Alibaba Cloud and provide related log information.

## FAQ of cloud disks

**What can I do if a timeout error has occurred when I mounted a PV to a pod?**

If the node that runs the pod is manually added, this issue may be caused by insufficient Security Token Service \(STS\) permissions. We recommend that you manually grant the RAM permissions. For more information, see [Bind an instance RAM role](/intl.en-US/Security/Instance RAM roles/Bind an instance RAM role.md).

**What can I do if a size error has occurred when I mounted a PV to a pod?**

To create a cloud disk, the disk size must meet the following requirements:

**Note:**

-   A basic disk must have a minimum capacity of 5 GiB.
-   An ultra disk must have a minimum capacity of 20 GiB.
-   A standard SSD must have a minimum capacity of 20 GiB.

**What can I do if a zone error has occurred when I mounted a PV to a pod?**

To mount a cloud disk to an Elastic Compute Service \(ECS\) instance, the cloud disk and the ECS instance must be deployed in the same region and zone.

**What can I do if an input/output error has occurred in a cloud disk after a system upgrade?**

1.  Upgrade FlexVolume to v1.9.7-42e8198 or later.
2.  Recreate the pod that has the error.

Run the following command to upgrade FlexVolume:

```
kubectl set image daemonset/flexvolume acs-flexvolume=registry.cn-hangzhou.aliyuncs.com/acs/flexvolume:v1.9.7-42e8198 -n kube-system
```

To obtain the latest FlexVolume version, log on to the Container Registry console. In the left-side navigation pane, choose Image Hub \> **Search**. On the Search page, select User Public Image from the drop-down list and enter acs/flexvolume in the search bar.

## FAQ about NAS

**What can I do if it took a long time to mount a NAS file system to pod?**

If the NAS file system contains a large amount of data and you set the chmod parameter in the mounting template, this issue may occur. We recommend that you remove the chmod parameter.

**What can I do if a timeout error has occurred when I mounted a NAS file system to a pod?**

Make sure that the mount target of the NAS file system and the cluster are deployed in the same virtual private cloud \(VPC\).

## FAQ of OSS

**What can I do if an OSS bucket failed to be mounted?**

-   Check whether the AccessKey information is invalid.

-   Check whether the URL used to mount the OSS bucket is accessible over the network.

**What can I do if the OSS bucket directory that is mounted to a container is unavailable after an upgrade of the cluster that runs the pod?**

If you upgrade your Container Service for Kubernetes \(ACK\) cluster or restart a kubelet, the container network is restarted. This causes the OSSFS process to restart.

After the OSSFS process restarts, the mapping between the host and container directory becomes invalid. In this case, restart the container or recreate the pod. You can configure the health check feature to automatically restart a container or pod.

For more information about OSS, see [Use an OSS volume](/intl.en-US/User Guide for Kubernetes Clusters/Storage management-Flexvolume/OSS volumes/Use an OSS volume.md).

