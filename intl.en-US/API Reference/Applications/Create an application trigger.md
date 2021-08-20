# Create an application trigger

You can call the CreateTrigger operation to create an application trigger.

## Debugging

[OpenAPI Explorer automatically calculates the signature value. For your convenience, we recommend that you call this operation in OpenAPI Explorer. OpenAPI Explorer dynamically generates the sample code of the operation for different SDKs.](https://api.aliyun.com/#product=CS&api=CreateTrigger&type=ROA&version=2015-12-15)

## Request syntax

```
POST /clusters/cluster_id/triggers HTTP/1.1
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
|cluster\_id|String|Yes|c68deb844dc6d480eb775be611fdf\*\*\*\*|The ID of the cluster. |

|Parameter|Type|Required|Example|Description|
|---------|----|--------|-------|-----------|
|cluster\_id|String|Yes|c5cdf7e3938bc4f8eb0e44b21a80f\*\*\*\*|The ID of the cluster. |
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
HTTP/1.1 200 OK
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
|id|String|102536|The ID of the trigger. |
|cluster\_id|String|c93095129fc41463aa455d89444fd\*\*\*\*|The ID of the cluster. |
|project\_id|String|default/test-app|The name of the project. |
|type|String|deployment|The type of trigger. Default value: deployment. |
|action|String|redeploy|The action that the trigger performs. For example, a value of `redeploy` indicates that the trigger redeploys the application. |

## Examples

Sample requests

```
POST /clusters/c68deb844dc6d480eb775be611fdf****/triggers HTTP/1.1
Host:cs.aliyuncs.com
Content-Type:application/json

{
  "cluster_id" : "c5cdf7e3938bc4f8eb0e44b21a80f****",
  "project_id" : "default/test-app",
  "action" : "redeploy",
  "type" : "deployment"
}
```

Sample success responses

`XML` format

```
HTTP/1.1 200 OK
Content-Type:application/xml

<CreateTriggerResponse>
    <id>102536</id>
    <cluster_id>c93095129fc41463aa455d89444fd****</cluster_id>
    <project_id>default/test-app</project_id>
    <type>deployment</type>
    <action>redeploy</action>
</CreateTriggerResponse>
```

`JSON` format

```
HTTP/1.1 200 OK
Content-Type:application/json

{
  "id" : "102536",
  "cluster_id" : "c93095129fc41463aa455d89444fd****",
  "project_id" : "default/test-app",
  "type" : "deployment",
  "action" : "redeploy"
}
```

## Error codes

For a list of error codes, visit the [API Error Center](https://error-center.alibabacloud.com/status/product/CS).

