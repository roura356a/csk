# Signature method

You must sign all API requests to ensure security. Alibaba Cloud uses the request signature to verify the identity of the API caller. Each API request must contain the signature, regardless of whether the request is sent over HTTP or HTTPS.

## Overview

To sign a RESTful API request, add the Authorization field to the request header in the following format:

```
Authorization:acs:AccessKeyId:Signature
```

<input tabindex="-1" class="dnt" readonly="readonly" value="Do Not Translate"\>

-   acs: the abbreviation for Alibaba Cloud Service. This is a fixed field and cannot be modified.
-   AccessKeyId: the AccessKey ID that is used to call the API.
-   Signature: the signature generated after the request is symmetrically encrypted by using the AccessKey secret.

## Signature calculation

The signature algorithm complies with the HMAC-SHA1 specifications in RFC 2104. The AccessKey secret is used to calculate the Hash-based Message Authentication Code \(HMAC\) value of an encoded and formatted query string. The HMAC value is then used as the signature. Some parameters in a request are used to calculate the signature. Therefore, the signature of a request varies based on the API request parameters.

```
Signature = Base64( HMAC-SHA1( AccessSecret, UTF-8-Encoding-Of(
StringToSign)) )
```

To calculate a signature, perform the following steps:

1.  Compose a string-to-sign.

    A string-to-sign is a string assembled by using the elements in an API request. The string is used to calculate the signature and contains the following elements:

    -   HTTP headers
    -   Canonicalized headers of Alibaba Cloud \(CanonicalizedHeaders\)
    -   Canonicalized resource \(CanonicalizedResource\)
    -   Body
    A string-to-sign must be created in the following format:

    ```
    StringToSign = 
           //HTTP headers
            HTTP-Verb + "\n" +
            Accept + "\n" +
            Content-MD5 + "\n" +//The request body encrypted by using the MD5 algorithm. Content-Type + "\n" +
            Date + "\n" +
           //Canonicalized headers of Alibaba Cloud (CanonicalizedHeaders).
            CanonicalizedHeaders +
           //Canonicalized resource (CanonicalizedResource).
            CanonicalizedResource
    ```

    Sample original requests:

    ```
    POST /stacks? name=test_alert&status=COMPLETE HTTP/1.1
    Host: ***.aliyuncs.com
    Accept: application/json
    Content-MD5: ChDfdfwC+Tn874znq7Dw7Q==
    Content-Type: application/x-www-form-urlencoded;charset=utf-8
    Date: Thu, 22 Feb 2018 07:46:12 GMT 
    x-acs-signature-nonce: 550e8400-e29b-41d4-a716-446655440000
    x-acs-signature-method: HMAC-SHA1
    x-acs-signature-version: 1.0
    x-acs-version: 2016-01-02
    ```

    Sample canonicalized requests:

    ```
    POST
    application/json
    ChDfdfwC+Tn874znq7Dw7Q==
    application/x-www-form-urlencoded;charset=utf-8
    Thu, 22 Feb 2018 07:46:12 GMT
    x-acs-signature-nonce: 550e8400-e29b-41d4-a716-446655440000
    x-acs-signature-method:HMAC-SHA1
    x-acs-signature-version:1.0
    x-acs-version:2016-01-02
    /stacks? name=test_alert&status=COMPLETE
    ```

2.  Add the signature string.

    Add the calculated signature in the following format to the request header:

    `Authorization: acs AccessKeyId:Signature`


## HTTP headers

HTTP headers in a string-to-sign must contain the following parameters. Parameters must be arranged in alphabetical order. If a parameter has no value, set the value to \\n.

-   Accept: the response type required by the client. Valid values: application/json and application/xml.
-   Content-MD5: the Base64-encoded 128-bit MD5 hash value of the request body.
-   Content-Type: the type of the HTTP request body defined in RFC 2616.
-   Date: the GMT time specified in HTTP 1.1. For example, Wed, 05 Sep. 2012 23:00:00 GMT.

**Note:** You do not need to specify your AccessKey pair in the HTTP header.

Sample original headers:

```
Accept: application/json
Content-MD5: ChDfdfwC+Tn874znq7Dw7Q==
Content-Type: application/x-www-form-urlencoded;charset=utf-8
Date: Thu, 22 Feb 2018 07:46:12 GMT
```

Sample canonicalized headers:

```
application/json
ChDfdfwC+Tn874znq7Dw7Q==
application/x-www-form-urlencoded;charset=utf-8
Thu, 22 Feb 2018 07:46:12 GMT
```

## Canonicalized headers of Alibaba Cloud \(CanonicalizedHeaders\)

Canonicalized headers are non-standard HTTP headers of Alibaba Cloud. The parameters are prefixed with `x-acs-` in a request. A request must contain the following parameters:

-   x-acs-signature-nonce: a unique, random number used to prevent replay attacks. You must use different random numbers for different requests.
-   x-acs-signature-version: the version of the signature encryption algorithm. Set the value to 1.0.
-   x-acs-version: the version number of the API. For more information, see the API documentation of each service.

To compose canonicalized headers of Alibaba Cloud, perform the following steps:

1.  Convert the names of all HTTP request headers prefixed with `x-acs-` to lowercase letters. For example, convert `X-acs-OSS-Meta-Name: TaoBao` into `x-acs-oss-meta-name: TaoBao`.
2.  Arrange all HTTP request headers that are obtained from the preceding step in alphabetical order.
3.  Delete all spaces on each side of a delimiter between the request header and its content. For example, convert `x-acs-oss-meta-name: TaoBao,Alipay` into `x-acs-oss-meta-name:TaoBao,Alipay`.
4.  Separate all headers and content with delimiters \(\\n\) to form the final canonicalized headers.

Sample original headers:

```
x-acs-signature-nonce: 550e8400-e29b-41d4-a716-446655440000
x-acs-signature-method: HMAC-SHA1
x-acs-signature-version: 1.0
x-acs-version: 2016-01-02GMT
```

Sample canonicalized headers:

```
x-acs-signature-nonce:550e8400-e29b-41d4-a716-446655440000
x-acs-signature-method:HMAC-SHA1
x-acs-signature-version:1.0
x-acs-version:2016-01-02
```

## Canonicalized resource \(CanonicalizedResource\)

CanonicalizedResource represents the specification description of the resource to be accessed. Arrange sub-resources along with query parameters in alphabetical order and separate them with ampersands \(&\) to generate a sub-resource string. The sub-resource string consists of all parameters that follow the question mark \(?\).

Sample original requests:

```
/stacks? status=COMPLETE&name=test_alert
```

Sample canonicalized requests:

```
/stacks? name=test_alert&status=COMPLETE
```

## Body

Encrypt the request body by using the MD5 algorithm, encode the request body in Base64, and add the Base64-encoded string to the Content-MD5 parameter.

