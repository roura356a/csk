# GetKubernetesTrigger

Queries application triggers of a specified Container Service for Kubernetes \(ACK\) cluster.

## Debugging

[OpenAPI Explorer automatically calculates the signature value. For your convenience, we recommend that you call this operation in OpenAPI Explorer. OpenAPI Explorer dynamically generates the sample code of the operation for different SDKs.](https://api.aliyun.com/#product=CS&api=GetKubernetesTrigger&type=ROA&version=2015-12-15)

## Request syntax

```
GET /triggers/ClusterId?Namespace=String&Type=String&Name=String&action=String HTTP/1.1 
Content-Type:application/json
```

## Request parameters

|Parameter|Type|Required|Example|Description|
|---------|----|--------|-------|-----------|
|ClusterId|String|Yes|c5cdf7e3938bc4f8eb0e44b21a80f\*\*\*\*|The ID of the ACK cluster. |

|Parameter|Type|Required|Example|Description|
|---------|----|--------|-------|-----------|
|Namespace|String|Yes|default|The name of the namespace. |
|Type|String|No|deployment|The type of the trigger. Valid values:

 -   `deployment`: performs actions on Deployments.
-   `application`: performs actions on applications that are deployed through Application Center.

 Default value: `deployment`.

 If you do not specify this parameter, triggers are not filtered by type. |
|Name|String|Yes|web-server|The name of the application. |
|action|String|No|redeploy|The action that the trigger performs. Valid value:

 `redeploy`: redeploys the resource specified by `project_id`.

 If you do not specify this parameter, triggers are not filtered by action. |

## Response syntax

```
HTTP/1.1 200
Content-Type:application/json
[ {
  "id" : "String",
  "name" : "String",
  "cluster_id" : "String",
  "project_id" : "String",
  "type" : "String",
  "action" : "String"
} ]
```

## Response parameters

|Parameter|Type|Example|Description|
|---------|----|-------|-----------|
| |Array of trigger\_info| |The details of the triggers returned. |
|id|String|1234|The ID of the trigger. |
|name|String|test|The name of the trigger. |
|cluster\_id|String|c259f563386444ebb8d7\*\*|The ID of the ACK cluster. |
|project\_id|String|default/test-app|The name of the project for which the trigger is created.

 The name consists of the namespace where the application is deployed and the name of the application. The format is `${namespace}/${name}`. Example: default/test-app. |
|type|String|deployment|The type of the trigger.

 Valid values:

 -   `deployment`: performs actions on Deployments.
-   `application`: performs actions on applications that are deployed through Application Center.

 Default value: `deployment`. |
|action|String|redeploy|The action that the trigger performs. Valid value:

 `redeploy`: redeploys the resource specified by project\_id. |

## Examples

Sample requests

```
GET /triggers/c5cdf7e3938bc4f8eb0e44b21a80f****?Namespace=default&Type=deployment&Name=web-server&action=redeploy HTTP/1.1 
Content-Type:application/json
```

Sample success responses

`XML` format

```
HTTP/1.1 200 OK
Content-Type:application/xml

<0>
    <id>1234</id>
    <name>test</name>
    <cluster_id>c259f563386444ebb8d7**</cluster_id>
    <project_id>default/test-app</project_id>
    <type>deployment</type>
    <action>redeploy</action>
</0>
```

`JSON` format

```
HTTP/1.1 200 OK
Content-Type:application/json

[ {
  "id" : "1234",
  "name" : "test",
  "cluster_id" : "c259f563386444ebb8d7**",
  "project_id" : "default/test-app",
  "type" : "deployment",
  "action" : "redeploy"
} ]
```

## Error codes

For a list of error codes, visit the [API Error Center](https://error-center.alibabacloud.com/status/product/CS).

