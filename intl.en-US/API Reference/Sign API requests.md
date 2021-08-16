---
keyword: [request signing, signature, ACK, K8s]
---

# Sign API requests

To ensure secure API calls, Alibaba Cloud verifies signatures to authenticate each API request. Each API request must contain a signature, regardless of whether the request is sent over HTTP or HTTPS. This topic describes how to sign API requests.

For each HTTP or HTTPS request, Alibaba Cloud uses the request signature to verify the identity of the API caller. You must perform the following steps to sign a request:

-   [Step 1: Construct a canonicalized header](#section_qpi_dum_9sm)
-   [Step 2: Construct a canonicalized resource](#section_55g_u17_t1u)
-   [Step 3: Construct a string-to-sign](#section_wq0_lip_qrc)
-   [Step 4: Calculate the signature string](#section_aye_52m_eyh)
-   [Step 5: Add the signature to the request](#section_oqz_e02_4zw)

## Step 1: Construct a canonicalized header

Canonicalized headers are non-standard HTTP headers. Canonicalized headers refer to the parameters that are prefixed with `x-acs-` in the request. You can perform the following steps to construct canonicalized headers:

-   Convert all HTTP header field names prefixed with `x-acs-` to lowercase letters. For example, convert `X-acs-Meta-Name: TaoBao` to `x-acs-meta-name: TaoBao`. Header field names are not case-sensitive. We recommend that you use lowercase letters.
-   If a header field value is too long, replace specific delimiters with spaces. The delimiters include `\t`, `\n`, `\r`, and `\f`.
-   Sort all HTTP headers that you obtained in the preceding step in ascending lexicographic order.
-   Delete all spaces on both sides of the delimiter that separates the field name and field value of each header. For example, convert `x-acs-oss-meta-name : TaoBao,Alipay` to `x-acs-oss-meta-name:TaoBao,Alipay`.
-   Append the `\n` delimiter to the end of each header, and then concatenate all the headers.

## Step 2: Construct a canonicalized resource

Canonicalized resources refer to the canonical descriptions of the resources that you want to access. You can perform the following steps to construct canonicalized resources:

1.  Sort the parameters in the request query string by name in lexicographic order, and concatenate the parameters with an `ampersand (&)` delimiter to generate the sorted query string.

    Skip this step if the request does not contain a query string.

2.  Concatenate the requested resource path and the sorted query string with a question mark `(?)`. The resource path refers to the string between the host address and the query string, which includes the forward slash `(/)` following the host address but excludesthe question mark `(?)` that precedes the query string.This generates a canonicalized resource. If the request does not contain a query string, use the requested resource path as the canonicalized resource.

Example

Original request URL:

```
http://demo-product.aliyuncs.com/instances?status=ONLINE&group=test_group
```

Canonicalized resource:

```
/instances?group=test_group&status=ONLINE
```

## Step 3: Construct a string-to-sign

Construct a string-to-sign based on the following pseudocode:

```
StringToSign = 
    HTTP-Verb + "\n" +
    Accept + "\n" +
    Content-MD5 + "\n" +
    Content-Type + "\n" +
    Date + "\n" +
    CanonicalizedHeaders +
    CanonicalizedResource
```

|Parameter|Description|
|---------|-----------|
|HTTP-Verb|The HTTP method in uppercase letters, such as POST and GET.|
|Accept|The value of the Accept header field. Set the value to an empty string if this header does not exist.|
|Content-MD5|The value of the Content-MD5 header field. Set the value to an empty string if this header does not exist.|
|Content-Type|The value of the Content-Type header field. Set the value to an empty string if this header does not exist.|
|Date|The value of the Date header field.|
|CanonicalizedHeaders|The canonicalized header that you obtained in [Step 1: Construct a canonicalized header](#section_qpi_dum_9sm).|
|CanonicalizedResource|The canonicalized resource that you obtained in [Step 2: Construct a canonicalized resource](#section_55g_u17_t1u).|

## Step 4: Calculate the signature string

Calculate the hash-based message authentication code \(HMAC\) value of the string-to-sign by using the HMAC-SHA1 algorithm and then encode the HMAC value into a signature string based on Base64 encoding rules. For more information about HMAC, see [RFC 2104](https://www.ietf.org/rfc/rfc2104.txt?spm=a2c4g.11186623.2.12.4ccd2be6wshrNa&file=rfc2104.txt).

```
Signature = Base64( HMAC-SHA1( SigningKey, StringToSign ) )
```

**Note:** In the preceding formula, set the value of the SigningKey parameter to your AccessKey secret. For more information, see [Create an AccessKey pair]().

## Step 5: Add the signature to the request

After you obtain the signature string, construct the Authorization header value based on the following formula:

```
Authorization = "acs " + AccessKeyId + ":" + Signature
```

**Related topics**  


[Common parameters](/intl.en-US/API Reference/Common parameters.md)

