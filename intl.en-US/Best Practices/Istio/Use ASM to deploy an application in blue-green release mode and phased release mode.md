# Use ASM to deploy an application in blue-green release mode and phased release mode

This topic describes how to use virtual services and destination rules of Alibaba Cloud Service Mesh \(ASM\) to deploy an application in blue-green release mode and phased release mode.

-   At least one ASM instance is created. For more information, see [Create an ASM instance]().
-   At least one Alibaba Cloud Container Service for Kubernetes \(ACK\) cluster is added to the ASM instance. For more information, see [Add a cluster to an ASM instance]().
-   The Bookinfo application is deployed in the ACK cluster that is added to the ASM instance. For more information, see [Deploy an application in an ASM instance]().
-   An ingress gateway is deployed for the ACK cluster that is added to the ASM instance. For more information, see [Deploy an ingress gateway service]().

## Create a destination rule

Create a destination rule for the Bookinfo application that is deployed in your ASM instance. For more information, see [Manage destination rules](). The following code shows the configuration of a sample destination rule:

```
apiVersion: networking.istio.io/v1alpha3
kind: DestinationRule
metadata:
  name: productpage
spec:
  host: productpage
  subsets:
  - name: v1
    labels:
      version: v1
---
apiVersion: networking.istio.io/v1alpha3
kind: DestinationRule
metadata:
  name: reviews
spec:
  host: reviews
  subsets:
  - name: v1
    labels:
      version: v1
  - name: v2
    labels:
      version: v2
  - name: v3
    labels:
      version: v3
---
apiVersion: networking.istio.io/v1alpha3
kind: DestinationRule
metadata:
  name: ratings
spec:
  host: ratings
  subsets:
  - name: v1
    labels:
      version: v1
  - name: v2
    labels:
      version: v2
  - name: v2-mysql
    labels:
      version: v2-mysql
  - name: v2-mysql-vm
    labels:
      version: v2-mysql-vm
---
apiVersion: networking.istio.io/v1alpha3
kind: DestinationRule
metadata:
  name: details
spec:
  host: details
  subsets:
  - name: v1
    labels:
      version: v1
  - name: v2
    labels:
      version: v2
```

## Create a virtual service

Create a virtual service for the Bookinfo application that is deployed in your ASM instance. For more information, see [Manage virtual services](). The following code shows the configuration of a sample virtual service:

```
apiVersion: networking.istio.io/v1alpha3
kind: VirtualService
metadata:
  name: productpage
spec:
  hosts:
  - productpage
  http:
  - route:
    - destination:
        host: productpage
        subset: v1
---
apiVersion: networking.istio.io/v1alpha3
kind: VirtualService
metadata:
  name: reviews
spec:
  hosts:
  - reviews
  http:
  - route:
    - destination:
        host: reviews
        subset: v1
---
apiVersion: networking.istio.io/v1alpha3
kind: VirtualService
metadata:
  name: ratings
spec:
  hosts:
  - ratings
  http:
  - route:
    - destination:
        host: ratings
        subset: v1
---
apiVersion: networking.istio.io/v1alpha3
kind: VirtualService
metadata:
  name: details
spec:
  hosts:
  - details
  http:
  - route:
    - destination:
        host: details
        subset: v1
```

## Deploy version 2 in blue-green release mode

After the preceding destination rule and virtual service are created, version 2 of the reviews microservice of the Bookinfo application is running. However, no traffic is routed to version 2. To route traffic to version 2, you must deploy version 2 in blue-green release mode.

Create a virtual service to deploy version 2 of Bookinfo in blue-green release mode. For more information, see [Manage virtual services](). The following code shows the configuration of a sample virtual service:

```
apiVersion: networking.istio.io/v1alpha3
kind: VirtualService
metadata:
  name: reviews
spec:
  hosts:
  - reviews
  http:
  - route:
    - destination:
        host: reviews
        subset: v2
```

After the preceding virtual service is created, refresh the page of Bookinfo. The reviews microservice displays ratings as black stars.

## Deploy version 3 in phased release mode to handle traffic by weight

You can run both version 2 and version 3 online and route traffic to the two versions by weight, such as 50% to 50%.

Create a virtual service to deploy version 3 of Bookinfo in phased release mode. For more information, see [Manage virtual services](). The following code shows the configuration of a sample virtual service:

```
apiVersion: networking.istio.io/v1alpha3
kind: VirtualService
metadata:
  name: reviews
spec:
  hosts:
    - reviews
  http:
  - route:
    - destination:
        host: reviews
        subset: v2
      weight: 50
    - destination:
        host: reviews
        subset: v3
      weight: 50
```

After the preceding virtual service is created, refresh the page of Bookinfo. The reviews microservice displays ratings by using version 2 or version 3 at random. The reviews microservice of version 3 displays ratings as red stars.

## Deploy version 3 in phased release mode based on the request content

Phased release based on the traffic weight cannot meet the requirements of all scenarios. You can also deploy an application in phased release mode based on the user identity. This way, the application displays different pages for different users.

Create a virtual service to deploy the Bookinfo application in phased release mode. For more information, see [Manage virtual services](). The following code shows the configuration of a sample virtual service:

```
apiVersion: networking.istio.io/v1alpha3
kind: VirtualService
metadata:
  name: reviews
spec:
  hosts:
  - reviews
  http:
  - match:
    - headers:
        end-user:
          exact: jason
    route:
    - destination:
        host: reviews
        subset: v3
  - route:
    - destination:
        host: reviews
        subset: v2
```

After the preceding virtual service is created, refresh the page of Bookinfo. Bookinfo always displays ratings as black stars. You can click **Sign in** in the upper-right corner to log on to Bookinfo with the username jason. The logon does not require a password. After you log on, you can find that Bookinfo displays ratings as red stars.

**Note:** When you log on to Bookinfo and access its backend microservices, your requests contain the HTTP header `end-user=XXX`, which indicates the user identity. If you log on to Bookinfo with the username jason, the rule in the YAML file is matched and your requests are directed to the reviews microservice of version 3.

