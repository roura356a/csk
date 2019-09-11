# 添加已有节点到 Kubernetes 边缘托管版集群 {#reference_hrl_33b_wdb .reference}

添加已有节点到边缘Kubernetes托管集群，该API返回唯一的可执行脚本，用户获取脚本后，在已有节点上执行即可完成该节点的接入。

## 请求信息 {#section_e2b_mjb_wdb .section}

请求行 RequestLine

``` {#codeblock_877_0cf_8g6}
POST /clusters/{cluster_id}/attachscript HTTP/1.1
```

|名称|类型|是否必须|描述|
|:-|:-|:---|:-|
|cluster\_id|string|是|集群 ID|

特有请求头 RequestHead

无，请参考[公共请求头部](cn.zh-CN/开发指南/集群 API 调用方式/公共参数.md#section_mr5_lf1_wdb)。

请求体 RequestBody

``` {#codeblock_v4n_uez_lv2}
{
  "options": "{
    \"flannelIface\": "flannel绑定网卡",
    \"enableIptables\": "是否启用iptables",
    \"manageRuntime\": "是否使用接入工具管理runtime",
    \"nodeNameStrategy\": "节点的命名机制",
    \"nodeName\": "节点名称"，
    \"nodeNamePrefix\": "节点名称前缀",
    \"enabledAddons\": [
            \"kube-proxy\",
            \"flannel\",
            \"coredns\"
    ]
     }"
}
```

|名称|类型|是否必须|描述|
|:-|:-|:---|:-|
|options|json|是|节点的接入配置。|
|enabledAddons|array|否|需要安装的组件列表；默认为空，不安装；普通节点需要配置为`["kube-proxy","flannel", "coredns"]`。|
|enableIptables|bool|否|是否开启iptables，默认值true。|
|flannelIface|String|否|flannel使用的网卡名；默认值是eth0。|
|manageRuntime|bool|否|是否由edgeadm安装并检测Runtime。|
|nodeName|string|否|设置节点名。 只有在`--nodename-strategy=customized`时生效。

 |
|nodeNamePrefix|string|否|节点名称前缀。 -   如果前缀为空，则默认使用hostname
-   如果不为空，则取值为prefix+UUID

 |
|nodeNameStrategy|bool|否| 节点生成策略。目前支持四种策略：

-   hostname：主机名
-   random：随机字符串
-   randomWithPrefix：指定前缀+随机字符串
-   customized：自定义

默认值为hostname。

 |

## 返回信息 {#section_awy_llb_wdb .section}

返回行 ResponseLine

``` {#codeblock_r66_o68_1ak}
HTTP/1.1 202 OK
```

特有返回头 ResponseHead

无，请参考[公共返回头部](cn.zh-CN/开发指南/集群 API 调用方式/公共参数.md#section_zr5_lf1_wdb)。

返回体 ResponseB

``` {#codeblock_xkw_0fo_bsp}
 "wget http://aliacs-k8s-cn-hangzhou.oss-cn-hangzhou.aliyuncs.com/public/pkg/run/attach/1.12.6-aliyunedge.1/edgeadm -O edgeadm; chmod u+x edgeadm; ./edgeadm join --openapi-token=XXX --node-spec="{\"flannelIface\":\"eth0\",\"enableIptables\":true,\"assumeYes\":true,\"manageRuntime\":true,\"nodeNameStrategy\":\"hostname\",\"enabledAddons\":[\"kube-proxy\",\"flannel\",\"coredns\"]}""
```

## 示例 {#section_t2q_rlb_wdb .section}

请求示例

``` {#codeblock_g2h_xt5_5k0}
POST /clusters/Cccfd68c47445466XXXX/attachscript HTTP/1.1
<公共请求头>
{
     "options": "{
      \"flannelIface\": \"eth0\",
      \"enableIptables\": true,
      \"manageRuntime\": true,
      \"nodeNameStrategy\": \"hostname\",
      \"enabledAddons\": [
            \"kube-proxy\",
            \"flannel\",
            \"coredns\"
      ]
     }"
}
```

返回示例

``` {#codeblock_o0c_rcz_gr0}
HTTP/1.1 202 Accepted
<公共响应头>
 "wget http://aliacs-k8s-cn-hangzhou.oss-cn-hangzhou.aliyuncs.com/public/pkg/run/attach/1.12.6-aliyunedge.1/edgeadm -O edgeadm; chmod u+x edgeadm; ./edgeadm join --openapi-token=XXX --node-spec="{\"flannelIface\":\"eth0\",\"enableIptables\":true,\"assumeYes\":true,\"manageRuntime\":true,\"nodeNameStrategy\":\"hostname\",\"enabledAddons\":[\"kube-proxy\",\"flannel\",\"coredns\"]}""
```

