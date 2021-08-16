# Common parameters

This topic describes the common parameters of the Container Service for Kubernetes \(ACK\) API.

## Common request parameters

Common request parameters are used to identify general information such as the API operation to be called, user identity, and signature. These parameters must be included in each API request.

Common request parameters are carried in the header of an HTTP request. The following table describes the parameters.

|Parameter|Required|Description|
|---------|--------|-----------|
|x-acs-action|Yes|The name of the API operation. For more information about valid values, see [API overview](/intl.en-US/API Reference/API overview.md).|
|x-acs-version|Yes|The version of the API operation in the YYYY-MM-DD format. Example: 2018-01-01.|
|x-acs-signature-nonce|No|A unique random number that is used to prevent network replay attacks. A different random number is required for each request.|
|x-acs-signature-method|Yes if the request is non-anonymous|The encryption method of the signature string. Set the value to HMAC-SHA1.|
|Authorization|Yes if the request is non-anonymous|The authentication information that is used to check the authenticity of a request. The string must follow the `AccessKeyId:Signature` format.-   AccessKeyId represents the AccessKey ID of the user.
-   Signature represents the signature of the request. For more information about valid values, see [Sign API requests](/intl.en-US/API Reference/Sign API requests.md). |
|Date|Yes|The specified Greenwich Mean Time \(GMT\) in HTTP 1.1, for example, Tue 9 Apr 2019 07:35:29 GMT.|
|Accept|No|The type of response that is required by the client. Valid values: application/json and application/xml. Default value: application/xml. We recommend that you use application/json.|
|Content-MD5|No|The Base64-encoded 128-bit MD5 hash value of the HTTP request body.|

**Sample requests**

**Note:** In the following sample requests, the request parameters in the request URLs are not encoded for demonstration purposes. In actual requests, the request parameters must be encoded in the request URLs.

-   Sample GET request

    ```
    GET https://demo-product.aliyuncs.com/path?foo1=bar1&foo2=bar2 HTTP/1.1
    Authorization: acs DemoAccessKey:31nTIpResD/0C8gb+ChUeuvsxlw=
    Host: demo-product.aliyuncs.com
    Date: Thu, 17 Nov 2018 18:49:58 GMT
    Accept: application/json
    x-acs-action: demoAction
    x-acs-version: 2018-01-01
    x-acs-signature-nonce: 51284580-614a-11eb-8a9b-9dcfec8d61b7
    x-acs-signature-method: HMAC-SHA1
    ```

-   Sample POST request \(application/json\)

    ```
    POST https://demo-product.aliyuncs.com/path?foo=bar HTTP/1.1
    Authorization: acs DemoAccessKey:31nTIpResD/0C8gb+ChUeuvsxlw=
    Host: demo-product.aliyuncs.com
    Date: Thu, 17 Nov 2018 18:49:58 GMT
    Accept: application/json
    Content-Type: application/json; charset=utf-8
    Content-Length: 135
    Content-MD5: MACiECZtnLiNkNS1v5ZCAA=1
    x-acs-action: demoAction
    x-acs-version: 2018-01-01
    x-acs-signature-nonce: 51284580-614a-11eb-8a9b-9dcfec8d61b7
    x-acs-signature-method: HMAC-SHA1
    
    {"demoKey":"demoValue", ... }
    ```


## Common response parameters

Every response returns a unique request ID regardless of whether the request is successful. All responses use a unified format. The responses use the HTTP response format where a `2xx` status code indicates a successful call and a `4xx` or `5xx` status code indicates a failed call.

**XML format**

```
<?xml version="1.0" encoding="UTF-8"?>
<!--Root node of the response-->
<Operation name + Response>
 | <!--Request ID-->
 | <RequestId>4C467B38-3910-447D-87BC-AC049166F216</RequestId>
 | <!--Response data-->
</Operation name + Response>
```

**JSON format**

```
{
    "RequestId": "4C467B38-3910-447D-87BC-AC049166F216"
    /* Response data */
}
```

