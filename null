# Create a trigger

You can call the CreateKubernetesTrigger operation to create an application trigger.

## Debugging

[OpenAPI Explorer automatically calculates the signature value. For your convenience, we recommend that you call this operation in OpenAPI Explorer. OpenAPI Explorer dynamically generates the sample code of the operation for different SDKs.](https://api.aliyun.com/#product=CS&api=CreateKubernetesTrigger&type=ROA&version=2015-12-15)

## Request syntax

```
POST /triggers HTTP/1.1 
Content-Type:application/json
{
  "cluster_id" : "String",
  "project_id" : "String",
  "action" : "String",
  "type" : "String"
}
```

## Request parameters

|Parameter|Type|Required|Example|Description|
|---------|----|--------|-------|-----------|
|cluster\_id|String|Yes|c5cdf7e3938bc4f8eb0e44b21a80f\*\*\*\*|The ID of the Container Service for Kubernetes \(ACK\) cluster. |
|project\_id|String|Yes|default/test-app|The name of the project.

 The name consists of the namespace where the application is deployed and the name of the application. The format is `${namespace}/${name}`.

 Example: `default/test-app`. |
|action|String|Yes|redeploy|The action that the trigger performs. Set the value to redeploy.

 `redeploy`: redeploys the resources specified by `project_id`. |
|type|String|No|deployment|The type of trigger. Valid values:

 -   `deployment`: performs actions on Deployments.
-   `application`: performs actions on applications that are deployed in Application Center.

 Default value: `deployment`. |

## Response syntax

```
HTTP/1.1 200
Content-Type:application/json
{
  "id" : "String",
  "cluster_id" : "String",
  "project_id" : "String",
  "type" : "String",
  "action" : "String"
}
```

## Response parameters

|Parameter|Type|Example|Description|
|---------|----|-------|-----------|
|id|String|111|The ID of the trigger. |
|cluster\_id|String|c5cdf7e3938bc4f8eb0e44b21a80f\*\*\*\*|The ID of the ACK cluster. |
|project\_id|String|default/test-app|The name of the project. |
|type|String|deployment|The type of trigger.

 Valid values:

 -   `deployment`: performs actions on Deployments.
-   `application`: performs actions on applications that are deployed in Application Center. |
|action|String|redeploy|The action that the trigger performs. For example, a value of `redeploy` indicates that the trigger redeploys the application. |

## Examples

Sample requests

```
POST /triggers HTTP/1.1 
Content-Type:application/json
{
  "cluster_id" : "c5cdf7e3938bc4f8eb0e44b21a80f****",
  "project_id" : "redeploy",
  "action" : "default/test-app",
  "type" : "deployment"
}
```

Sample success responses

`XML` format

```
HTTP/1.1 200 OK
Content-Type:application/xml

<id>111</id>
<cluster_id>c5cdf7e3938bc4f8eb0e44b21a80f****</cluster_id>
<project_id>default/test-app</project_id>
<type>deployment</type>
<action>redeploy</action>
```

`JSON` format

```
HTTP/1.1 200 OK
Content-Type:application/json

{
  "id" : "111",
  "cluster_id" : "c5cdf7e3938bc4f8eb0e44b21a80f****",
  "project_id" : "default/test-app",
  "type" : "deployment",
  "action" : "redeploy"
}
```

## Error codes

For a list of error codes, visit the [API Error Center](https://error-center.alibabacloud.com/status/product/CS).

