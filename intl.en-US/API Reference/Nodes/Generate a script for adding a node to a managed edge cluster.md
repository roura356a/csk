# Generate a script for adding a node to a managed edge cluster

You can call DescribeClusterAttachScripts to generate a script for adding a node to a managed edge cluster. After you run this script on an existing node, the node is added to a managed edge cluster.

## Debugging

[OpenAPI Explorer automatically calculates the signature value. For your convenience, we recommend that you call this operation in OpenAPI Explorer. OpenAPI Explorer dynamically generates the sample code of the operation for different SDKs.](https://api.aliyun.com/#product=CS&api=DescribeClusterAttachScripts&type=ROA&version=2015-12-15)

## Request headers

This operation uses the common request header only. For more information, see Common parameters.

## Request syntax

```
POST /clusters/[ClusterId]/attachscript HTTPS|HTTP
```

## Request parameters

|Parameter|Type|Required|Example|Description|
|---------|----|--------|-------|-----------|
|ClusterId|String|Yes|c106f377e16f34eb1808d6b9362c9\*\*\*\*|The ID of the cluster. |
|arch|String|Yes|amd64|The CPU architecture of the node. Valid values: amd64, arm, and arm64. Default value: amd64. |
|options|Json|No|\{\}|The configurations of the node. |
|enableIptables|Boolean|No|true|Specifies whether to enable iptables. Default value: true. |
|flannelIface|String|No|eth0|The name of the network interface controller \(NIC\) that is used by the Flannel plug-in. By default, this parameter is set to the name of the NIC that is used by the default gateway. |
|gpuVersion|String|No|false|Specifies whether the node is a graphics processing unit \(GPU\) node. By default, this parameter is not specified. Valid values: Nvidia\_Tesla\_T4, Nvidia\_Tesla\_P4, and Nvidia\_Tesla\_P100. |
|manageRuntime|Boolean|No|false|Specifies whether to use edgeadm to install and check the runtime. Default value: false. |
|nodeNameOverride|String|No|""|The name of the node.

 -   By default, this parameter is not specified. In this case, the host name is used as the node name.
-   The value can be a random string that contains six characters.
-   The value can be a random string that is followed by a suffix. The random string contains six characters. |
|quiet|Boolean|No|false|Specifies whether to enable the silent installation mode. |
|allowedClusterAddons|List<String\>|No|\["kube-proxy","flannel", "coredns"\]|A list of the add-ons to be installed for the node. By default, this parameter is not specified. In this case, no add-ons are installed. We recommend that you set this parameter to \["kube-proxy","flannel","coredns"\]. |

## Examples

Sample requests

```
POST /clusters/c106f377e16f34eb1808d6b9362c9****/attachscript HTTP/1.1

Common request headers
{
     "options": "{
      \"enableIptables\": true,
      \"manageRuntime\": true,
      \"quiet\": true,
      \"allowedClusterAddons\": [
            \"kube-proxy\",
            \"flannel\",
            \"coredns\"
      ]
     }"
}
```

Sample success responses

`JSON` format

```
"wget http://aliacs-k8s-cn-hangzhou.oss-cn-hangzhou.aliyuncs.com/public/pkg/run/attach/1.12.6-aliyunedge.1/edgeadm -O edgeadm; chmod u+x edgeadm; ./edgeadm join --openapi-token=XXX --node-spec="{\"flannelIface\":\"eth0\",\"enableIptables\":true,\"assumeYes\":true,\"manageRuntime\":true,\"nodeNameStrategy\":\"hostname\",\"enabledAddons\":[\"kube-proxy\",\"flannel\",\"coredns\"]}""
```

## Error codes

For a list of error codes, visit the [API Error Center](https://error-center.alibabacloud.com/status/product/CS).

