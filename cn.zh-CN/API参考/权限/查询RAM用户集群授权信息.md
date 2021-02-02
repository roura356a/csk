# 查询RAM用户集群授权信息

调用DescribeUserPermission查询指定RAM用户的集群授权信息。

## 调试

[您可以在OpenAPI Explorer中直接运行该接口，免去您计算签名的困扰。运行成功后，OpenAPI Explorer可以自动生成SDK代码示例。](https://api.aliyun.com/#product=CS&api=DescribeUserPermission&type=ROA&version=2015-12-15)

## 请求语法

```
GET /permissions/users/uid HTTP/1.1
Content-Type:application/json
```

## 请求参数

|参数名称|类型|是否必选|示例|说明|
|----|--|----|--|--|
|uid|String|是|21175\*\*\*\*|指定RAM用户的ID。

 **说明：** 当为RAM角色授权时，使用RAM角色的ID。 |

## 响应体语法

```
HTTP/1.1 200 OK
Content-Type:application/json

[ {
  "resource_id" : "String",
  "resource_type" : "String",
  "role_name" : "String",
  "role_type" : "String",
  "is_owner" : Long,
  "is_ram_role" : Long
} ]
```

## 响应参数

|参数名称|类型|示例|说明|
|----|--|--|--|
| |Array| |返回体参数。 |
|resource\_id|String|c1b542\*\*\*\*|集群访问配置，格式为：

 -   当是集群维度授权时，格式为：`{cluster_id}`。
-   当是命名空间维度授权时，格式为：`{cluster_id}/{namespace}`。
-   当是所有集群授权时，值固定为：`all-clusters`。 |
|resource\_type|String|cluster|授权类型，取值：

 -   `cluster`：集群维度。
-   `namespace`：命名空间维度。
-   `console`：前端展示 。 |
|role\_name|String|view|自定义角色名称，当授权自定义角色时，该字段为指定的自定义集群管理角色名称。 |
|role\_type|String|admin|预置的角色类型，取值：

 -   `admin`：管理员。
-   `ops`：运维人员。
-   `dev`：开发人员。
-   `restricted`：受限用户。
-   `custom`：使用自定义的集群管理角色。 |
|is\_owner|Long|1|是否为集群创建者的授权，取值：

 -   `0`：代表不是集群创建者的授权记录。
-   `1`：代表该授权记录为集群创建者的管理员权限。 |
|is\_ram\_role|Long|1|是否为RAM角色授权，取值：

 -   `0`：代表当前记录不是RAM角色授权。
-   `1`：代表当前记录是RAM角色授权。 |

## 查询RAM用户集群授权信息示例

```
GET /permissions/users/21175**** HTTP/1.1 
Host:cs.aliyuncs.com 
Content-Type:application/json
```

正常返回示例

`XML`格式

```
HTTP/1.1 200 OK
Content-Type:application/xml

<0>
    <resource_id>c1b542**</resource_id>
    <resource_type>cluster</resource_type>
    <role_name>view</role_name>
    <role_type>admin</role_type>
    <is_owner>1</is_owner>
    <is_ram_role>1</is_ram_role>
</0>
```

`JSON`格式

```
HTTP/1.1 200 OK
Content-Type:application/json

[ {
  "resource_id" : "c1b542**",
  "resource_type" : "cluster",
  "role_name" : "view",
  "role_type" : "admin",
  "is_owner" : 1,
  "is_ram_role" : 1
} ]
```

## 错误码

访问[错误中心](https://error-center.aliyun.com/status/product/CS)查看更多错误码。

## 开发者资源

-   [SDK](https://next.api.aliyun.com/api-tools/sdk/CS?version=2015-12-15&)

    阿里云为您提供多种语言的SDK，帮助您快速通过API集成阿里云的产品和服务，推荐您使用SDK调用API，已免除您手动签名验证，详情请参见SDK参考文档链接。

-   [OpenAPI Explorer](https://next.api.aliyun.com/api/CS/2015-12-15/DescribeUserPermission)

    快速检索，可视化调试API，在线命令行工具，同步动态生成可执行的SDK代码示例。

-   [阿里云CLI](https://github.com/aliyun/aliyun-cli)

    阿里云资产管理和配置工具，可通过命令方式同时管理多个阿里云产品和服务，简单快捷，是您上云好帮手。


