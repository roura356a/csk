# 生成Kubernetes集群的节点接入脚本

调用DescribeClusterAttachScripts添加已有节点到边缘Kubernetes托管集群。该API返回唯一的可执行脚本。您获取脚本后，在已有节点上执行该脚本即可完成节点的接入。

## 调试

[您可以在OpenAPI Explorer中直接运行该接口，免去您计算签名的困扰。运行成功后，OpenAPI Explorer可以自动生成SDK代码示例。](https://api.aliyun.com/#product=CS&api=DescribeClusterAttachScripts&type=ROA&version=2015-12-15)

## 请求头

该接口使用公共请求头，无特殊请求头。请参见公共请求参数文档。

## 请求语法

```
POST /clusters/{ClusterId}/attachscript HTTP|HTTPS
```

## 请求参数

|名称|类型|位置|是否必选|示例值|描述|
|--|--|--|----|---|--|
|ClusterId|String|Path|是|ca375a93a30474552ad2a0ebe183e\*\*\*\*|集群ID。 |
| |Object|Body|否| |1 |
|arch|String|Body|否|amd64|节点CPU架构。支持的CPU架构包括:amd64、arm、arm64。默认amd64。当集群类型为边缘托管版时必填。 |
|options|Object|Body|否| |节点的接入配置参数，当集群类型为边缘托管版时必填。 |
|allowedClusterAddons|Array of String|Body|否|kube-proxy|组件名称。 |
|enableIptables|Boolean|Body|否|true|是否开启iptables，默认值true。 |
|flannelIface|String|Body|否|eth0|flannel使用的网卡名。默认使用节点默认路由的网卡名。 |
|gpuVersion|String|Body|否|false|表示要接入的节点是否为GPU节点，默认为空。当前支持的GPU版本是Nvidia\_Tesla\_T4、Nvidia\_Tesla\_P4、Nvidia\_Tesla\_P100。 |
|manageRuntime|Boolean|Body|否|false|是否由edgeadm安装并检测Runtime，默认false。 |
|nodeNameOverride|String|Body|否|""|设置节点名。

 -   ""（默认值，表示使用主机名。）
-   "\*"（表示随机生成6位的字符串。）
-   "\*.XXX"（表示随机生成6位字符串+XXX后缀。） |
|quiet|String|Body|否|false|是否使用静默模式安装。 |

## 返回数据

|名称|类型|示例值|描述|
|--|--|---|--|
| |String|"wget http://aliacs-k8s-cn-hangzhou.oss-cn-hangzhou.aliyuncs.com/public/pkg/run/attach/1.12.6-aliyunedge.1/edgeadm -O edgeadm; chmod u+x edgeadm; ./edgeadm join --openapi-token=XXX --node-spec="\{\\"flannelIface\\":\\"eth0\\",\\"enableIptables\\":true,\\"assumeYes\\":true,\\"manageRuntime\\":true,\\"nodeNameStrategy\\":\\"hostname\\",\\"enabledAddons\\":\[\\"kube-proxy\\",\\"flannel\\",\\"coredns\\"\]\}""|节点接入脚本。 |

## 示例

请求示例

```
POST /clusters/[c106f377e16f34eb1808d6b9362c9****]/attachscript HTTP/1.1
公共请求头
```

请求示例补充说明

```
边缘托管版生成节点接入脚本示例，options和arch参数必填：

POST /clusters/c106f377e16f34eb1808d6b9362c9****/attachscript HTTP/1.1
公共请求头
{
    "arch":"amd64",
    "options":"{\"enableIptables\": true,\"manageRuntime\": true,\"quiet\": true,\"allowedClusterAddons\": [\"kube-proxy\",\"flannel\",\"coredns\"]}"
}
```

返回示例补充说明

```
非边缘托管版：
"curl http://aliacs-k8s-cn-chengdu.oss-cn-chengdu-internal.aliyuncs.com/public/pkg/run/attach/1.16.9-aliyun.1/attach_node.sh | bash -s -- --node-name-mode nodeip --cms-enabled --cms-version 1.3.7 --openapi-token **** --addon-names flannel,csi-plugin,csi-provisioner,logtail-ds,ack-node-problem-detector,nginx-ingress-controller,kube-flannel-ds --cpu-policy none --node-cidr-mask 26 --node-port-range 30000-32767 --runtime docker --runtime-version 19.03.5"

边缘托管版：
"wget http://aliacs-k8s-cn-hangzhou.oss-cn-hangzhou.aliyuncs.com/public/pkg/run/attach/1.12.6-aliyunedge.1/edgeadm -O edgeadm; chmod u+x edgeadm; ./edgeadm join --openapi-token=*** --node-spec="{\"flannelIface\":\"eth0\",\"enableIptables\":true,\"assumeYes\":true,\"manageRuntime\":true,\"nodeNameStrategy\":\"hostname\",\"enabledAddons\":[\"kube-proxy\",\"flannel\",\"coredns\"]}""

```

## 错误码

访问[错误中心](https://error-center.aliyun.com/status/product/CS)查看更多错误码。

