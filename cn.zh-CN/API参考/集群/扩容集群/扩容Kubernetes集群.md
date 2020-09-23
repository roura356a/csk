# 扩容Kubernetes集群

调用ScaleOutCluster增加集群中Worker节点的数量（支持多可用区实例）。

## 调试

[您可以在OpenAPI Explorer中直接运行该接口，免去您计算签名的困扰。运行成功后，OpenAPI Explorer可以自动生成SDK代码示例。](https://api.aliyun.com/#product=CS&api=ScaleOutCluster&type=ROA&version=2015-12-15)

## 请求头

该接口使用公共请求头，无特殊请求头。请参见公共请求参数文档。

## 请求语法

```
post /api/v2/clusters/{ClusterId} http|https
```

## 请求参数

|名称|类型|是否必选|示例值|描述|
|--|--|----|---|--|
|ClusterId|String|是|c82e6987e2961451182edacd74faf\*\*\*\*|集群ID。 |
|count|Integer|否|3|扩容节点数量。 |
|worker\_instance\_charge\_type|String|否|PrePaid|节点付费类型，可选值为：

 -   PrePaid：预付费。
-   PostPaid：按量付费。

 默认为按量付费。 |
|worker\_period|Integer|否|1|节点包年包月时长，当worker\_instance\_charge\_type取值为PrePaid时才生效且为必选值，取值范围：`PeriodUnit=Month`时，Period取值：\{ “1”， “2”， “3”， “6”， “12”\}。 |
|worker\_period\_unit|String|否|Month|节点包年包月周期，当指定为PrePaid的时候需要指定周期。Month：以月为计时单位。 |
|worker\_auto\_renew|Boolean|否|true|节点是否开启自动续费，可选值为：

 -   true：自动续费。
-   false：不自动续费。 |
|worker\_auto\_renew\_period|Integer|否|6|节点自动续费周期，当选择预付费和自动续费时才生效，且为必选值： `PeriodUnit=Month`时，取值\{“1”， “2”， “3”， “6”， “12”\}。 |
|worker\_system\_disk\_category|String|否|cloud\_efficiency|Worker节点系统盘类型。 |
|worker\_system\_disk\_size|Integer|否|120|节点系统盘大小，单位GB。 |
|worker\_data\_disk|Boolean|否|true|节点是否挂载数据盘，可选择为：

 -   true：表示worker节点挂载数据盘。
-   false：表示worker节点不挂载数据盘。 |
|key\_pair|String|否|secrity-key|密钥登录时的密钥对对名称，和`login_password`二选一。 |
|login\_password|String|否|Hello@1234|SSH登录密码。密码规则为8~30 个字符，且至少同时包含三项（大小写字母、数字和特殊符号），和`key_pair`二选一。 |
|cloud\_monitor\_flags|Boolean|否|true|节点是否安装云监控。 |
|cpu\_policy|String|否|none|CPU管理策略，集群版本为1.12.6及以上版本支持static和none两种策略。默认为none。 |
|disable\_rollback|Boolean|否|true|节点添加失败是否回滚。 |
|image\_id|String|否|""|节点自定义镜像，默认使用系统镜像，当选择自定义镜像时，将取代默认系统镜像。请参考 [自定义镜像](https://help.aliyun.com/document_detail/146647.html?spm=5176.2020520152.0.0.546b16ddPelyaG)。 |
|user\_data|String|否|""|节点自定义数据，Windows 支持 bat 和 powershell 两种格式，在 Base64 编码前，第一行为\[bat\] 或者 \[powershell\]。Linux 支持 shell 脚本，更多的格式请参见 [cloud-init](https://cloudinit.readthedocs.io/en/latest/topics/format.html) \| [查看详情](https://help.aliyun.com/knowledge_detail/49121.html)。

 如果您使用的自定义脚本大小大于 1 KB，建议您将脚本上传到 OSS，通过 OSS 内网端点拉取脚本执行。

 创建集群或添加节点提交成功不代表实例自定义脚本执行成功，请自行确定脚本执行情况。 |
|runtime|Object|否| |节点容器运行时。 |
|name|String|否|docker|容器运行时名称。 |
|version|String|否|19.03.5|容器运行时版本。 |
|vswitch\_ids|Array of String|否|vsw-2ze4jvvvade1yk899\*\*\*\*|虚拟交换机ID。 |
|worker\_instance\_types|Array of String|否|ecs.c5.xlarge|节点类型。 |
|rds\_instances|Array of String|否|rm-2zev748xi27xc\*\*\*\*|RDS实例。 |
|worker\_data\_disks|Array|否| |节点数据盘配置。 |
|category|String|否|cloud\_ssd|数据盘类型。 |
|encrypted|String|否|false|数据盘是否加密。 |
|size|String|否|120|数据盘大小。 |
|tags|Array|否| |节点标签信息。 |
|key|String|否|tier|标签key。 |
|value|String|否|frontend|标签值。 |
|taints|Array|否| |节点污点信息。 |
|effect|String|否|NoSchedule|调度策略。 |
|key|String|否|node\_type|污点key。 |
|value|String|否|prod|污点值。 |

## 返回数据

|名称|类型|示例值|描述|
|--|--|---|--|
|cluster\_id|String|"c82e6987e2961451182edacd74faf\*\*\*\*"|集群ID。 |
|request\_id|String|"687C5BAA-D103-4993-884B-C35E4314A1E1"|请求ID。 |
|task\_id|String|"T-5a54309c80282e39ea00002f"|任务ID。 |

## 示例

请求示例

```
POST /api/v2/clusters/[ClusterId] HTTP/1.1
公共请求头
{
    "ClusterId":"c82e6987e2961451182edacd7****",
    "count":1,
    "key_pair":"common",
    "vswitch_ids":[
        "vsw-uf684tfrpwup8gcsw****"
    ],
    "worker_instance_types":[
        "ecs.c5.xlarge"
    ],
    "worker_system_disk_category":"cloud_efficiency",
    "worker_system_disk_size":120,
    "worker_data_disk":false,
    "worker_data_disks":[
        {
            "category":"cloud_ssd",
            "size":500
        }
    ],
    "tags":[
        {
            "key":"tier",
            "value":"frontend"
        }
    ]
}
```

正常返回示例

`XML` 格式

```
<cluster_id>c82e6987e2961451182edacd74faf****</cluster_id>
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

访问[错误中心](https://error-center.aliyun.com/status/product/CS)查看更多错误码。

