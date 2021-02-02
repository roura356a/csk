# 全量更新RAM用户集群授权信息

调用GrantPermissions全量更新RAM用户集群授权信息。

**注意事项**：

-   请确保已在RAM控制台为目标RAM用户授予了至少拥有目标集群的只读权限的RAM策略，否则API将返回`ErrorRamPolicyConfig`错误码。

    关于RAM授权操作详情，请参见 [自定义RAM授权策略](~~86485~~)。

-   如果调用这个API的账号是RAM用户账号，请先确保该账号已拥有修改其他RAM用户集群授权信息的权限，否则API将返回`StatusForbidden`或`ForbiddenGrantPermissions`错误码。具体操作，请参见[子账号如何给其他子账号进行RBAC授权](~~119035~~) 。
-   全量更新RAM用户集群授权信息操作会覆盖目标RAM用户已有的集群权限，请求时需要包含想要授权给目标RAM用户的所有权限配置。

## 调试

[您可以在OpenAPI Explorer中直接运行该接口，免去您计算签名的困扰。运行成功后，OpenAPI Explorer可以自动生成SDK代码示例。](https://api.aliyun.com/#product=CS&api=GrantPermissions&type=ROA&version=2015-12-15)

## 请求语法

```
POST /permissions/users/uid HTTP/1.1
Content-Type:application/json

[ {
  "cluster" : "String",
  "is_custom" : Boolean,
  "role_name" : "String",
  "role_type" : "String",
  "namespace" : "String",
  "is_ram_role" : Boolean
} ]
```

## 请求参数

|参数名称|类型|是否必选|示例|说明|
|----|--|----|--|--|
|uid|String|是|2367\*\*\*\*|RAM用户的ID。 |

|参数名称|类型|是否必选|示例|说明|
|----|--|----|--|--|
| |Array|否| |请求体参数 |
|cluster|String|是|c796c60\*\*\*|授权目标集群ID。

 -   当`role_type`参数的值是`all-clusters`时，此参数的值传空字符串。 |
|is\_custom|Boolean|否|false|该授权是否是自定义授权（`role_name`使用自定义的集群管理角色名称）。 |
|role\_name|String|是|ops|预置的角色名称，取值：

 -   `admin`: 管理员。
-   `ops`：运维人员。
-   `dev`：开发人员。
-   `restricted`: 受限用户。
-   `custom`：自定义的集群管理角色名称。 |
|role\_type|String|是|cluster|授权类型，取值：

 -   `cluster`：集群维度。
-   `namespace`: 命名空间维度。
-   `all-clusters`: 所有集群维度，此时`cluster` 字段的值需传当前所有集群的集群ID（使用逗号分隔多个集群ID）。 |
|namespace|String|否|test|命名空间名称，集群维度授权时默认为空。 |
|is\_ram\_role|Boolean|否|false|是否是RAM角色授权。 |

## 响应体语法

```
HTTP/1.1 200 OK
```

## 响应参数

无响应参数

## 全量更新RAM用户集群授权信息示例

```
POST /permissions/users/2367**** HTTP/1.1 
Host:cs.aliyuncs.com 
Content-Type:application/json

[ {
  "cluster" : "c796c60***",
  "is_custom" : false,
  "role_name" : "ops",
  "role_type" : "cluster",
  "namespace" : "test",
  "is_ram_role" : false
} ]
```

正常返回示例

`JSON`格式

```
HTTP/1.1 200 OK
```

## 错误码

访问[错误中心](https://error-center.alibabacloud.com/status/product/CS)查看更多错误码。

