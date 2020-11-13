# Common parameters

This topic describes the common parameters of the Container Service for Kubernetes \(ACK\) API.

## Common request parameters

Common request parameters must be included in all ACK API requests.

|Parameter|Example|Required|Description|
|:--------|-------|--------|:----------|
|Authorization|acs <yourAccessKeyId\>:<yourSignature\>|Yes|The authentication information that is used to verify the validity of the request. Set the value in the `AccessKeyId:Signature` format.|
|Content-Length|0|Yes|The length of the HTTP request body. The length is defined in RFC 2616.|
|Content-Type|application/json|Yes|The type of the HTTP request body. The type is defined in RFC 2616.|
|Content-MD5|0e30656xxxxxxxxx0bc6f70bbdfe|Yes|The Base64-encoded 128-bit MD5 hash value of the HTTP request body. We recommend that you set this parameter for all requests to prevent the requests from being tampered with.|
|Date|Wed, 16 Dec 2015 11:18:47 GMT|Yes|The time when the request was created. The time must be in GMT. If the deviation between the time when the request was sent and the time when the request was received exceeds 15 minutes, the request is considered invalid.|
|Host|cs.aliyuncs.com|Yes|The endpoint of the ACK API, for example, cs.aliyuncs.com.|
|Accept|application/json|Yes|The type of the response that is required by the client. Valid values: application/json and application/xml.|
|x-acs-version|1.0|Yes|The version number of the API. Set the value to 2015-12-15.|
|x-acs-region-id|cn-beijing|Yes|The ID of the region.|
|x-acs-signature-nonce|f63659d4-10ac-483b-99da-ea8fde61eae3|Yes|A unique, random number that is used to prevent replay attacks. You must use different numbers for different requests.|
|x-acs-signature-method|HMAC-SHA1|Yes|The signature algorithm. Set the value to HMAC-SHA1.|

**Examples**

```
GET /clusters HTTP/1.1
Host: cs.aliyuncs.com
Accept: application/json
User-Agent: cs-sdk-python/0.0.1 (Darwin/15.2.0/x86_64;2.7.10)
x-acs-signature-nonce: f63659d4-10ac-483b-99da-ea8fde61eae3
Authorization: acs <yourAccessKeyId>:<yourSignature>
x-acs-signature-version: 1.0
Date: Wed, 16 Dec 2015 11:18:47 GMT
x-acs-signature-method: HMAC-SHA1
Content-Type: application/json;charset=utf-8
X-Acs-Region-Id: cn-beijing
Content-Length: 0
```

## Common response parameters

The system returns a unique request ID, regardless of whether the call is successful. All responses use a unified format. The responses use the HTTP response format where a `2xx` status code indicates a successful call and a `4xx` or `5xx` status code indicates a failed call.

**XML format**

```
<? xml version="1.0" encoding="UTF-8"? >
<!-Root node of the response-->
<API name + Response>
 | <!--Request ID-->
 | <RequestId>4C467B38-3910-447D-87BC-AC049166F216</RequestId>
 | <!--Response data-->
</API name + Response>
```

**JSON format**

```
{
    "RequestId": "4C467B38-3910-447D-87BC-AC049166F216"
    /* Response data */
}
```

