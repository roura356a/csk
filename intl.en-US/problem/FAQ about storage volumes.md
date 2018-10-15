# FAQ about storage volumes {#concept_vdv_xht_h2b .concept}

## Storage volumes cannot be mounted {#section_dpb_h3t_h2b .section}

**Check if flexvolume is installed.**

Execute the following command on the master node:

```
# kubectl get pod -n kube-system | grep flexvolume

flexvolume-4wh8s            1/1       Running   0          8d
flexvolume-65z49            1/1       Running   0          8d
flexvolume-bpc6s            1/1       Running   0          8d
flexvolume-l8pml            1/1       Running   0          8d
flexvolume-mzkpv            1/1       Running   0          8d
flexvolume-wbfhv            1/1       Running   0          8d
flexvolume-xf5cs            1/1       Running   0          8d

```

Check if the flexvolume pod status is Running and if the number of running flexvolume pods is the same as the number of nodes.

If not, see [../../../../dita-oss-bucket/SP\_235/DNcsk1877477/EN-US\_TP\_15789.md\#](../../../../intl.en-US/User Guide/Kubernetes cluster/Storage/Install the plug-in.md#).

If the flexvolume pod status is not running, see the running log analysis of the plug-in.

**Check if the dynamic storage plug-in is installed**

To use the dynamic storage function of a cloud disk, execute the following command to verify the dynamic storage plug-in is installed:

```
# kubectl get pod -n kube-system | grep alicloud-disk

alicloud-disk-controller-8679c9fc76-lq6zb     1/1 Running   0   7d

```

If not, see [../../../../dita-oss-bucket/SP\_235/DNcsk1877477/EN-US\_TP\_15789.md\#](../../../../intl.en-US/User Guide/Kubernetes cluster/Storage/Install the plug-in.md#).

If the dynamic storage plug-in status is not running, see the running log analysis of the plug-in.

## How to view types of storage logs? {#section_wrg_q3t_h2b .section}

**View flexvolume logs by executing commands on the master1 node**

Execute the following get command to view the error pod:

```
# kubectl get pod -n kube-system | grep flexvolume
```

Execute the following log command to view the log for the error pod:

```
# kubectl logs flexvolume-4wh8s -n kube-system
# kubectl describe pod flexvolume-4wh8s -n kube-system

# The last several lines in the pod description are the descriptions of pod running status. You can analyze pod errors based on the descriptions.
```

View drive logs of the cloud disk, Network Attached Storage \(NAS\), and Object Storage Service \(OSS\):

```
# View the persistent logs on the host node;
# If a pod mount fails, view the address of the node on which the pod resides:

# kubectl describe pod nginx-97dc96f7b-xbx8t | grep Node
Node: cn-hangzhou.i-bp19myla3uvnt6zihejb/192.168.247.85
Node-Selectors:  <none>

# Log on to the node to view logs:

# ssh 192.168.247.85
# ls /var/log/alicloud/flexvolume*
flexvolume_disk.log  flexvolume_nas.log  flexvolume_o#ss.log

You can see logs mounted on the cloud disk, NAS, and OSS;
```

**View provsioner plug-in logs by executing commands on the master1 node**

Execute the following get command to view the error pod:

```
# kubectl get pod -n kube-system | grep alicloud-disk
```

Execute the log command to view the log for the error pod:

```
# kubectl logs alicloud-disk-controller-8679c9fc76-lq6zb -n kube-system
# kubectl describe pod alicloud-disk-controller-8679c9fc76-lq6zb -n kube-system

# The last several lines in the pod description are the descriptions of pod running status. You can analyze pod errors based on the descriptions.
```

**View Kubelet logs**

```
# If a pod mount fails, view the address of the node on which the pod resides:

# kubectl describe pod nginx-97dc96f7b-xbx8t | grep Node
Node: cn-hangzhou.i-bp19myla3uvnt6zihejb/192.168.247.85
Node-Selectors: <none>

# Log on to the node to view kubelet logs:

# ssh 192.168.247.85
# journalctl -u kubelet -r -n 1000 &> kubelet.log

# The value of -n indicates the number of log lines that you expect to see;

```

The above are methods to obtain error logs of flexvolume, provsioner, and kubelet. If the logs cannot help you to repair the status, contact Alibaba Cloud technical support with the logs.

## FAQ about cloud disks {#section_eb2_cjt_h2b .section}

**Cloud disk mount fails with timeout errors**

If the node is added manually, the failure may be caused by problem about Security Token Service \(STS\) permissions. You need to manually configure Resource Access Management \(RAM\) permissions: [Use the instance RAM role in the console](../../../../intl.en-US/User Guide/Instances/Instance RAM roles/Use the instance RAM role in the console.md#).

**Cloud disk mount fails with size errors**

The following are size requirements for creating a cloud disk:

**Note:** 

-   Basic cloud disk: Minimum 5Gi
-   Ultra cloud disk: Minimum 20Gi
-   SSD cloud disk: Minimum 20Gi

**Cloud disk mount fails with zone errors**

When the ECS mounts a cloud disk, they must be in the same zone under the same region. Otherwise, the cloud disk cannot be mounted successfully.

**After your system is upgraded, the cloud disk sometimes reports input/output error**

1.  Upgrade flexvolume to v1.9.7-42e8198 or later.
2.  Rebuild pods that have already gone wrong.

Upgrading command:

```
# kubectl set image daemonset/flexvolume acs-flexvolume=registry.cn-hangzhou.aliyuncs.com/acs/flexvolume:v1.9.7-42e8198 -n kube-system
```

Flexvolume version information: To obtain the latest version of flexvolume, log on to the container image service console, click **Image search** in the left-side navigation pane, and search for acs/flexvolume.

## FAQ about NAS {#section_utx_njt_h2b .section}

**NAS mount time is too long**

If the NAS volume contains a large amount of files and the chmod parameter is configured in the mount template, the mount time may be too long. To solve this problem, remove the chmod parameter.

**NAS mount fails with the timeout error**

Check if the NAS mount point and the cluster are within the same Virtual Private Cloud \(VPC\). If not, NAS cannot be mounted.

## FAQ about OSS {#section_mvn_pjt_h2b .section}

**OSS mount fails**

Check if the AK used is correct.

