# Manually add an existing instance to a specified node pool

You can call the DescribeClusterAttachScripts operation to manually add an existing instance to a specified Container Service for Kubernetes \(ACK\) cluster. This operation returns a unique executable script. You can run this script on an existing instance to add the instance to the specified ACK cluster as a node.

## Debugging

[OpenAPI Explorer automatically calculates the signature value. For your convenience, we recommend that you call this operation in OpenAPI Explorer. OpenAPI Explorer dynamically generates a sample code of the operation for different SDKs.](https://api.aliyun.com/#product=CS&api=DescribeClusterAttachScripts&type=ROA&version=2015-12-15)

## Request syntax

```
POST /clusters/ClusterId/attachscript HTTP/1.1 
Content-Type:application/json
{
  "nodepool_id" : "String",
  "format_disk" : Boolean,
  "keep_instance_name" : Boolean,
  "rds_instances" : [ "String" ],
  "arch" : "String",
  "options" : "String"
}
```

## Request parameters

|Parameter|Type|Required|Example|Description|
|---------|----|--------|-------|-----------|
|ClusterId|String|Yes|ca375a93a30474552ad2a0ebe183e\*\*\*\*|The ID of the ACK cluster. |

|Parameter|Type|Required|Example|Description|
|---------|----|--------|-------|-----------|
|nodepool\_id|String|No|np1c9229d9be2d432c93f77a88fca0\*\*\*\*|The ID of the node pool to which you want to add an existing node. This parameter allows you to add an existing node to a specified node pool.

 **Note:** If you do not specify a node pool ID, the node is added to a default node pool. |
|format\_disk|Boolean|No|false|Specifies whether to mount data disks to an existing instance when you add the instance to the cluster. You can add data disks to store the container data and images. Valid values:

 -   `true`: mounts data disks to the existing instance that you want to add. After a data disk is mounted, the original data that is stored on the disk is erased. Back up data before you mount a data disk.
-   `false`: does not mount data disks to the existing instance.

 Default value: `false`.

 How to mount a data disk:

 -   If the Elastic Compute Service \(ECS\) instances are already mounted with data disks and the file system of the last data disk is not initialized, the system automatically formats this data disk to ext4 and mounts it to /var/lib/docker and /var/lib/kubelet.
-   If no data disk is mounted to the ECS instance, the system does not purchase a new data disk. |
|keep\_instance\_name|Boolean|No|true|Specifies whether to retain the name of the existing instance when it is added to the cluster. If you do not retain the instance name, the instance is named in the `worker-k8s-for-cs-<clusterid>` format. Valid values:

 -   `true`: retains the instance name.
-   `false`: does not retain the instance name.

 Default value: `true`. |
|rds\_instances|Array of String|No|rm-xxx|The names of the ApsaraDB RDS instances. |
|arch|String|No|amd64|The CPU architecture of the node. Valid values: `amd64`, `arm`, and `arm64`.

 Default value: `amd64`.

 **Note:** This parameter is required if you want to add the existing node to a managed edge Kubernetes cluster. |
|options|String|No|\{\\"enableIptables\\": true,\\"manageRuntime\\": true,\\"quiet\\": true,\\"allowedClusterAddons\\": \[\\"kube-proxy\\",\\"flannel\\",\\"coredns\\"\]\}|The node configurations for the existing instance that you want to add as a node.

 **Note:** This parameter is required if you want to add the existing node to a managed edge Kubernetes cluster. |

## Response syntax

```
HTTP/1.1 200
Content-Type:application/json
```

## Response parameters

|Parameter|Type|Example|Description|
|---------|----|-------|-----------|
| |String|"curl http://aliacs-k8s-cn-hangzhou.oss-cn-hangzhou.aliyuncs.com/public/pkg/run/attach/1.12.6-aliyunedge.1/edgeadm -O edgeadm; chmod u+x edgeadm; ./edgeadm join --openapi-token=XXX --node-spec="\{\\"flannelIface\\":\\"eth0\\",\\"enableIptables\\":true,\\"assumeYes\\":true,\\"manageRuntime\\":true,\\"nodeNameStrategy\\":\\"hostname\\",\\"enabledAddons\\":\[\\"kube-proxy\\",\\"flannel\\",\\"coredns\\"\]\}""|The returned script that is used to add an existing instance to the ACK cluster. |

## Examples

Sample requests

```
POST /clusters/ca375a93a30474552ad2a0ebe183e****/attachscript HTTP/1.1 
Content-Type:application/json
{
  "nodepool_id" : "np1c9229d9be2d432c93f77a88fca0****",
  "format_disk" : false,
  "keep_instance_name" : true,
  "rds_instances" : [ "rm-xxx" ],
  "arch" : "amd64",
  "options" : "{\\\"enableIptables\\\": true,\\\"manageRuntime\\\": true,\\\"quiet\\\": true,\\\"allowedClusterAddons\\\": [\\\"kube-proxy\\\",\\\"flannel\\\",\\\"coredns\\\"]}"
}
```

Description of the sample request

```
When you call this operation to add an existing instance to a managed edge Kubernetes cluster, the options and arch parameters are required. The following content describes the options parameter:
```
{
	"options":"{
		"allowedClusterAddons": The name of the component. 
		"enableIptables": Specifies whether to enable iptables. Default value: true. 
		"flannelIface": the name of the network interface controller (NIC) used by Flannel. By default, this parameter is set to the name of the NIC that is specified in the default route entry of the node.  
		"gpuVersion": Specifies whether the node is a GPU-accelerated node. By default, this parameter is empty. Valid values: Nvidia_Tesla_T4, Nvidia_Tesla_P4, and Nvidia_Tesla_P100. 
		"manageRuntime": Specifies whether to use edgeadm to install and check the runtime. Default value: false. 
		"nodeNameOverride": Specifies how the node is named. A pair of quotation marks ("") indicates the host name is used as the node name. This is the default value. An asterisk (*) indicates that a random string that contains six characters is used as the node name.The string "*.XXX" indicates that a random string followed by a suffix is used as the node name. The random string contains six characters. 
		"quiet": Specifies whether to enable the silent installation mode. 
	}
}
```
```

