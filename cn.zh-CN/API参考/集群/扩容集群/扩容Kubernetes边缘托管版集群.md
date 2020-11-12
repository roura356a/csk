# 扩容Kubernetes边缘托管版集群

调用ScaleOutCluster增加集群中Worker节点数量。目前只能扩容边缘节点服务\(Edge Node Service, ENS\)实例。

## 请求信息

请求行RequestLine

```
POST /api/v2/clusters/{cluster_id} HTTP/1.1
```

|名称|类型|是否必须|描述|
|--|--|----|--|
|cluster\_id|string|是|集群ID。|

特有请求头RequestHead

无，请参见[公共请求和返回结果](/cn.zh-CN/API参考/公共请求和返回结果.md)。

请求体RequestBody

```
{
  "timeout_mins": 超时时间,
  "worker_instance_type": ens型号,
  "ens_region_id": ens region id,
  "worker_image_id": 实例使用的操作系统镜像,  
  "ens_internet_charge_type": 计费类型,
  "worker_period": 购买时长,
  "worker_auto_renew": 是否自动续费,
  "worker_auto_renew_period": 续费时长,
  "login_password": 扩容实例密码,
  "count": 扩容数量,
  "worker_system_disk_size": 系统盘大小,
  "worker_data_disk_size": 数据盘大小,
  "is_edge_worker": 是否为边缘节点
}
```

|名称|类型|必须|描述|
|--|--|--|--|
|count|int|是|扩容数量。|
|worker\_instance\_type|string|是|ENS型号。|
|ens\_region\_id|string|是|ENS的区域ID。|
|worker\_image\_id|string|是|实例使用的OS镜像。|
|ens\_internet\_charge\_type|string|是|第一次新购时必须填写。如果用户已有资源，则按已存在有效的计费方式。 -   BandwidthByDay：日峰值带宽。
-   95BandwidthByMonth：月95峰值带宽。 |
|worker\_period|int|是|购买资源的时长，单位为：月。取值范围：1~9，12。|
|login\_password|string|是|密码。|
|worker\_system\_disk\_size|int|是|系统盘大小，单位为GiB。取值：20~100之间10的倍数，并且要大于镜像大小。|
|is\_edge\_worker|bool|是|取值必须为true。|
|worker\_data\_disk\_size|int|否|数据盘大小。0表示不需要数据盘，20~200之间的10的倍数。|
|worker\_auto\_renew|bool|否|是否要自动续费。取值范围： -   true：自动续费。
-   false：不自动续费。

默认值：false。 |
|worker\_auto\_renew\_period|int|否|每次自动续费的时长，当参数AutoRenew取值true时为必填。取值范围：1~12。|
|timeout\_mins|int|否|超时时间。|

## 返回信息

返回行ResponseLine

```
HTTP/1.1 202 Accepted
```

特有返回头ResponseHead

无，请参见[公共请求和返回结果](/cn.zh-CN/API参考/公共请求和返回结果.md)。

返回体

```
{
    "cluster_id":"string",
    "request_id":"string",
    "task_id":"string"
}
```

## 示例

请求示例

```
POST /api/v2/clusters/Cccfd68c474454665ace07efce924**** HTTP/1.1
<公共请求头>
{
  "timeout_mins": 60,
  "worker_instance_type": "ens.sn1.tiny",
  "ens_region_id": "cn-beijing-telecom",
  "worker_image_id": "m-2QVLO2T8NYgm8CNQVg15gF",  
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

返回示例

```
HTTP/1.1 202 Accepted
<公共响应头>
{
    "cluster_id": "Cccfd68c474454665ace07efce924****",
    "request_id": "687C5BAA-D103-4993-884B-C35E4314A1E1",
    "task_id": "T-5a54309c80282e39ea00002f"
}
```

