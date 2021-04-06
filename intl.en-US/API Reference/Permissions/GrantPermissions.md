# GrantPermissions

Grants a specified Resource Access Management \(RAM\) user the permissions to manage Container Service for Kubernetes \(ACK\) clusters.

**Precautions**:

-   Make sure that you have granted the specified RAM user at least read permissions on the specified cluster by attaching RAM policies. Otherwise, the `ErrorRamPolicyConfig` error will be returned.

    For more information about how to authorize a RAM user by attaching RAM policies, see [Create a custom RAM policy](~~86485~~).

-   If you call this operation as a RAM user, make sure that this RAM user has the permissions to grant other RAM users the permissions to manage ACK clusters. Otherwise, the `StatusForbidden` or `ForbiddenGrantPermissions` errors will be returned. For more information, see [Use a RAM user to grant RBAC permissions to other RAM users](~~119035~~).
-   This operation overwrites the permissions that have been granted to the specified RAM user. When you call this operation, make sure that the required permissions are included.

## Debugging

[OpenAPI Explorer automatically calculates the signature value. For your convenience, we recommend that you call this operation in OpenAPI Explorer. OpenAPI Explorer dynamically generates the sample code of the operation for different SDKs.](https://api.aliyun.com/#product=CS&api=GrantPermissions&type=ROA&version=2015-12-15)

## Request syntax

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

## Request parameters

|Parameter|Type|Required|Example|Description|
|---------|----|--------|-------|-----------|
|uid|String|Yes|2367\*\*\*\*|The ID of the RAM user. |

|Parameter|Type|Required|Example|Description|
|---------|----|--------|-------|-----------|
| |Array|No| |Request body parameters |
|cluster|String|Yes|c796c60\*\*\*|The ID of the cluster that you want to manage.

 -   If you set `role_type` to `all-clusters`, set this parameter to an empty string. |
|is\_custom|Boolean|No|false|Specifies whether to perform a custom authorization. To perform a custom authorization, set `role_name` to a custom cluster role. |
|role\_name|String|Yes|ops|Specifies the predefined role that you want to assign. Valid values:

 -   `admin`: the administrator role.
-   `ops`: the operations and maintenance \(O&M\) engineer role.
-   `dev`: the developer role.
-   `restricted`: the restricted user role.
-   The custom cluster roles. |
|role\_type|String|Yes|cluster|The authorization type. Valid values:

 -   `cluster`: specifies that the permissions are scoped to a cluster.
-   `namespace`: specifies that the permissions are scoped to a namespace of a cluster.
-   `all-clusters`: specifies that the permissions are scoped to all clusters. |
|namespace|String|No|test|The namespace to which the permissions are scoped. This parameter is required only if you set role\_type to namespace. |
|is\_ram\_role|Boolean|No|false|Specifies whether the permissions are granted to a RAM role. |

## Response syntax

```
HTTP/1.1 200 OK
```

## Response parameters

None

## Examples

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

Sample success responses

`JSON` format

```
HTTP/1.1 200 OK
```

## Error codes

For a list of error codes, visit the [API Error Center](https://error-center.alibabacloud.com/status/product/CS).

