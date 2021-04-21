# DescribeClusterAddonsUpgradeStatus

Queries the upgrade status of the specified components in a Container Service for Kubernetes \(ACK\) cluster.

## Debugging

[OpenAPI Explorer automatically calculates the signature value. For your convenience, we recommend that you call this operation in OpenAPI Explorer. OpenAPI Explorer dynamically generates the sample code of the operation for different SDKs.](https://api.aliyun.com/#product=CS&api=DescribeClusterAddonsUpgradeStatus&type=ROA&version=2015-12-15)

## Request syntax

```
GET /clusters/[ClusterId]/components/upgradestatus?componentIds=["String"] HTTP/1.1 
Content-Type:application/json
```

## Request parameters

|Parameter|Type|Required|Example|Description|
|---------|----|--------|-------|-----------|
|ClusterId|String|Yes|c23421cfa74454bc8b37163fd19af\*\*\*\*|The ID of the ACK cluster. |

|Parameter|Type|Required|Example|Description|
|---------|----|--------|-------|-----------|
|componentIds|Array of String|No|metric-server|The name of the components that you want to query. |

## Response syntax

```
HTTP/1.1 200
Content-Type:application/json
{
  "key" : {
    "addon_info" : {
      "component_name" : "String",
      "version" : "String",
      "ready_to_upgrade" : "String"
    },
    "tasks" : {
      "created_at" : "String",
      "finished_at" : "String",
      "status" : "String",
      "is_canceled" : Boolean
    }
  }
}
```

## Response parameters

|Parameter|Type|Example|Description|
|---------|----|-------|-----------|
| |Map| |The returned data. |
| |object| |The details of the upgrade status of the queried components. |
|addon\_info|object| |The information about the component. |
|component\_name|String|cloud-controller-manager|The name of the component. |
|version|String|v1.9.3.340-g9830b58-aliyun|The current version of the component. |
|ready\_to\_upgrade|String|true|Indicates whether the component is upgradable. Valid values:

 -   `true`: The component is upgradable.
-   `false`: The component is not upgradable. |
|tasks|object| |The details of the upgrade task. |
|created\_at|String|2020-12-28T17:00:50Z|The beginning time of the task. |
|finished\_at|String|2020-12-28T17:05:36Z|The end time of the task. |
|status|String|Success|The state of the upgrade task. |
|is\_canceled|Boolean|false|Indicates whether the upgrade task was canceled. Valid values:

 -   `true`: The upgrade task was canceled.
-   `false`: The upgrade task was not canceled. |

## Examples

Sample requests

```
GET /clusters/[ClusterId]/components/upgradestatus?componentIds=["metric-server"] HTTP/1.1 
Content-Type:application/json
```

Sample success responses

`XML` format

```
HTTP/1.1 200 OK
Content-Type:application/xml

<cloud-controller-manager>
    <tasks>
        <created_at>2020-12-28T17:01:55Z</created_at>
        <finished_at>2020-12-28T17:05:11Z</finished_at>
        <is_canceled>false</is_canceled>
        <status>Success</status>
    </tasks>
    <addon_info>
        <component_name>cloud-controller-manager</component_name>
        <version>v1.9.3.340-g9830b58-aliyun</version>
        <ready_to_upgrade>true</ready_to_upgrade>
    </addon_info>
</cloud-controller-manager>
<metric-server>
    <tasks>
        <created_at>2020-12-28T17:00:32Z</created_at>
        <finished_at>2020-12-28T17:05:54Z</finished_at>
        <is_canceled>false</is_canceled>
        <status>Success</status>
    </tasks>
    <addon_info>
        <component_name>metric-server</component_name>
        <version/>
        <ready_to_upgrade>true</ready_to_upgrade>
    </addon_info>
</metric-server>
```

`JSON` format

```
HTTP/1.1 200 OK
Content-Type:application/json

{
  "cloud-controller-manager" : {
    "tasks" : {
      "created_at" : "2020-12-28T17:01:55Z",
      "finished_at" : "2020-12-28T17:05:11Z",
      "is_canceled" : false,
      "status" : "Success"
    },
    "addon_info" : {
      "component_name" : "cloud-controller-manager",
      "version" : "v1.9.3.340-g9830b58-aliyun",
      "ready_to_upgrade" : "true"
    }
  },
  "metric-server" : {
    "tasks" : {
      "created_at" : "2020-12-28T17:00:32Z",
      "finished_at" : "2020-12-28T17:05:54Z",
      "is_canceled" : false,
      "status" : "Success"
    },
    "addon_info" : {
      "component_name" : "metric-server",
      "version" : "",
      "ready_to_upgrade" : "true"
    }
  }
}
```

## Error codes

For a list of error codes, visit the [API Error Center](https://error-center.alibabacloud.com/status/product/CS).

