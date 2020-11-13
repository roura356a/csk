# Request method

To send a Container Service for Kubernetes \(ACK\) API request, you must send an HTTP request to the ACK endpoint. You must add the request parameters that correspond to the API operation being called. After you call the API, the system returns a response. The request and response are encoded in UTF-8.

## Endpoint

The endpoint of the ACK API is cs.aliyuncs.com.

## Protocol

You can send requests over HTTP or HTTPS. For security purposes, we recommend that you send requests over HTTPS.

## Request syntax

ACK provides a RESTful API. You can use the following request syntax:

```
HTTPMethod /resource_URI_parameters
RequestHeader
RequestBody
```

Request parameters include both common parameters and operation-specific parameters. Common parameters contain information such as the API version and authentication information.

-   HTTPMethod: the HTTP method used to send a request, such as PUT, POST, GET, and DELETE.
-   resource\_URI\_parameters: the identifier of the resource that you want to access, for example, `/cluster`.
-   RequestHeader: the request parameter, which contains information such as the API version, the host name, and authentication information. For more information, see [Common parameters](/intl.en-US/API Reference/Common parameters.md).
-   RequestBody: the request parameters.

Sample requests:

```
POST /clusters/cluster_id/attach HTTP/1.1
<Common request parameters>
{
    "password": "Hello1234",
    "instances": [
        "i-xxxx",
        "i-yyyy"
    ]
}
```

## Request encoding

All requests and responses are encoded in UTF-8.

