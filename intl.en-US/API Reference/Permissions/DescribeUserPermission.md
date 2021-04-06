# DescribeUserPermission

Queries the permissions that are granted to a specified Resource Access Management \(RAM\) user to manage Container Service for Kubernetes \(ACK\) clusters.

## Debugging

[OpenAPI Explorer automatically calculates the signature value. For your convenience, we recommend that you call this operation in OpenAPI Explorer. OpenAPI Explorer dynamically generates the sample code of the operation for different SDKs.](https://api.aliyun.com/#product=CS&api=DescribeUserPermission&type=ROA&version=2015-12-15)

## Request syntax

```
GET /permissions/users/uid HTTP/1.1
Content-Type:application/json
```

## Request parameters

|Parameter|Type|Required|Example|Description|
|---------|----|--------|-------|-----------|
|uid|String|Yes|21175\*\*\*\*|The ID of the RAM user.

 **Note:** If you want to query the permissions of a RAM role, specify the ID of the RAM role. |

## Response syntax

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

## Response parameters

|Parameter|Type|Example|Description|
|---------|----|-------|-----------|
| |Array| |Response body parameters. |
|resource\_id|String|c1b542\*\*\*\*|The permission settings to manage ACK clusters. Valid values:

 -   `{cluster_id}`: indicates that the permissions are scoped to a cluster.
-   `{cluster_id}/{namespace}`: indicates that the permissions are scoped to a namespace of a cluster.
-   `all-clusters`: indicates that the permissions are scoped to all clusters. |
|resource\_type|String|cluster|The authorization type. Valid values:

 -   `cluster`: indicates that the permissions are scoped to a cluster.
-   `namespace`: indicates that the permissions are scoped to a namespace of a cluster.
-   `console`: indicates that the permissions are scoped to all clusters. |
|role\_name|String|view|The name of the predefined role. If a custom role is assigned, the value is the name of the assigined custom role. |
|role\_type|String|admin|The predefined role. Valid values:

 -   `admin`: the administrator role.
-   `ops`: the operations and maintenance \(O&M\) engineer role.
-   `dev`: the developer role.
-   `restricted`: the restricted user role.
-   `custom`: the custom role. |
|is\_owner|Long|1|Indicates whether the permissions are granted to the cluster owner.

 -   `0`: indicates that the permissions are not granted to the cluster owner.
-   `1`: indicates that the permissions are granted to the cluster owner. The cluster owner is the administrator. |
|is\_ram\_role|Long|1|Indicates whether the permissions are granted to the RAM role. Valid values:

 -   `0`: indicates that the permissions are not granted to the RAM role.
-   `1`: indicates that the permissions are granted to the RAM role. |

## Examples

```
GET /permissions/users/21175**** HTTP/1.1 
Host:cs.aliyuncs.com 
Content-Type:application/json
```

Sample success responses

`XML` format

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

`JSON` format

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

## Error codes

For a list of error codes, visit the [API Error Center](https://error-center.alibabacloud.com/status/product/CS).

