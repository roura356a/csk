# Use a VirtualService and DestinationRule to complete blue/green and canary deployments {#concept_263233 .concept}

This topic describes how to use the Istio resources `VirtualService` and `Destination` to complete blue/green and canary deployments.

## Before you begin {#section_exb_17k_r9n .section}

1.  Deploy a `VirtualService` and `DestinationRule`.
    1.  Use [Cloud Shell](../../../../reseller.en-US/User Guide/Kubernetes cluster/Cluster management/Use kubectl commands in Cloud Shell to manage a Kubernetes cluster.md#) to connect to the target Kubernetes cluster.
    2.  Create the file virtual-service.yaml, copy the following code into the file, and then run the kubectl apply -f virtual-service.yaml command to deploy a `VirtualService`.

        ``` {#codeblock_967_inq_jjo}
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

    3.  Create the file destination.yaml, copy the following code into the file, and then run the kubectl apply -f destination.yaml command to deploy a DestinationRule.

        ``` {#codeblock_qd1_ghw_ey7}
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

2.  Run the following command to set an Istio ingress gateway to forward traffic:

    ``` {#codeblock_o7w_seb_qjj}
    kubectl port-forward -n istio-system svc/istio-ingressgateway 3000:80
    ```

3.  In your browser, enter localhost:3000/productpage to access the Product page.

    The **Book Review** area displays only comments without any ratings. This means that the Product page uses the version 1 of the review service.


## Use the blue/green deployment to release the version 2 of the review service {#section_v2o_yqz_plq .section}

After you complete this deployment, refresh the page. Then, the **Book Review** area displays ratings with black stars.

``` {#codeblock_1ty_tzd_stz}
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

## Use the canary deployment to release the version 3 of the review service {#section_k84_sxy_480 .section}

The version 2 and version 3 of the review service can be provided at the same time to response to 50% traffic respectively.

Each time when you refresh the page, the **Book Review** area randomly displays V2 and V3 of the review service. Ratings of the V3 contains red stars.

``` {#codeblock_xgn_5v6_zl1}
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

## Use the canary deployment to release the version 3 of the review service by HTTP headers {#section_8gf_xxh_xjh .section}

This method allows you to control the users to access to different versions of one page.

After you refresh the page for multiple times, the stars in the review area retain in black.

``` {#codeblock_ud9_zzk_epb}
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

In the upper-right corner, click **Sign in**, use **jason** to log on to the application \(no password is required\). The page displays that the stars in the review area are in red.

**Note:** When you use the **jason** account to log on to the application to access the backend service, your requests are attached with HTTP headers of `end-user=XXX`. Then, your requests meet the rules defined by the preceding YAML file. Therefore, your requests are directed to the review service of V3.

