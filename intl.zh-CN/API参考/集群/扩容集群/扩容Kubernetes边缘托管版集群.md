# 扩容Kubernetes边缘托管版集群

调用ScaleOutCluster增加集群中Worker节点数量。目前只能扩容边缘节点服务（Edge Node Service, ENS）实例。

## 调试

[您可以在OpenAPI Explorer中直接运行该接口，免去您计算签名的困扰。运行成功后，OpenAPI Explorer可以自动生成SDK代码示例。](https://api.aliyun.com/#product=CS&api=CreateCluster&type=ROA&version=2015-12-15)

## 请求语法

```
POST /api/v2/clusters/ClusterId HTTP/1.1
Content-Type:application/json
{
  "count" : Long,
  "worker_instance_type" : "String",
  "ens_region_id" : "String",
  "worker_image_id" : "String",
  "ens_internet_charge_type": "String",
  "worker_period": Long,
  "login_password": "String",
  "worker_system_disk_size": Long,
  "is_edge_worker": Boolean,
  "worker_data_disk_size": Long,
  "worker_auto_renew": Boolean,
  "worker_auto_renew_period": Long,
  "timeout_mins": Long
}
```

## 请求参数

|参数名称|类型|是否必选|示例|说明|
|----|--|----|--|--|
|ClusterId|String|是|Cccfd68c474454665ace07efce924\*\*\*\*|集群ID。 |

|名称|类型|必须|示例值|描述|
|--|--|--|---|--|
|count|Long|是|1|扩容ENS实例数量。|
|worker\_instance\_type|String|是|ens.sn1.tiny|ENS实例规格。|
|ens\_region\_id|String|是|cn-beijing-cmcc-2|ENS地域ID。|
|worker\_image\_id|String|是|centos\_7\_06\_64\_20G\_alibase\_2019\*\*\*\*|节点自定义镜像，默认使用系统镜像。当选择自定义镜像时，将取代默认系统镜像。请参见[自定义镜像](~~146647~~)。 |
|ens\_Internet\_charge\_type|String|是|BandwidthByDay|计费方式,，取值：-   `BandwidthByDay`：日峰值带宽。
-   `95BandwidthByMonth`：月95峰值带宽。 |
|worker\_period|Long|是|1|Worker节点包年包月时长，当`worker_instance_charge_type`取值为`PrePaid`时才生效且为必选值。

取值范围：\{1, 2, 3, 6, 12, 24, 36, 48, 60\}。

默认值：1。 |
|login\_password|String|是|HelloWorl\*\*\*\*|SSH登录密码，和`key_pair`二选一。密码规则为8~30个字符，且至少同时包含三项（大小写字母、数字和特殊符号）。 |
|worker\_system\_disk\_size|Long|是|40|系统盘大小，单位为GiB。取值：20~100之间10的倍数，并且要大于OS镜像大小。|
|is\_edge\_worker|Boolean|是|true|扩容的ENS节点是否作为边缘worker节点。取值：-   `true`：作为边缘Worker节点。
-   `false`：不作为边缘Worker。

该场景下，只允许为`true`。|
|worker\_data\_disk\_size|Long|否|40|数据盘大小。0表示不需要数据盘，20~200之间的10的倍数。|
|worker\_auto\_renew|Boolean|否|true|是否要自动续费。取值-   `true`：自动续费。
-   `false`：不自动续费。

默认值：`false`。|
|worker\_auto\_renew\_period|Long|否|1|每次自动续费的时长，当参数`worker_auto_renew`取值`true`时为必填。取值范围：\[1,12\]。|
|timeout\_mins|Long|否|60|扩容超时时间，单位分钟。|

## 响应体语法

```
HTTP/1.1 200
Content-Type:application/json
{
  "cluster_id" : "String",
  "request_id" : "String",
  "task_id" : "String"
}
```

## 响应参数

|名称|类型|示例值|描述|
|--|--|---|--|
|cluster\_id|String|Cccfd68c474454665ace07efce924\*\*\*\*|集群ID。 |
|request\_id|String|687C5BAA-D103-4993-884B-C35E4314A1E1|请求ID。 |
|task\_id|String|T-5a54309c80282e39ea00002f|任务ID。 |

## 扩容ACK边缘托管版集群示例

请求示例

```
POST /api/v2/clusters/Cccfd68c474454665ace07efce924**** 
<公共请求头>
{
  "timeout_mins": 60,
  "worker_instance_type": "ens.sn1.tiny",
  "ens_region_id": "cn-beijing-telecom",
  "worker_image_id": "m-2QVLO2T8NYgm8CNQVg****",  
  "ens_internet_charge_type": "BandwidthByDay",
  "worker_period": 1,
  "worker_auto_renew": true,
  "worker_auto_renew_period": 1,
  "login_password": "Hello1234!",
  "count": 1,
  "worker_system_disk_size": 20,
  "worker_data_disk_size": 0,
  "is_edge_worker": true
}
```

正常返回示例

`XML` 格式

```
<cluster_id>Cccfd68c474454665ace07efce924****</cluster_id>
<task_id>T-5a54309c80282e39ea00002f</task_id>
<request_id>687C5BAA-D103-4993-884B-C35E4314A1E1</request_id>
```

`JSON` 格式

```
{
    "cluster_id": "c82e6987e2961451182edacd74faf****",
    "task_id": "T-5a54309c80282e39ea00002f",
    "request_id": "687C5BAA-D103-4993-884B-C35E4314A1E1"
}
```

## 错误码

访问[错误中心](https://error-center.alibabacloud.com/status/product/CS)查看更多错误码。

