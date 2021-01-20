---
keyword: [Istio, intelligent routing]
---

# Use Istio to achieve intelligent routing in ACK

Container Service for Kubernetes \(ACK\) allows you to deploy Istio with a few clicks and supports multiple features of Istio. This topic describes the traffic routing, fault injection, and traffic shifting features of Istio, and how to implement intelligent routing by using Istio. An application from the official Istio website is used as an example in this topic.

## Prerequisites

-   [Create a managed Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster management/Create Kubernetes clusters/Create a managed Kubernetes cluster.md).
-   [Deploy Istio](/intl.en-US/User Guide for Kubernetes Clusters/Service mesh/Istio management/Deploy Istio.md).

    **Note:** The Istio version used in this example is 1.0.2.

-   A local Linux environment is set up and you are connected to an ACK cluster by using kubectl. For more information, see [Use kubectl to connect to an ACK cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster management/Access clusters/Use kubectl to connect to an ACK cluster.md).
-   The code of the Istio project in the preceding version is downloaded and you have run related commands in the Istio file directory. For more information, see [Istio](https://github.com/istio/istio/releases) and [intelligent routing](https://istio.io/docs/examples/intelligent-routing/).

## Install the Bookinfo application

For more information about how to install the Bookinfo application, see [Bookinfo Application](https://istio.io/docs/examples/bookinfo/).

1.  Run the following command to add the `istio-injection=enabled` label to the `default` namespace:

    **Note:** ACK allows you to deploy Istio with a few clicks and supports automatic sidecar injection.

    ```
    kubectl label namespace default istio-injection=enabled
    ```

2.  Run the following kubectl command to deploy the Bookinfo application:

    ```
    kubectl apply -f samples/bookinfo/platform/kube/bookinfo.yaml
    ```

    This command launches four services, including the v1, v2, and v3 versions of the `reviews` service.

3.  Run the following command to verify that all services and pods are running:

    ```
    kubectl get svc,pods
    ```

4.  If you need to access the application from outside the ACK cluster, for example, from a browser, you must create an [Istio gateway](https://istio.io/docs/concepts/traffic-management/#gateways). Then, define the gateway as an access gateway of the application.

    ```
    kubectl apply -f samples/bookinfo/networking/bookinfo-gateway.yaml
    ```

    Run the following command to verify that the gateway is created:

    ```
    kubectl get gateway
    ```

    ```
    NAME               AGE
    bookinfo-gateway   32s
    ```

5.  Run the following command to query the endpoint of `istio-ingressgateway`:

    ```
    kubectl get svc istio-ingressgateway -n istio-system
    ```

    You can also query the endpoint of `istio-ingressgateway` in the [the ACK console](https://cs.console.aliyun.com).

    1.  Log on to the [ACK console](https://cs.console.aliyun.com).

    2.  In the left-side navigation pane, click **Clusters**.

    3.  On the Clusters page, find the ACK cluster that you want to manage and click the cluster name or **Applications** in the **Actions** column.

    4.  On the page that appears, click **Services** in the left-side navigation pane to view the IP address of `istio-ingressgateway`.

6.  Enter `http://{EXTERNAL-IP}/productpage` into the address bar of the browser to open the Bookinfo homepage.

    ![error](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/7655359951/p13548.png)

    After you refresh the page several times, you can see different versions of reviews in productpage. The reviews are presented in a round robin style \(red star, black star, and no star\) because Istio has not been used to control version routing.


## Request routing

Three versions of the reviews service are deployed for the Bookinfo application. Therefore, you must set a default route for version routing. Otherwise, different versions of reviews \(with or without star ratings\) are presented each time you access the application. This occurs because no default route is set and Istio randomly routes requests to all available versions of the service.

Before you use Istio to control Bookinfo version routing, you must define the available versions in destination rules.

Run the following commands to create the default destination rule for the Bookinfo service:

-   If you do not require mutual TLS authentication, run the following command:

    ```
    kubectl apply -f samples/bookinfo/networking/destination-rule-all.yaml
    ```

-   If you require mutual TLS authentication, run the following command:

    ```
    kubectl apply -f samples/bookinfo/networking/destination-rule-all-mtls.yaml
    ```

    Wait a few seconds for the destination rule to take effect. You can run the following command to view the destination rule:

    ```
    kubectl get destinationrules -o yaml
    ```


## Set the default version of all microservices to v1

Run the following command to set the default version of all microservices to v1:

```
kubectl apply -f samples/bookinfo/networking/virtual-service-all-v1.yaml
```

Run the following command to view all routing rules:

```
kubectl get virtualservices -o yaml
```

Routing rules are distributed to proxy servers in an asynchronous manner. You may need to wait for the routing rules to be synchronized to all pods. Wait a few seconds and then access the application.

Enter the URL `http://{EXTERNAL-IP}/productpage` into the address bar of the browser to access the Bookinfo application.

On the productpage page of the Bookinfo application, the displayed content contains only reviews without star ratings. This occurs because the reviews:v1 service does not have access to the ratings service.

![error](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/8655359951/p12577.png)

## Route requests from a specified user to reviews:v2

Run the following command to route requests from user jason to reviews:v2. This enables the ratings service.

```
kubectl apply -f samples/bookinfo/networking/virtual-service-reviews-test-v2.yaml
```

Run the following command to verify that the routing rule is created:

```
kubectl get virtualservice reviews -o yaml
```

```
apiVersion: networking.istio.io/v1alpha3
kind: VirtualService
metadata:
  name: reviews
  ...
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
        subset: v2
  - route:
    - destination:
        host: reviews
        subset: v1
```

After you confirm that the routing rule is created, enter the URL `http://{EXTERNAL-IP}/productpage` into the address bar of the browser to access the Bookinfo application.

Use the jason account to log on to the productpage page. Then, you can view the rating information of each review.

**Note:** Both the account name and password are `jason`.

![error](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/8655359951/p12578.png)

**Note:** In this example, use Istio to route all requests to the v1 version of the reviews service for the Bookinfo application. Then, set a routing rule to route requests from a specified user to the v2 version of the reviews service based on request headers, for example, the user cookie.

## Inject faults

To test the resilience of the Bookinfo application, you can log on by using the jason account and inject a 7-second delay between the `reviews:v2` and `ratings` services. A 10-second connection timeout is set for the reviews:v2 service to call the ratings service. Therefore, it is expected that the end-to-end connection has no errors.

## HTTP Delay

Use HTTP Delay to create a fault injection rule to set a delay for traffic transmission from user jason.

```
kubectl apply -f samples/bookinfo/networking/virtual-service-ratings-test-delay.yaml
```

After you verify that the rule is created, enter the URL `http://{EXTERNAL-IP}/productpage` into the address bar of the browser to access the Bookinfo application.

Log on by using the jason account. The following content is displayed.

![error](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/8655359951/p12579.png)

**Note:** The reviews service fails because the timeout between the productpage and reviews services is shorter than that between the reviews and rating services \(10 seconds\). The timeout between the productpage and reviews services is 3 seconds per attempt and 6 seconds in total \(one retry\). This bug exists in enterprise applications where microservices are independently developed by different teams. The fault injection feature of Istio helps identify these bugs without affecting users.

## HTTP Abort

Use HTTP Abort to create a fault injection rule.

```
kubectl apply -f samples/bookinfo/networking/virtual-service-ratings-test-abort.yaml
```

After you verify that the rule is created, enter the URL `http://{EXTERNAL-IP}/productpage` into the address bar of the browser to access the Bookinfo application.

Log on by using the jason account. The following content is displayed.

![error](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/8655359951/p12580.png)

## Shift traffic

In addition to content-based routing, Istio also supports weighted routing rules.

1.  Run the following command to set the default version of all microservices to v1:

    ```
    kubectl replace -f samples/bookinfo/networking/virtual-service-all-v1.yaml
    ```

2.  Run the following command to shift 50% of traffic from reviews:v1 to reviews:v3:

    ```
    kubectl replace -f samples/bookinfo/networking/virtual-service-reviews-50-v3.yaml
    ```


Refresh the productpage page several times in the browser. You have a 50% opportunity to see reviews with red stars.

**Note:** Using this method to migrate versions is different from using the deployment feature of the container orchestration platform. The container orchestration platform uses instance scaling to manage traffic. If you use Istio, you can scale in or out one of the reviews service versions without affecting the other version. Traffic distribution between the two service versions are not affected.

After you verify that the `reviews:v3` service is stable, you can route 100% of the virtual service traffic to `reviews:v3`. Run the following command to perform a canary release:

```
kubectl replace -f samples/bookinfo/networking/virtual-service-reviews-v3.yaml
```

## Summary

ACK allows you to deploy Istio, an open platform where you can connect, manage, and secure microservices. You can also integrate and configure multiple related services for applications. In the preceding example, the traffic routing, fault injection, and traffic shifting features of Istio are described by using an application from the official Istio website. You are welcomed to use ACK to deploy Istio as an open platform for managing microservices and integrate Istio into the project of the microservices.

