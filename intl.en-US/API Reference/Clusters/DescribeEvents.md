# DescribeEvents

Queries events in a specified Container Service for Kubernetes \(ACK\) cluster.

## Debugging

[OpenAPI Explorer automatically calculates the signature value. For your convenience, we recommend that you call this operation in OpenAPI Explorer. OpenAPI Explorer dynamically generates the sample code of the operation for different SDKs.](https://api.aliyun.com/#product=CS&api=DescribeEvents&type=ROA&version=2015-12-15)

## Request syntax

```
GET /events?cluster_id=String&type=String&page_size=Long&page_number=Long HTTP/1.1
Content-Type:application/json
```

## Request parameters

|Parameter|Type|Required|Example|Description|
|---------|----|--------|-------|-----------|
|cluster\_id|String|No|cf62854ac2130470897be7a27ed1f\*\*\*\*|The ID of the ACK cluster. |
|type|String|No|nodePool\_upgrade|The type of event that you want to query. Valid values:

 -   `cluster_create`: cluster creation.
-   `cluster_scaleout`: cluster scale-out.
-   `cluster_attach`: adding existing nodes.
-   `cluster_delete`: cluster deletion.
-   `cluster_upgrade`: cluster upgrades.
-   `cluster_migrate`: cluster migration.
-   `cluster_node_delete`: node removal.
-   `cluster_node_drain`: node draining.
-   `cluster_modify`: cluster modifications.
-   `cluster_configuration_modify`: modifications to cluster control configurations.
-   `cluster_addon_install`: component installation.
-   `cluster_addon_upgrade`: component upgrades.
-   `cluster_addon_uninstall`: component uninstallation.
-   `runtime_upgrade`: runtime upgrades.
-   `nodepool_upgrade`: node pool upgrades.
-   `nodepool_update`: node pool updates. |
|page\_size|Long|No|50|The number of entries to return on each page. |
|page\_number|Long|No|1|The number of the page to return. |

## Response syntax

```
HTTP/1.1 200 OK
Content-Type:application/json

{
  "events" : [ {
    "event_id" : "String",
    "type" : "String",
    "source" : "String",
    "subject" : "String",
    "time" : "String",
    "cluster_id" : "String",
    "data" : {
      "level" : "String",
      "reason" : "String",
      "message" : "String"
    }
  } ],
  "page_info" : {
    "page_size" : Long,
    "page_number" : Long,
    "total_count" : Long
  }
}
```

## Response parameters

|Parameter|Type|Example|Description|
|---------|----|-------|-----------|
|events|Array of event| |The details of the returned event. |
|event\_id|String|A234-1234-1234|The ID of the event. |
|type|String|nodePool\_upgrade|The type of the event. Valid values:

 -   `cluster_create`: cluster creation.
-   `cluster_scaleout`: cluster scale-out.
-   `cluster_attach`: adding existing nodes.
-   `cluster_delete`: cluster deletion.
-   `cluster_upgrade`: cluster upgrades.
-   `cluster_migrate`: cluster migration.
-   `cluster_node_delete`: node removal.
-   `cluster_node_drain`: node draining.
-   `cluster_modify`: cluster modifications.
-   `cluster_configuration_modify`: modifications to cluster control modifications.
-   `cluster_addon_install`: component installation.
-   `cluster_addon_upgrade`: component upgrades.
-   `cluster_addon_uninstall`: component uninstallation.
-   `runtime_upgrade`: runtime upgrades.
-   `nodepool_upgrade`: node pool upgrades.
-   `nodepool_update`: node pool updates. |
|source|String|/clusters/cf62854ac2130470897be7a27ed1f\*\*\*\*/nodepools|The source of the event. |
|subject|String|nodePool-id|The subject of the event. |
|time|String|2020-12-01T17:31:00Z|The time when the event started. |
|cluster\_id|String|cluster-id|The ID of the ACK cluster. |
|data|object| |The description of the event. |
|level|String|info|The level of the event. |
|reason|String|Started|The state of the event. |
|message|String|Start to upgrade NodePool nodePool/nodePool-A|The details of the event. |
|page\_info|object| |The pagination details. |
|page\_size|Long|50|The number of entries returned per page. |
|page\_number|Long|1|The page number of the returned page. |
|total\_count|Long|3|The total number of entries returned. |

## Examples

Sample requests

```
GET /events?cluster_id=cf62854ac2130470897be7a27ed1f****&type=nodePool_upgrade&page_size=50&page_number=1 HTTP/1.1 
Host:cs.aliyuncs.com 
Content-Type:application/json
```

Sample success responses

`XML` format

```
HTTP/1.1 200 OK
Content-Type:application/xml

<events>
    <event_id>A234-1234-1234</event_id>
    <type>nodePool_upgrade</type>
    <source>/clusters/cf62854ac2130470897be7a27ed1f****/nodepools</source>
    <subject>nodePool-id</subject>
    <time>2020-12-01T17:31:00Z</time>
    <cluster_id>cluster-id</cluster_id>
    <data>
        <level>info</level>
        <reason>Started</reason>
        <message>Start to upgrade NodePool nodePool/nodePool-A</message>
    </data>
</events>
<page_info>
    <page_size>50</page_size>
    <page_number>1</page_number>
    <total_count>3</total_count>
</page_info>
```

`JSON` format

```
HTTP/1.1 200 OK
Content-Type:application/json

{
  "events" : [ {
    "event_id" : "A234-1234-1234",
    "type" : "nodePool_upgrade",
    "source" : "/clusters/cf62854ac2130470897be7a27ed1f****/nodepools",
    "subject" : "nodePool-id",
    "time" : "2020-12-01T17:31:00Z",
    "cluster_id" : "cluster-id",
    "data" : {
      "level" : "info",
      "reason" : "Started",
      "message" : "Start to upgrade NodePool nodePool/nodePool-A"
    }
  } ],
  "page_info" : {
    "page_size" : 50,
    "page_number" : 1,
    "total_count" : 3
  }
}
```

## Error codes

For a list of error codes, visit the [API Error Center](https://error-center.alibabacloud.com/status/product/CS).

