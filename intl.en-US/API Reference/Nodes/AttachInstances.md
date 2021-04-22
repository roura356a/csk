# AttachInstances

Adds existing Elastic Compute Service \(ECS\) instances to a specified Container Service for Kubernetes \(ACK\) cluster.

## Debugging

[OpenAPI Explorer automatically calculates the signature value. For your convenience, we recommend that you call this operation in OpenAPI Explorer. OpenAPI Explorer dynamically generates the sample code of the operation for different SDKs.](https://api.aliyun.com/#product=CS&api=AttachInstances&type=ROA&version=2015-12-15)

## Request syntax

```
POST /clusters/ClusterId/attach HTTP/1.1 
Content-Type:application/json
{
  "instances" : [ "String" ],
  "key_pair" : "String",
  "password" : "String",
  "format_disk" : Boolean,
  "keep_instance_name" : Boolean,
  "is_edge_worker" : Boolean,
  "nodepool_id" : "String",
  "image_id" : "String",
  "cpu_policy" : "String",
  "user_data" : "String",
  "rds_instances" : [ "String" ],
  "runtime" : {
    "name" : "String",
    "version" : "String"
  },
  "tags" : [ {
    "key" : "String",
    "value" : "String"
  } ]
}
```

## Request parameters

|Parameter|Type|Required|Example|Description|
|---------|----|--------|-------|-----------|
|ClusterId|String|Yes|c106f377e16f34eb1808d6b9362c9\*\*\*\*|The ID of the ACK cluster. |

|Parameter|Type|Required|Example|Description|
|---------|----|--------|-------|-----------|
|instances|Array of String|Yes|i-2zed0sswuau6o89b\*\*\*\*|The IDs of the ECS instances that you want to add. |
|key\_pair|String|Yes|secrity-key|The name of the key pair that is used to log on to the nodes. You must set key\_pair or `login_password`. |
|password|String|Yes|Hello1234|The SSH logon password that is used to log on to the nodes. You must set login\_password or `key_pair`. The password must be 8 to 30 characters in length, and must contain at least three of the following character types: uppercase letters, lowercase letters, digits, and special characters. It cannot contain backward slashes \(\\\) or double quotation marks \("\).

For security considerations, the password is encrypted during data transfer. |
|format\_disk|Boolean|No|false|Specifies whether to mount data disks. Valid values:

-   `true`: mounts data disks to store container data and images.
-   `false`: does not mount data disks to store container data or images

Default value: `false`.

**Note:** : After a data disk is mounted, the original data that is stored on the disk is cleared. Before you mount a data disk to the nodes, we recommend that you back up the disk data.

A data disk is mounted based on the following rules:

-   If data disks have been mounted to the ECS instances and the file system of the last data disk is not initialized, the system automatically formats the last data disk to ext4 and mounts this data disk to /var/lib/docker and /var/lib/kubelet.
-   If no data disk is mounted to the ECS instances, the system does not purchase a new data disk. |
|keep\_instance\_name|Boolean|No|true|Specifies whether to keep the instance name. Valid values:

-   `true`: keeps the instance name.
-   `false`: does not keep the instance name.

Default value:`true`. |
|is\_edge\_worker|Boolean|No|false|Specifies whether the nodes that you want to add are Edge Node Service \(ENS\) nodes. Valid values:

-   `true`: The nodes that you want to add are ENS nodes.
-   `false`: The nodes that you want to add are not ENS nodes.

Default value: `false`.

**Note:** : If the nodes you want to add are ENS nodes, you must set this parameter to `true`. This allows you to identify these ENS nodes. |
|nodepool\_id|String|No|np615c0e0966124216a0412e10afe0\*\*\*\*|The ID of the node pool to which the nodes are added. If you do not set this parameter, the nodes are added to a default node pool. |
|image\_id|String|No|aliyun\_2\_1903\_x64\_20G\_alibase\_20200529.vhd|The ID of the custom image. If you do not set this parameter, the default system image is used.

**Note:** : If you specify a custom image, the custom image is used to deploy the operating systems on the system disks of the nodes. |
|cpu\_policy|String|No|none|The CPU management policy of the nodes. The following policies are supported for clusters of Kubernetes 1.12.6 and later:

-   `static`: This policy allows pods with specific resource characteristics on the node to be granted enhanced CPU affinity and exclusivity.
-   `none`: This policy indicates that the default CPU affinity is used. This is the default policy.

Default value: `none`. |
|user\_data|String|No|IyEvdXNyL2Jpbi9iYXNoCmVjaG8gIkhlbGxvIEFDSyEi|The user-defined data. For more information, see [Prepare user-defined data](https://www.alibabacloud.com/help/doc-detail/49121.htm). |
|rds\_instances|Array of String|No|rm-67yawhau\*\*\*\*|The IDs of the Relational Database Service \(RDS\) instances. |
|runtime|[runtime](/intl.en-US/API Reference/Commonly used parameters.md)|No| |The name of the container runtime. |
|tags|Array of [tag](/intl.en-US/API Reference/Commonly used parameters.md)|No| |The labels of the nodes. You must add labels based on the following rules:

-   A label is a case-sensitive key-value pair. You can add up to 20 labels to a node.
-   A key must be unique and 1 to 64 characters in length. A value can be empty and can contain up to 128 characters. Neither keys or values can start with aliyun, acs:, https://, or http://. For more information, see [Labels and Selectors](https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/#syntax-and-character-set). |

## Response syntax

```
HTTP/1.1 200
Content-Type:application/json
{
  "list" : [ {
    "code" : "String",
    "instanceId" : "String",
    "message" : "String"
  } ],
  "task_id" : "String"
}
```

## Response parameters

|Parameter|Type|Example|Description|
|---------|----|-------|-----------|
|list|Array of list| |The details of the added nodes. |
|code|String|200|The code that indicates the task result. |
|instanceId|String|i-2ze0lgm3y6iylcbt\*\*\*\*|The IDs of the added ECS instances. |
|message|String|successful|Indicates whether the ECS instances are successfully added to the ACK cluster. |
|task\_id|String|T-5a544aff80282e39ea000039|The ID of the task. |

## Examples

Sample requests

```
POST /clusters/c106f377e16f34eb1808d6b9362c9****/attach HTTP/1.1 
Content-Type:application/json
{
  "instances" : [ "i-2zed0sswuau6o89b****" ],
  "key_pair" : "secrity-key",
  "password" : "Hello1234",
  "format_disk" : false,
  "keep_instance_name" : true,
  "is_edge_worker" : false,
  "nodepool_id" : "np615c0e0966124216a0412e10afe0****",
  "image_id" : "aliyun_2_1903_x64_20G_alibase_20200529.vhd",
  "cpu_policy" : "none",
  "user_data" : "IyEvdXNyL2Jpbi9iYXNoCmVjaG8gIkhlbGxvIEFDSyEi",
  "rds_instances" : [ "rm-67yawhau****" ],
  "runtime" : {
    "name" : "docker",
    "version" : "19.03.5"
  },
  "tags" : [ {
    "key" : "env",
    "value" : "prod"
  } ]
}
```

Sample success responses

`XML` format

```
HTTP/1.1 200 OK
Content-Type:application/xml

<list>
    <code>200</code>
    <instanceId>i-2ze0lgm3y6iylcbt****</instanceId>
    <message>successful</message>
</list>
<task_id>T-5a544aff80282e39ea000039</task_id>
```

`JSON` format

```
HTTP/1.1 200 OK
Content-Type:application/json

{
  "list" : [ {
    "code" : "200",
    "instanceId" : "i-2ze0lgm3y6iylcbt****",
    "message" : "successful"
  } ],
  "task_id" : "T-5a544aff80282e39ea000039"
}
```

## Error codes

For a list of error codes, visit the [API Error Center](https://error-center.alibabacloud.com/status/product/CS).

