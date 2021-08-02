# FAQ about FlexVolume

This topic provides answers to some frequently asked questions about the use of volumes.

-   [What do I do when a volume fails to be mounted?](#section_dpb_h3t_h2b)
-   [How can I view the log related to storage services?](#section_wrg_q3t_h2b)
-   [What do I do if I find Kubelet contains pod log that is not managed by ACK?](#section_u3t_r77_ctv)

**FAQ about disks**

-   [Why does a timeout error occur when I mount a disk to a pod?](#p_9ip_0tb_o7e)
-   [Why does a size error occur when I mount a disk to a pod?](#p_9ee_ydd_m8g)
-   [Why does a zone error occur when I mount a disk to a pod?](#b_jo1_rxy_3um)
-   [Why does an input/output error occur in a disk after a system upgrade?](#p_8bx_120_0x5)
-   [Why does the system prompt "The specified disk is not a portable disk" when I unmount a disk?](#p_753_h0p_4vv)
-   [Why does the system prompt "had volume node affinity conflict" when I launch a pod that has a disk mounted?](#p_lbx_etz_a0k)
-   [Why does the system prompt "can't find disk" when I launch a pod that has a disk mounted?](#p_h54_zl8_8lx)
-   [Why does the system prompt "disk size is not supported" when I dynamically provision a PV?](#b_0jl_4zq_rn1)

**FAQ about NAS**

-   [Why does the system take a long time period to mount a NAS file system to a pod?](#p_02o_oix_evx)
-   [Why does a timeout error occur when I mount a NAS file system to a pod?](#p_gdi_g3x_fee)
-   [Why does the system prompt "chown: option not permitted" when I mount a NAS file system?](#p_r5b_5cr_i0p)
-   [Why do I fail to mount a NAS file system as a PV?](#p_959_byv_oy5)

**FAQ about OSS**

-   [Why does an OSS bucket fail to be mounted?](#p_hck_sx6_if8)
-   [Why does the OSS bucket directory that is mounted to a container become unavailable after the cluster that runs the pod is upgraded?](#p_ndo_474_6sc)

## What do I do when a volume fails to be mounted?

You must make sure that FlexVolume and the alicloud-disk-controller plug-in are installed.

**Method 1: Check whether FlexVolume is installed**

Run the following command on a master node to query the pods:

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

Check whether the pod that runs FlexVolume is in the Running state. Check whether the number of running FlexVolume pods in a cluster equals the number of nodes that run these pods in the cluster.

If the FlexVolume pods are not in the Running state, troubleshoot the issue based on the log of the FlexVolume plug-in.

**Method 2: Check whether the alicloud-disk-controller plug-in is installed**

To mount a disk as a dynamically provisioned persistent volume \(PV\), you must install the alicloud-disk-controller plug-in. Run the following command to query the pods:

```
kubectl get pod -n kube-system | grep alicloud-disk
```

The following output is returned:

```
alicloud-disk-controller-8679c9fc76-lq6zb     1/1 Running   0   7d           
```

If the pod is not in the Running state, troubleshoot the issue based on the log of the plug-in.

## How can I view the log related to storage services?

You can view the logs of FlexVolume, the provisioner plug-in, and Kubelet.

**Method 1: Print the log of FlexVolume on Master Node 1**

Run the following command to view the pod on which an error occurs:

```
kubectl get pod -n kube-system | grep flexvolume
```

Run the following command to print the log of the pod that has an error:

```
kubectl logs flexvolume-4wh8s -n kube-system
kubectl describe pod flexvolume-4wh8s -n kube-system
```

**Note:** The last several entries of the returned pod information indicate the state of the pod. You can analyze the error based on the log data.

View the logs of the disk, Apsara Storage NAS \(NAS\), and Object Storage Service \(OSS\) drivers.

Run the following command to print the PV log on the host node. If a PV fails to be mounted to a pod, check the IP address of the node where the pod is located.

```
kubectl describe pod nginx-97dc96f7b-xbx8t | grep Node
```

The following output is returned:

```
Node: cn-hangzhou.i-bp19myla3uvnt6zihejb/192.168.XX.XX
Node-Selectors:  <none>
```

Log on to the node and print the logs of the disk, NAS, and OSS drivers.

```
ssh 192.168.XX.XX
ls /var/log/alicloud/flexvolume*
```

The following output is returned:

```
flexvolume_disk.log  flexvolume_nas.log  flexvolume_o#ss.log
```

**Method 2: Print the log of the provisioner plug-in on Master Node 1**

Run the following command to view the pod on which an error occurs:

```
kubectl get pod -n kube-system | grep alicloud-disk
```

Run the following command to print the log of the pod that has an error:

```
kubectl logs alicloud-disk-controller-8679c9fc76-lq6zb -n kube-system
kubectl describe pod alicloud-disk-controller-8679c9fc76-lq6zb -n kube-system
```

**Note:** The last several entries of the returned pod information indicate the state of the pod. You can analyze the error based on the log data.

**Method 3: Print the log of Kubelet**

If a PV fails to be mounted to a pod, check the IP address of the node where the pod is located.

```
kubectl describe pod nginx-97dc96f7b-xbx8t | grep Node
```

The following output is returned:

```
Node: cn-hangzhou.i-bp19myla3uvnt6zihejb/192.168.XX.XX
Node-Selectors:  <none>
```

Log on to the node and print the log of Kubelet.

```
ssh 192.168.XX.XX
journalctl -u kubelet -r -n 1000 &> kubelet.log
```

**Note:** In the preceding command, -n specifies the number of log entries to return.

The preceding steps describe how to print the logs of the FlexVolume, provisioner, and Kubelet plug-ins. If the problem persists, contact the technical support team of Alibaba Cloud and provide related log information.

## What do I do if I find Kubelet contains pod log that is not managed by ACK?

The pod exceptionally exits. Some mount targets are not removed when the system deletes the pod. As a result, the system fails to delete the pod. Kubelet cannot collect all volume garbage. You must remove invalid mount targets manually or by executing an automated script.

Run the following script on the failed node to remove invalid mount targets:

```
wget https://raw.githubusercontent.com/AliyunContainerService/kubernetes-issues-solution/master/kubelet/kubelet.sh
sh kubelet.sh
```

## FAQ about disks

**Why does a timeout error occur when I mount a disk to a pod?**

If the node where the pod runs is manually added, this issue may be caused by insufficient Security Token Service \(STS\) permissions. You must manually grant the Resource Access Management \(RAM\) permissions. For more information, see [Bind an instance RAM role](/intl.en-US/Security/Manage identities and permissions/Use RAM roles to control resource access/Bind an instance RAM role.md).

**Why does a size error occur when I mount a disk to a pod?**

To create a disk, the disk size must meet the following requirements.

**Note:**

-   A basic disk must have a minimum capacity of 5 GiB.
-   An ultra disk must have a minimum capacity of 20 GiB.
-   A standard SSD must have a minimum capacity of 20 GiB.

**Why does a zone error occur when I mount a disk to a pod?**

To mount a disk to an Elastic Compute Service \(ECS\) instance, the disk and the ECS instance must be deployed in the same region and zone.

**Why does an input/output error occur in a disk after a system upgrade?**

1.  Upgrade FlexVolume to V1.9.7-42e8198 or later.
2.  Recreate the pod that has the error.

Run the following command to upgrade FlexVolume:

```
kubectl set image daemonset/flexvolume acs-flexvolume=registry.cn-hangzhou.aliyuncs.com/acs/flexvolume:v1.9.7-42e8198 -n kube-system
```

To obtain the latest FlexVolume version, log on to the Container Registry console. In the left-side navigation pane, click **Search**. On the Search page, enter acs/flexvolume in the search bar.

**Why does the system prompt "The specified disk is not a portable disk" when I unmount a disk?**

The disk is billed on a subscription basis, or you accidentally switch the billing method of the disk to subscription when you upgrade the ECS instance that is associated with the disk. To resolve this problem, change the billing method of the disk to pay-as-you-go.

**Why do I fail to launch a pod that has a disk mounted and the system prompts "had volume node affinity conflict"?**

You have set the nodeaffinity attribute for the PV. The value of the attribute is different from that of the pod. Therefore, the pod cannot be scheduled to the expected node. Modify the nodeaffinity attribute of the PV or pod to ensure that the PV and pod use the same value.

**Why does the system prompt "can't find disk" when I launch a pod that has a disk mounted?**

You entered an invalid value for diskid when you set the parameters of the PV, or your account does not have permissions to modify diskid. The disk may not belong to the current account. To resolve this problem, modify the diskid parameter.

**Why does the system prompt "disk size is not supported" when I dynamically provision a PV?**

The size of the disk that you specified in the PVC is invalid. The disk size must be at least 20 GiB. You must change the size of the disk that is specified in the PVC to a valid value.

## FAQ about NAS

**Why does the system take a long time period to mount a NAS file system to a pod?**

If the NAS file system contains a large amount of data and you set the chmod parameter in the mounting template, this issue may occur. We recommend that you remove the chmod parameter.

**Why does a timeout error occur when I mount a NAS file system to a pod?**

Make sure that the mount target of the NAS file system and the cluster are deployed in the same virtual private cloud \(VPC\).

**Why does the system prompt "chown: option not permitted" when I mount a NAS file system?**

Your container does not have permissions to use the specified NAS file system. You must launch the container with root privileges.

**Why do I fail to mount a NAS file system as a PV?**

You fail to mount a NAS file system as a PV and the system prompts the following error:

```
Unable to mount volumes for pod "dp-earnings-pod_default(906172c6-3d68-11e8-86e0-00163e0084d3)": timeout expired waiting for volumes to attach/mount for pod "default"/"dp-earnings-pod". list of unattached/unmounted volumes=[vol1 vol2]
```

The FlexVolume plug-in is not installed. To resolve this problem, install the FlexVolume plug-in. For more information, see [Install and upgrade FlexVolume](/intl.en-US/User Guide for Kubernetes Clusters/Storage management-Flexvolume/Install and upgrade FlexVolume.md).

## FAQ about OSS

**Why does an OSS bucket fail to be mounted?**

-   Check whether the AccessKey information is invalid.

-   Check whether the URL used to mount the OSS bucket is accessible over the network.

**Why does the OSS bucket directory that is mounted to a container become unavailable after the cluster that runs the pod is upgraded?**

If you upgrade your Container Service for Kubernetes \(ACK\) cluster or restart Kubelet, the container network is restarted. This causes the OSSFS process to restart.

After the OSSFS process restarts, the mapping between the host and container directory becomes invalid. In this case, restart the container or recreate the pod. You can configure the health check feature and enable the system to automatically restart a container or pod.

For more information about OSS, see [Mount OSS volumes](/intl.en-US/User Guide for Kubernetes Clusters/Storage management-Flexvolume/OSS volumes/Mount OSS volumes.md).

