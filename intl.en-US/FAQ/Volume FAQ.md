# Volume FAQ {#concept_vdv_xht_h2b .concept}

## What do I do if a volume cannot be mounted to the Kubernetes cluster? {#section_dpb_h3t_h2b .section}

**Check whether the flexvolume plugin is installed**

Run the following command on the Master node:

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

If the flexvolume plugin is installed, check whether flexvolume pods is running and that the number of running pods is the same as the number of nodes.

If no flexvolume plugin is installed, see [Install the plug-in](../../../../reseller.en-US/User Guide/Kubernetes cluster/Storage/Install the plug-in.md#).

If the flexvolume pod is not running, see the running logs of the plugin to further analyze the cause.

**Check whether the dynamic storage plugin is installed**

To use the dynamic storage function of a cloud disk, you must install the dynamic storage plugin. Run the following command to check whether the dynamic storage plugin is installed:

```
# kubectl get pod -n kube-system | grep alicloud-disk

alicloud-disk-controller-8679c9fc76-lq6zb     1/1 Running   0   7d

```

If no dynamic storage plugin is installed, see [Install the plug-in](../../../../reseller.en-US/User Guide/Kubernetes cluster/Storage/Install the plug-in.md#).

If the pod is not running, see the running logs of the plugin to further analyze the cause.

## How do I view storage logs? {#section_wrg_q3t_h2b .section}

**View flexvolume logs by running commands on Master1 node**

Run the get command to view the error pod as follows:

```
# kubectl get pod -n kube-system | grep flexvolume
```

Run the log command to view the logs of the error pod as follows:

```
# kubectl logs flexvolume-4wh8s -n kube-system
# kubectl describe pod flexvolume-4wh8s -n kube-system

#The last several lines of the pod description describe the status of the pod. You can analyze errors according to the status of the pod.
```

View the driver logs of a cloud disk, NAS, and OSS:

```
# View the persistent logs on the host node.
# If a volume cannot be mounted to a pod, run the following command to view the address of the node where the pod resides:

# kubectl describe pod nginx-97dc96f7b-xbx8t | grep Node
Node: cn-hangzhou.i-bp19myla3uvnt6zihejb/192.168.247.85
Node-Selectors:  <none>

# Log on to the node to view logs.

# ssh 192.168.247.85
# ls /var/log/alicloud/flexvolume*
flexvolume_disk.log  flexvolume_nas.log  flexvolume_o#ss.log

The logs mounted to the cloud disk, NAS, and OSS are displayed.
```

**Run the following commands on Master1 node to view provsioner plugin logs**

Run the get command to view the error pod as follows:

```
# kubectl get pod -n kube-system | grep alicloud-disk
```

Run the log command to view the logs of the error pod as follows:

```
# kubectl logs alicloud-disk-controller-8679c9fc76-lq6zb -n kube-system
# kubectl describe pod alicloud-disk-controller-8679c9fc76-lq6zb -n kube-system

#The last several lines of the pod description describe the status of the pod. You can analyze errors according to the status of the pod.
```

**View kubelet logs**

```
# If a volume cannot be mounted to a pod, run the following command to view the address of the node where the pod resides:

# kubectl describe pod nginx-97dc96f7b-xbx8t | grep Node
Node: cn-hangzhou.i-bp19myla3uvnt6zihejb/192.168.247.85
Node-Selectors:  <none>

# Log on to the node to view kubelet logs:

# ssh 192.168.247.85
# journalctl -u kubelet -r -n 1000 &> kubelet.log

# The value of -n indicates the number of log lines that you expect to display.

```

The preceding content describes the methods to obtain the logs of errors that occurred to flexvolume, provsioner, and kubelet. If you cannot fix the errors according to the logs, we recommend that you submit a ticket with the log information for further consultation.

## Cloud disk volume FAQ {#section_eb2_cjt_h2b .section}

**What do I do if a cloud disk fails to be mounted to the cluster and a timeout error is displayed?**

If the cluster node is added manually, this problem may be caused by insufficient STS permissions. We recommend that you manually configure RAM permissions. For more information, see [Use the instance RAM role in the console](../../../../reseller.en-US/User Guide/Instances/Instance RAM roles/Use the instance RAM role in the console.md#).

**What do I do if a cloud disk fails to be mounted to the cluster and a Size error is displayed?**

Depending on the type of cloud disk you create, you must confirm that the following requirements are met:

**Note:** 

-   The minimum capacity of a basic cloud disk is 5 GiB.
-   The minimum capacity of an Ultra disk is 20 GiB.
-   The minimum capacity of an SSD disk is 20 GiB.

**What do I do if a cloud disk fails to be mounted to the cluster and a zone error is displayed?**

To mount a cloud disk to your Kubernetes cluster, you must select the cloud disk that is in the same region and zone as the ECS instances used by the Kubernetes cluster.

**What do I do if the input/output error is displayed by the cloud disk after the system is upgraded?**

1.  Upgrade the flexvolume to v1.9.7-42e8198 or later.
2.  Recreate the faulty pods.

Run the following command to upgrade the flexvolume:

```
# kubectl set image daemonset/flexvolume acs-flexvolume=registry.cn-hangzhou.aliyuncs.com/acs/flexvolume:v1.9.7-42e8198 -n kube-system
```

To obtain the latest flexvolume version, log on to the Container Registry console, click **Search** in the left-side navigation pane, and search for acs/flexvolume.

## NAS volume FAQ {#section_utx_njt_h2b .section}

**What do I do if it takes a long time to mount a NAS file system to a Kubernetes cluster?**

This problem may occur if the NAS file system contains a large amount of data and you set the chmod parameter in the mount template. We recommend that you remove the chmod parameter setting.

**What do I do if a NAS file system fails to be mounted to a Kubernetes cluster and a timeout error is displayed?**

Check whether the NAS mount point and the cluster are in the same VPC. Otherwise, the NAS file system cannot be mounted to the cluster.

## OSS volume FAQ {#section_mvn_pjt_h2b .section}

**What do I do if an OSS bucket fails to be mounted to a Kubernetes cluster?**

-   Check whether the submitted Access Key is valid.

-   Check whether the URL used to mount the OSS bucket is accessible through network.

**What do I do if the OSS mount directory within the container becomes unavailable after the cluster is upgraded?**

If you upgrade your Kubernetes cluster or restart a kubelet, the ossfs process restarts because the container network restarts.

After the ossfs process restarts, the mapping to the container directory from your host becomes invalid. In this case, you need to restart the container or recreate the pod.

We recommend that you configure the liveness probe for heath checks so that the container or pod can automatically restart when such a problem occurs. For more information, see [Use Alibaba Cloud OSS volumes](../../../../reseller.en-US/User Guide/Kubernetes cluster/Storage/Use Alibaba Cloud OSS volumes.md#).

