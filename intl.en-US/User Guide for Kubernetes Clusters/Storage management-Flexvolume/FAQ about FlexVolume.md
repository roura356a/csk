---
keyword: [volume, frequently asked questions, FAQ]
---

# FAQ about FlexVolume

This topic provides answers to some frequently asked questions about disk volumes, Apsara File Storage NAS \(NAS\) volumes, and Object Storage Service \(OSS\) volumes.

|Type|Question|
|----|--------|
|FAQ|-   [What do I do when a volume fails to be mounted?](#section_dpb_h3t_h2b)
-   [How do I view the log related to storage services?](#section_wrg_q3t_h2b)
-   [What do I do if I find kubelet contains pod log that is not managed by ACK?](#section_u3t_r77_ctv) |
|FAQ about disk volumes|-   [t2100934.md\#section\_td0\_7vk\_92o]()
-   [t2100934.md\#section\_n33\_3bc\_3jb]()
-   [t2100934.md\#section\_vxc\_7oy\_i24]()
-   [t2100934.md\#section\_f1w\_yob\_goz]()
-   [t2100934.md\#section\_fay\_4ao\_0ik]()
-   [t2100934.md\#section\_kwt\_2d5\_r0u]()
-   [t2100934.md\#section\_vi0\_dfh\_v7w]() |
|FAQ about NAS volumes|-   [t2101096.md\#section\_td0\_7vk\_92o]()
-   [t2101096.md\#section\_n33\_3bc\_3jb]()
-   [t2101096.md\#section\_vxc\_7oy\_i24]()
-   [t2101096.md\#section\_f1w\_yob\_goz]()
-   [t2101096.md\#section\_xgz\_7fw\_p7e]() |
|FAQ about OSS volumes|-   [t2101108.md\#section\_td0\_7vk\_92o]()
-   [t2101108.md\#section\_n33\_3bc\_3jb]() |

## What do I do when a volume fails to be mounted?

Check whether FlexVolume and the plug-in for dynamic provisioning are installed.

**Method 1: Check whether FlexVolume is installed**

Run the following command to query information about the pod:

```
kubectl get pod -n kube-system | grep flexvolume
```

Expected output:

```
flexvolume-4wh8s            1/1       Running   0          8d
flexvolume-65z49            1/1       Running   0          8d
flexvolume-bpc6s            1/1       Running   0          8d
flexvolume-l8pml            1/1       Running   0          8d
flexvolume-mzkpv            1/1       Running   0          8d
flexvolume-wbfhv            1/1       Running   0          8d
flexvolume-xf5cs            1/1       Running   0          8d   
```

Check whether the FlexVolume pods are in the Running state. Check whether the number of running FlexVolume pods equals the number of nodes in the cluster.

If the FlexVolume pods are not in the Running state, troubleshoot the issue based on the log of the FlexVolume plug-in.

**Method 2: Check whether the plug-in for dynamic provisioning is installed**

To mount a dynamically provisioned disk volume, you must install the plug-in for dynamic provisioning. Run the following command to query the pod on which the plug-in runs:

```
kubectl get pod -n kube-system | grep alicloud-disk
```

Expected output:

```
alicloud-disk-controller-8679c9fc76-lq6zb     1/1 Running   0   7d           
```

If the pod is not in the Running state, troubleshoot the issue based on the log of the plug-in.

## How do I view the log related to storage services?

You can view the logs of FlexVolume, the provisioner plug-in, and kubelet.

**Method 1: Print the log of FlexVolume on Master Node 1**

Run the following command to view the pod on which an error occurs:

```
kubectl get pod -n kube-system | grep flexvolume
```

Run the following command to print the log of the pod on which the error occurs:

```
kubectl logs flexvolume-4wh8s -n kube-system
kubectl describe pod flexvolume-4wh8s -n kube-system
```

**Note:** The last several entries of the returned pod information indicate the status of the pod. You can analyze the error based on the log data.

View the logs of the disk, NAS, and OSS drivers.

Run the following command to print the PV log on the host node. If a PV fails to be mounted to a pod, check the IP address of the node where the pod is deployed.

```
kubectl describe pod nginx-97dc96f7b-xbx8t | grep Node
```

Expected output:

```
Node: cn-hangzhou.i-bp19myla3uvnt6zi****/192.168.XX.XX
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

Run the following command to print the log of the pod on which the error occurs:

```
kubectl logs alicloud-disk-controller-8679c9fc76-lq6zb -n kube-system
kubectl describe pod alicloud-disk-controller-8679c9fc76-lq6zb -n kube-system
```

**Note:** The last several entries of the returned pod information indicate the status of the pod. You can analyze the error based on the log data.

**Method 3: Print the log of kubelet**

If a PV fails to be mounted to a pod, check the IP address of the node where the pod is deployed.

```
kubectl describe pod nginx-97dc96f7b-xbx8t | grep Node
```

Expected output:

```
Node: cn-hangzhou.i-bp19myla3uvnt6zi****/192.168.XX.XX
Node-Selectors:  <none>
```

Log on to the node and print the log of kubelet.

```
ssh 192.168.XX.XX
journalctl -u kubelet -r -n 1000 &> kubelet.log
```

**Note:** In the preceding command, -n specifies the number of log entries to return.

The preceding steps describe how to print the logs of the FlexVolume, provisioner, and kubelet plug-ins. If the problem persists, contact the technical support team of Alibaba Cloud and provide related log information.

## What do I do if I find kubelet contains pod log that is not managed by ACK?

The pod exceptionally exits. Some mount targets are not removed when the system deletes the pod. As a result, the system fails to delete the pod. kubelet cannot collect all volume garbage. You must remove invalid mount targets manually or by executing an automated script.

Run the following script on the failed node to remove invalid mount targets:

```
wget https://raw.githubusercontent.com/AliyunContainerService/kubernetes-issues-solution/master/kubelet/kubelet.sh
sh kubelet.sh
```