Sample success responses

`JSON` format

```
HTTP/1.1 200 OK
```

Description of the sample response

```
If the cluster is not a managed edge Kubernetes cluster, the following output is returned:
"curl http://aliacs-k8s-cn-chengdu.oss-cn-chengdu-internal.aliyuncs.com/public/pkg/run/attach/1.16.9-aliyun.1/attach_node.sh | bash -s -- --node-name-mode nodeip --cms-enabled --cms-version 1.3.7 --openapi-token **** --addon-names flannel,csi-plugin,csi-provisioner,logtail-ds,ack-node-problem-detector,nginx-ingress-controller,kube-flannel-ds --cpu-policy none --node-cidr-mask 26 --node-port-range 30000-32767 --runtime docker --runtime-version 19.03.5"

If the cluster is a managed edge Kubernetes cluster, the following output is returned:
"wget http://aliacs-k8s-cn-hangzhou.oss-cn-hangzhou.aliyuncs.com/public/pkg/run/attach/1.12.6-aliyunedge.1/edgeadm -O edgeadm; chmod u+x edgeadm; ./edgeadm join --openapi-token=*** --node-spec="{\"flannelIface\":\"eth0\",\"enableIptables\":true,\"assumeYes\":true,\"manageRuntime\":true,\"nodeNameStrategy\":\"hostname\",\"enabledAddons\":[\"kube-proxy\",\"flannel\",\"coredns\"]}""

```

## Error codes

For a list of error codes, visit the [API Error Center](https://error-center.alibabacloud.com/status/product/CS).

