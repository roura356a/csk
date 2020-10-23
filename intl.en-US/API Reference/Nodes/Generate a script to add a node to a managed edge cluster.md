# Generate a script to add a node to a managed edge cluster

You can call DescribeClusterAttachScripts to add a node to a managed edge cluster. This operation returns a unique executable script. You can run this script on an existing node to add the node to a managed edge cluster.

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
|options|Json|Yes|\{\}|The node configurations. |
|flannelIface|String|No|eth0|The name of the network interface card \(NIC\) that is used by the Flannel plug-in. Default value: eth0. |
|enableIptables|Boolean|No|true|Specifies whether to enable iptables. Default value: true. |
|manageRuntime|Boolean|No|true|Specifies whether to use edgeadm to install and check the runtime. |
|gpuVersion|String|No|Nvidia\_Tesla\_T4|Specifies whether the node is a GPU node. By default, this parameter is not specified. Currently, Nvidia\_Tesla\_T4 is supported. |
|nodeNamePrefix|String|No|prefix|The prefix of the node name.

 -   By default, the host name is used as the prefix.
-   Otherwise, the parameter is set to a prefixed UUID. |
|nodeNameStrategy|String|No|hostname|The node naming strategy. Valid values:

 -   hostname: The host name of the node.
-   random: A random string.
-   randomWithPrefix: A random string with a specified prefix.
-   customized: A custom name.

 Default value: hostname. |
|enabledAddons|List<String\>|No|\["flannel","kube-proxy"\]|The add-ons to be installed for the node. By default, this parameter is not specified, which indicates that no add-ons are installed. We recommend that you set this parameter to `["kube-proxy","flannel","coredns"]`. |
|nodeName|String|No|""|The name of the node.

 This parameter takes effect only if`nodeNameStrategy is set to customized.` |

\{

"options": "\{

\\"flannelIface\\": "The NIC used by the Flannel plug-in.",

\\"enableIptables\\": "Specifies whether to enable iptables.",

\\"manageRuntime\\": "Specifies whether to use edgeadm to install and check the runtime.",

\\"nodeNameStrategy\\": "The node naming strategy.",

\\"nodeName\\": "The node name.",

\\"nodeNamePrefix\\": "The prefix of the node name.",

\\"enabledAddons\\": \[

\\"kube-proxy\\",

\\"flannel\\",

\\"coredns\\"

\]

\}"

\}

## Examples

Sample requests

```
POST /clusters/c106f377e16f34eb1808d6b9362c9****/attachscript HTTP/1.1

Common request header
{
     "options": "{
               \"flannelIface\": \"eth0\",
               \"enableIptables\": true,
               \"manageRuntime\": true,
               \"nodeNameStrategy\": \"hostname\",
               \"enabledAddons\": [
                      \"kube-proxy\",
                      \"flannel\",
                     \"coredns\",
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

