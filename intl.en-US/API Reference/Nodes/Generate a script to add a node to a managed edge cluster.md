# Generate a script to add a node to a managed edge cluster

You can call DescribeClusterAttachScripts to generate a script to add a node to a managed edge cluster. This operation returns a unique executable script. You can run this script on an existing node to add the node to a managed edge cluster.

## Debugging

[OpenAPI Explorer automatically calculates the signature value. For your convenience, we recommend that you call this operation in OpenAPI Explorer. OpenAPI Explorer dynamically generates the sample code of the operation for different SDKs.](https://api.aliyun.com/#product=CS&api=DescribeClusterAttachScripts&type=ROA&version=2015-12-15)

## Request parameters

This operation uses common request parameters only. For more information, see Common parameters.

## Request syntax

```
POST /clusters/{ClusterId}/attachscript HTTP|HTTPS
```

## Request parameters

|Parameter|Type|Position|Required|Example|Description|
|---------|----|--------|--------|-------|-----------|
|ClusterId|String|Path|Yes|ca375a93a30474552ad2a0ebe183e\*\*\*\*|The ID of the cluster. |
| |Object|Body|No| |The CPU architecture of the node. Valid values: amd64, arm, and arm64. Default value: amd64. This parameter is required if the cluster is a managed edge cluster. |
|arch|String|Body|No|amd64|The CPU architecture of the node. Valid values: amd64, arm, and arm64. Default value: amd64. This parameter is required if the cluster is a managed edge cluster. |
|options|Object|Body|No| |The configurations of the node. This parameter is required if the cluster is a managed edge cluster. |
|allowedClusterAddons|Array of String|Body|No|kube-proxy|The name of the components. |
|enableIptables|Boolean|Body|No|true|Specifies whether to enable iptables. Default value: true. |
|flannelIface|String|Body|No|eth0|The name of the network interface controller \(NIC\) that is used by the Flannel plug-in. By default, this parameter is set to the name of the NIC that is used by the default ingress. |
|gpuVersion|String|Body|No|false|Specifies whether the node is a graphics processing unit \(GPU\) node. By default, this parameter is not specified. Valid values: Nvidia\_Tesla\_T4, Nvidia\_Tesla\_P4, and Nvidia\_Tesla\_P100. |
|manageRuntime|Boolean|Body|No|false|Specifies whether to use edgeadm to install and check the runtime. Default value: false. |
|nodeNameOverride|String|Body|No|""|The name of the node.

-   By default, this parameter is not specified, and the host name is used as the node name.
-   The value can be a random string that contains six characters.
-   The value can be a random string that is followed by a suffix. The random string contains six characters. |
|quiet|String|Body|No|false|Specifies whether to enable the silent installation mode. |

## Response parameters

|Parameter|Type|Example|Description|
|---------|----|-------|-----------|
| |String|"wget http://aliacs-k8s-cn-hangzhou.oss-cn-hangzhou.aliyuncs.com/public/pkg/run/attach/1.12.6-aliyunedge.1/edgeadm -O edgeadm; chmod u+x edgeadm; ./edgeadm join --openapi-token=XXX --node-spec="\{\\"flannelIface\\":\\"eth0\\",\\"enableIptables\\":true,\\"assumeYes\\":true,\\"manageRuntime\\":true,\\"nodeNameStrategy\\":\\"hostname\\",\\"enabledAddons\\":\[\\"kube-proxy\\",\\"flannel\\",\\"coredns\\"\]\}""|The returned script. |

## Examples

Sample requests

```
POST /clusters/[ClusterId]/attachscript HTTP/1.1
            
```

Sample request description

```
If the ACK cluster is a managed edge Kubernetes cluster, you must set the arch and options parameters. You can set the parameters based on the following sample request: POST /clusters/[ClusterId]/attachscript HTTP/1.1 Common request headers { "ClusterId":"c106f377e16f34eb1808d6b9362c9****", "arch":"amd64", "options":"{\"enableIptables\": true,\"manageRuntime\": true,\"quiet\": true,\"allowedClusterAddons\": [\"kube-proxy\",\"flannel\",\"coredns\"]}" }
```

Sample response description

```
If the ACK cluster is not a managed edge Kubernetes cluster, the following script is returned:： "curl http://aliacs-k8s-cn-chengdu.oss-cn-chengdu-internal.aliyuncs.com/public/pkg/run/attach/1.16.9-aliyun.1/attach_node.sh | bash -s -- --node-name-mode nodeip --cms-enabled --cms-version 1.3.7 --openapi-token **** --addon-names flannel,csi-plugin,csi-provisioner,logtail-ds,ack-node-problem-detector,nginx-ingress-controller,kube-flannel-ds --cpu-policy none --node-cidr-mask 26 --node-port-range 30000-32767 --runtime docker --runtime-version 19.03.5" 边缘托管版： "wget http://aliacs-k8s-cn-hangzhou.oss-cn-hangzhou.aliyuncs.com/public/pkg/run/attach/1.12.6-aliyunedge.1/edgeadm -O edgeadm; chmod u+x edgeadm; ./edgeadm join --openapi-token=*** --node-spec="{\"flannelIface\":\"eth0\",\"enableIptables\":true,\"assumeYes\":true,\"manageRuntime\":true,\"nodeNameStrategy\":\"hostname\",\"enabledAddons\":[\"kube-proxy\",\"flannel\",\"coredns\"]}""
```

## 错误码

访问[错误中心](https://error-center.alibabacloud.com/status/product/CS)查看更多错误码。

