# DescribeClusterAttachScripts

Manually adds an existing instance to a specified Container Service for Kubernetes \(ACK\) cluster. This operation returns a unique executable script. You can run this script on an existing node to add the node to the specified ACK cluster.

## Debugging

[OpenAPI Explorer automatically calculates the signature value. For your convenience, we recommend that you call this operation in OpenAPI Explorer. OpenAPI Explorer dynamically generates the sample code of the operation for different SDKs.](https://api.aliyun.com/#product=CS&api=DescribeClusterAttachScripts&type=ROA&version=2015-12-15)

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
|nodepool\_id|String|No|np1c9229d9be2d432c93f77a88fca0\*\*\*\*|The ID of the node pool to which the existing node is added. You can specify this parameter to add the existing node to a specific node pool. |
|format\_disk|Boolean|No|false|Specifies whether to mount data disks to the existing instance when the instance is added to the cluster. You can add data disks to store the container data and images. Valid values:

 -   `true`: mounts data disks to the existing instance. After a data disk is mounted, the original data that is stored on the disk is cleared. Before you mount a data disk to the nodes, we recommend that you back up the disk data.
-   `false`: does not mount data disks to the existing instance.

 Default value: `false`.

 A data disk is mounted based on the following rules:

 -   If data disks have been mounted to the ECS instance and the file system of the last data disk is not initialized, the system automatically formats the last data disk to ext4 and mounts the data disk to /var/lib/docker and /var/lib/kubelet.
-   If no data disk is mounted to the ECS instance, the system does not purchase a new data disk. |
|keep\_instance\_name|Boolean|No|true|Specifies whether to keep the instance name after it is added to the cluster. If you set this parameter to false, the instance is named in the `worker-k8s-for-cs-<clusterid>` format. Valid values:

 -   `true`: keeps the instance name.
-   `false`: does not keep the instance name.

 Default value: `true`. |
|rds\_instances|Array of String|No|rm-xxx|The names of the Relational Database Service \(RDS\) instances. |
|arch|String|No|amd64|The CPU architecture of the node. Valid values: `amd64`, `arm`, and `arm64`.

 Default value: `amd64`.

 **Note:** This parameter is required if the existing node is added to a managed edge Kubernetes cluster. |
|options|String|No|\{\\"enableIptables\\": true,\\"manageRuntime\\": true,\\"quiet\\": true,\\"allowedClusterAddons\\": \[\\"kube-proxy\\",\\"flannel\\",\\"coredns\\"\]\}|The connection configurations of the existing node that you want to add.

 **Note:** This parameter is required if the existing node is added to a managed edge Kubernetes cluster. |

## Response syntax

```
HTTP/1.1 200
Content-Type:application/json
{
  "result" : "String"
}
```

## Response parameters

|Parameter|Type|Example|Description|
|---------|----|-------|-----------|
| |String|"wget http://aliacs-k8s-cn-hangzhou.oss-cn-hangzhou.aliyuncs.com/public/pkg/run/attach/1.12.6-aliyunedge.1/edgeadm -O edgeadm; chmod u+x edgeadm; ./edgeadm join --openapi-token=XXX --node-spec="\{\\"flannelIface\\":\\"eth0\\",\\"enableIptables\\":true,\\"assumeYes\\":true,\\"manageRuntime\\":true,\\"nodeNameStrategy\\":\\"hostname\\",\\"enabledAddons\\":\[\\"kube-proxy\\",\\"flannel\\",\\"coredns\\"\]\}""|The returned script that is used to add an existing node to the ACK cluster. |

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

Sample success responses

`XML` format

```
HTTP/1.1 200 OK
Content-Type:application/xml

<result>"wget http://aliacs-k8s-cn-hangzhou.oss-cn-hangzhou.aliyuncs.com/public/pkg/run/attach/1.12.6-aliyunedge.1/edgeadm -O edgeadm; chmod u+x edgeadm; ./edgeadm join --openapi-token=XXX --node-spec="{\"flannelIface\":\"eth0\",\"enableIptables\":true,\"assumeYes\":true,\"manageRuntime\":true,\"nodeNameStrategy\":\"hostname\",\"enabledAddons\":[\"kube-proxy\",\"flannel\",\"coredns\"]}""</result>
```

`JSON` format

```
HTTP/1.1 200 OK
Content-Type:application/json

{
  "result" : "\"wget http://aliacs-k8s-cn-hangzhou.oss-cn-hangzhou.aliyuncs.com/public/pkg/run/attach/1.12.6-aliyunedge.1/edgeadm -O edgeadm; chmod u+x edgeadm; ./edgeadm join --openapi-token=XXX --node-spec=\"{\\\"flannelIface\\\":\\\"eth0\\\",\\\"enableIptables\\\":true,\\\"assumeYes\\\":true,\\\"manageRuntime\\\":true,\\\"nodeNameStrategy\\\":\\\"hostname\\\",\\\"enabledAddons\\\":[\\\"kube-proxy\\\",\\\"flannel\\\",\\\"coredns\\\"]}\"\""
}
```

## Error codes

For a list of error codes, visit the [API Error Center](https://error-center.alibabacloud.com/status/product/CS).

