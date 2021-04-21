# DescribeClusterAddonsVersion

Queries the details of all components that are installed in a specified Container Service for Kubernetes \(ACK\) cluster.

## Debugging

[OpenAPI Explorer automatically calculates the signature value. For your convenience, we recommend that you call this operation in OpenAPI Explorer. OpenAPI Explorer dynamically generates the sample code of the operation for different SDKs.](https://api.aliyun.com/#product=CS&api=DescribeClusterAddonsVersion&type=ROA&version=2015-12-15)

## Request syntax

```
GET /clusters/ClusterId/components/version HTTP/1.1 
Content-Type:application/json
```

## Request parameters

|Parameter|Type|Required|Example|Description|
|---------|----|--------|-------|-----------|
|ClusterId|String|Yes|c82e6987e2961451182edacd74faf\*\*\*\*|The ID of the ACK cluster. |

## Response syntax

```
HTTP/1.1 200
Content-Type:application/json
{
  "key" : {
    "component_name" : "String",
    "version" : "String",
    "next_version" : "String",
    "changed" : "String",
    "can_upgrade" : Boolean,
    "policy" : "String",
    "ready_to_upgrade" : "String",
    "exist" : Boolean,
    "required" : Boolean,
    "message" : "String",
    "value" : "String"
  }
}
```

## Response parameters

|Parameter|Type|Example|Description|
|---------|----|-------|-----------|
| |Map| |The response body parameters. |
| |object| |The details of the component. |
|component\_name|String|ack-node-problem-detector|The name of the component. |
|version|String|1.2.1|The current version of the component. |
|next\_version|String|1.2.1|The version to which the component can be upgraded. |
|changed|String|false|Indicates whether the component is upgraded. Valid values:

 -   `true`: The component is upgraded.
-   `false`: The component is not upgraded. |
|can\_upgrade|Boolean|false|Indicates whether the component is upgradable. Valid values:

 -   `true`: The component is upgradable.
-   `false`: The component is not upgradable. |
|policy|String|overwrite|The upgrade policy of the component.

 -   `overwrite`: The component configurations of the later version overwrite those of the earlier version.

 Default value: `overwrite`.`` |
|ready\_to\_upgrade|String|false|Indicates whether the component meets the upgrade conditions. Valid values:

 -   `true`: The component meets the upgrade conditions.
-   `false`: The component does not meet the upgrade conditions. |
|exist|Boolean|true|Indicates whether the component is installed. Valid values:

 -   `true`: The component is installed.
-   `false`: The component is not installed. |
|required|Boolean|false|Indicates whether the component is required in an ACK cluster. Valid values:

 -   `true`: The component is required in an ACK cluster.
-   `false`: The component is not required in an ACK cluster. |
|message|String|A plug-in that can detects exceptions and reports them to the Kubernetes event center.|Additional information about whether the component is upgradable. |
|value|String|npd:\\n \# Custom monitor definitions to add to Node Problem Detector \*\*\*\*|The custom parameter settings of a component when it is being installed. |

## Examples

Sample requests

```
GET /clusters/c82e6987e2961451182edacd74faf****/components/version HTTP/1.1 
Content-Type:application/json
```

Sample success responses

`XML` format

```
HTTP/1.1 200 OK
Content-Type:application/xml

<ack-node-problem-detector>
    <exist>true</exist>
    <component_name>ack-node-problem-detector</component_name>
    <can_upgrade>false</can_upgrade>
    <ready_to_upgrade>false</ready_to_upgrade>
    <message>A plug-in that detects node exceptions and reports them to the Kubernetes event center.</message>
    <version>1.2.1</version>
    <value>npd:
  # Custom monitor definitions to add to Node Problem Detector ****</value>
    <required>false</required>
    <next_version>1.2.1</next_version>
    <changed>false</changed>
    <policy>overwrite</policy>
</ack-node-problem-detector>
```

`JSON` format

```
HTTP/1.1 200 OK
Content-Type:application/json

{
  "ack-node-problem-detector" : {
    "exist" : "true",
    "component_name" : "ack-node-problem-detector",
    "can_upgrade" : "false",
    "ready_to_upgrade" : "false",
    "message" : "A plug-in that detects node exceptions and reports them to the Kubernetes event center.",
    "version" : "1.2.1",
    "value" : "npd:\n  # Custom monitor definitions to add to Node Problem Detector ****",
    "required" : "false",
    "next_version" : "1.2.1",
    "changed" : "false",
    "policy" : "overwrite"
  }
}
```

## Error codes

For a list of error codes, visit the [API Error Center](https://error-center.alibabacloud.com/status/product/CS).

