# OpenAckService

Activates a type of Container Service for Kubernetes \(ACK\) service.

## Debugging

[OpenAPI Explorer automatically calculates the signature value. For your convenience, we recommend that you call this operation in OpenAPI Explorer. OpenAPI Explorer dynamically generates the sample code of the operation for different SDKs.](https://api.aliyun.com/#product=CS&api=OpenAckService&type=ROA&version=2015-12-15)

## Request structure

```
POST /service/open?type=String HTTP/1.1 
Content-Type:application/json
```

## Request parameters

|Parameter|Type|Required|Example|Description|
|---------|----|--------|-------|-----------|
|type|String|No|propayasgo|The type of ACK service that you want to activate. Valid values:

 -   `propayasgo`: professional managed Kubernetes service.
-   `edgepayasgo`: edge Kubernetes service.
-   `gspayasgo`: Kubernetes service for Alibaba Cloud Genomics Compute Service \(AGS\). |

## Response structure

```
HTTP/1.1 200
Content-Type:application/json
{
  "request_id" : "String",
  "order_id" : "String"
}
```

## Response parameters

|Parameter|Type|Example|Description|
|---------|----|-------|-----------|
|request\_id|String|20758A-585D-4A41-A9B2-28DA8F4F534F|The ID of the request. |
|order\_id|String|2067\*\*\*\*\*\*\*0374|The ID of the order. |

## Examples

Sample requests

```
POST /service/open?type=propayasgo HTTP/1.1 
Content-Type:application/json
```

Sample success responses

`XML` format

```
HTTP/1.1 200
Content-Type:application/xml
<request_id>20758A-585D-4A41-A9B2-28DA8F4F534F</request_id>
<order_id>2067*******0374</order_id>
```

`JSON` format

```
HTTP/1.1 200
Content-Type:application/json
{
  "request_id" : "20758A-585D-4A41-A9B2-28DA8F4F534F",
  "order_id" : "2067*******0374"
}
```

## Error codes

For a list of error codes, visit the [API Error Center](https://error-center.alibabacloud.com/status/product/CS).

