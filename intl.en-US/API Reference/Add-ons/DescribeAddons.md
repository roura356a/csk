# DescribeAddons

Queries the details of the components that are installed in a specified Container Service for Kubernetes \(ACK\) cluster.

## Debugging

[OpenAPI Explorer automatically calculates the signature value. For your convenience, we recommend that you call this operation in OpenAPI Explorer. OpenAPI Explorer dynamically generates the sample code of the operation for different SDKs.](https://api.aliyun.com/#product=CS&api=DescribeAddons&type=ROA&version=2015-12-15)

## Request syntax

```
GET /clusters/components/metadata?region=String&cluster_type=String HTTP/1.1 
Content-Type:application/json
```

## Request parameters

|Parameter|Type|Required|Example|Description|
|---------|----|--------|-------|-----------|
|region|String|Yes|cn-beijing|The ID of the region where the ACK cluster is deployed. |
|cluster\_type|String|No|kubernetes|The cluster type. Valid values:

 -   `Kubernetes`: dedicated Kubernetes cluster
-   `ManagedKubernetes`: managed Kubernetes cluster
-   `Ask`: serverless Kubernetes \(ASK\) cluster
-   `ExternalKubernetes`: registered external Kubernetes cluster |

## Response syntax

```
HTTP/1.1 200
Content-Type:application/json
{
  "ComponentGroups" : [ {
    "group_name" : "String",
    "items" : [ {
      "name" : "String"
    } ]
  } ],
  "StandardComponents" : {
    "key" : {
      "name" : "String",
      "version" : "String",
      "description" : "String",
      "required" : "String",
      "disabled" : Boolean
    }
  }
}
```

## Response parameters

|Parameter|Type|Example|Description|
|---------|----|-------|-----------|
|ComponentGroups|Array of ComponentGroup| |The details of the returned components. |
|group\_name|String|storage|The name of the component group. |
|items|Array of item| |The names of the components in the component group. |
|name|String|flexvolume|The name of the component. |
|StandardComponents|Map| |Standard components. |
| |object| |The details of the standard components. |
|name|String|ack-arena|The name of the component. |
|version|String|0.5.0|The version of the component. |
|description|String|\*\*\*|The description of the component. |
|required|String|false|Indicates whether the component is required in an ACK cluster. Valid values:

 -   `true`: The component is required and must be installed when an ACK cluster is created.
-   `false`: The component is optional. After an ACK cluster is created, you can go to the `Add-ons`page in the ACK console to install the component. |
|disabled|Boolean|false|Indicates whether the automatic installation of the component is disabled. By default, some optional components, such as components for logging and Ingresses, are installed when an ACK cluster is created. You can set this parameter to disable the automatic installation of a component. Valid values:

 -   `true`: disables the automatic installation of the component.
-   `false`: allows the automatic installation of the component. |

## Examples

Sample requests

```
GET /clusters/components/metadata?region=cn-beijing	&cluster_type=kubernetes HTTP/1.1 
Content-Type:application/json
```

Sample success responses

`XML` format

```
HTTP/1.1 200 OK
Content-Type:application/xml

<StandardComponents>
    <cloud-controller-manager>
        <name>cloud-controller-manager</name>
        <version>v1.9.3.339-g9830b58-aliyun</version>
        <disabled>false</disabled>
        <required>true</required>
        <description>name: Cloud Controller Manager introduction: Using Cloud Controller Manager to create load balancers for k8s applications and manage node route entries doc:en: https://www.alibabacloud.com/help/doc-detail/94925.htm</description>
    </cloud-controller-manager>
    <csi-plugin>
        <name>csi-plugin</name>
        <version>v1.16.9.43-f36bb540-aliyun</version>
        <disabled>false</disabled>
        <required>false</required>
        <description>csi-plugin</description>
    </csi-plugin>
</StandardComponents>
<ComponentGroups>
    <group_name>storage</group_name>
    <items>
        <name>flexvolume</name>
    </items>
    <items>
        <name>alicloud-disk-controller</name>
    </items>
    <items>
        <name>csi-plugin</name>
    </items>
    <items>
        <name>csi-provisioner</name>
    </items>
</ComponentGroups>
<ComponentGroups>
    <group_name>network</group_name>
    <items>
        <name>kube-flannel-ds</name>
    </items>
    <items>
        <name>terway</name>
    </items>
    <items>
        <name>terway-eni</name>
    </items>
    <items>
        <name>terway-eniip</name>
    </items>
    <items>
        <name>nginx-ingress-controller</name>
    </items>
    <items>
        <name>coredns</name>
    </items>
    <items>
        <name>managed-kube-proxy</name>
    </items>
</ComponentGroups>
```

`JSON` format

```
HTTP/1.1 200 OK
Content-Type:application/json

{
  "StandardComponents" : {
    "cloud-controller-manager" : {
      "name" : "cloud-controller-manager",
      "version" : "v1.9.3.339-g9830b58-aliyun",
      "disabled" : false,
      "required" : "true",
      "description" : "name: Cloud Controller Manager introduction: Using Cloud Controller Manager to create load balancers for k8s applications and manage node route entries doc:en: https://www.alibabacloud.com/help/doc-detail/94925.htm"
    },
    "csi-plugin" : {
      "name" : "csi-plugin",
      "version" : "v1.16.9.43-f36bb540-aliyun",
      "disabled" : false,
      "required" : "false",
      "description" : "csi-plugin"
    }
  },
  "ComponentGroups" : [ {
    "group_name" : "storage",
    "items" : [ {
      "name" : "flexvolume"
    }, {
      "name" : "alicloud-disk-controller"
    }, {
      "name" : "csi-plugin"
    }, {
      "name" : "csi-provisioner"
    } ]
  }, {
    "group_name" : "network",
    "items" : [ {
      "name" : "kube-flannel-ds"
    }, {
      "name" : "terway"
    }, {
      "name" : "terway-eni"
    }, {
      "name" : "terway-eniip"
    }, {
      "name" : "nginx-ingress-controller"
    }, {
      "name" : "coredns"
    }, {
      "name" : "managed-kube-proxy"
    } ]
  } ]
}
```

## Error codes

For a list of error codes, visit the [API Error Center](https://error-center.alibabacloud.com/status/product/CS).

