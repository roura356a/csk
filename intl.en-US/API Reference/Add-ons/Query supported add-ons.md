# Query supported add-ons

You can call DescribeAddons to query details about the add-ons that are supported by a specified cluster type.

## Debugging

[OpenAPI Explorer automatically calculates the signature value. For your convenience, we recommend that you call this operation in OpenAPI Explorer. OpenAPI Explorer dynamically generates the sample code of the operation for different SDKs.](https://api.aliyun.com/#product=CS&api=DescribeAddons&type=ROA&version=2015-12-15)

## Request parameter types

This operation uses common request parameters only. For more information, see Common parameters.

## Request syntax

```
GET /clusters/components/metadata HTTPS|HTTP
```

## Request parameters

|Parameter|Type|Required|Example|Description|
|---------|----|--------|-------|-----------|
|region|String|No|cn-beijing|The ID of the region. |
|cluster\_type|String|No|kubernetes|The type of the cluster. Default value: kubernetes. |

## Response parameters

|Parameter|Type|Example|Description|
|---------|----|-------|-----------|
|ComponentGroups|Array| |A list of add-on groups. |
|default|List|\[ "flexvolume", "alicloud-disk-controller" \]|The default add-ons in the group. |
|group\_name|String|storage|The name of the add-on group. |
|items|Array| |The details about the add-ons in the group. |
|name|String|csi-plugin|The name of the add-on. |
|required|String|""|Indicates whether the add-on is required. |
|version|String|""|The version of the add-on. |
|StandardComponents|Struct| |A list of standard add-ons. |
|addon\_name|Struct| |The name of the standard add-on. |
|name|String|sandboxed-container-controller|The name of the add-on. |
|required|String|"false"|Indicates whether the add-on is required. |
|version|String|v1.14.8.44-c23b62c5-aliyun|The version of the add-on. |

## Examples

Sample requests

```
GET /clusters/components/metadata HTTP/1.1
Common request parameters
```

Sample success responses

`XML` format

```
<ComponentGroups>
    <group_name>storage</group_name>
    <default>flexvolume</default>
    <default>alicloud-disk-controller</default>
    <items>
        <name>flexvolume</name>
        <disabled>false</disabled>
        <version>1.1</version>
        <description>flexvolume</description>
    </items>
</ComponentGroups>
<StandardComponents>
    <sandboxed-container-controller>
        <description>alicloud-disk-controller</description>
        <name>alicloud-disk-controller</name>
        <required>false</required>
        <disabled>false</disabled>
        <version>v1.14.8.44-c23b62c5-aliyun</version>
    </sandboxed-container-controller>
</StandardComponents>
```

`JSON` format

```
{
    "ComponentGroups": [
        {
            "group_name": "storage",
            "default": ["flexvolume", "alicloud-disk-controller"],
            "items": [{"name": "flexvolume", "disabled": false, "version": "1.1", "description":"flexvolume"}]
        }
    ],
    "StandardComponents": {
        "sandboxed-container-controller": {
            "description": "alicloud-disk-controller",
            "name": "alicloud-disk-controller",
            "required": "false",
            "disabled":false,
            "version":"v1.14.8.44-c23b62c5-aliyun"
        }
    }
}
```

## Error codes

For a list of error codes, visit the [API Error Center](https://error-center.alibabacloud.com/status/product/CS).

