# Use annotations to configure SLB instances

You can use annotations in the YAML file of a service to configure a Server Load Balancer \(SLB\) instance. This topic describes how to use annotations to configure the SLB resources of the following types: SLB instances, listeners, and VServer groups.

## Usage notes

-   The values of annotations are case-sensitive.
-   In `annotations`, the keyword `alicloud` is changed to `alibaba-cloud` since September 11, 2019.

    Examples:

    Old annotation: `service.beta.kubernetes.io/alicloud-loadbalancer-id`

    New annotation: `service.beta.kubernetes.io/alibaba-cloud-loadbalancer-id`

    The system still supports annotations that use the keyword `alicloud`. You do not have to change the existing annotations.


## SLB

**Common annotations for configuring SLB instances**

-   Create a public-facing SLB instance

    ```
    apiVersion: v1
    kind: Service
    metadata:
      name: nginx
      namespace: default
    spec:
      ports:
      - port: 80
        protocol: TCP
        targetPort: 80
      selector:
        run: nginx
      type: LoadBalancer
    ```

-   Create an internal SLB instance

    ```
    apiVersion: v1
    kind: Service
    metadata:
      annotations:
        service.beta.kubernetes.io/alibaba-cloud-loadbalancer-address-type: "intranet"
      name: nginx
      namespace: default
    spec:
      ports:
      - port: 80
        protocol: TCP
        targetPort: 80
      selector:
        run: nginx
      type: LoadBalancer
    ```

-   Create an HTTP-based SLB instance

    ```
    apiVersion: v1
    kind: Service
    metadata:
      annotations:
        service.beta.kubernetes.io/alibaba-cloud-loadbalancer-protocol-port: "http:80"
      name: nginx
      namespace: default
    spec:
      ports:
      - port: 80
        protocol: TCP
        targetPort: 80
      selector:
        run: nginx
      type: LoadBalancer
    ```

-   Create an HTTPS-based SLB instance

    Create a certificate in the SLB console and note down the certificate ID. Then, use the annotations in the following template to create an HTTPS-based SLB instance:

    **Note:** HTTPS listeners of the SLB instance decrypt HTTPS requests into HTTP requests and forward them to pods on the backend servers.

    ```
    apiVersion: v1
    kind: Service
    metadata:
      annotations:
        service.beta.kubernetes.io/alibaba-cloud-loadbalancer-protocol-port: "https:443"
        service.beta.kubernetes.io/alibaba-cloud-loadbalancer-cert-id: "${YOUR_CERT_ID}"
      name: nginx
      namespace: default
    spec:
      ports:
      - port: 443
        protocol: TCP
        targetPort: 80
      selector:
        run: nginx
      type: LoadBalancer
    ```

-   Create an SLB instance of a specified specification

    For more information about SLB instance specifications, see [CreateLoadBalancer](/intl.en-US/User Guide/Developer Guide/Server Load Balancer (SLB) instances/CreateLoadBalancer.md). You can use the annotation in the following template to create an SLB instance of a specified specification or update the specification of an existing SLB instance.

    **Note:** We recommend that you do not modify the specification of an SLB instance in the SLB console. Otherwise, Cloud Controller Manager \(CCM\) may restore the SLB instance to the original specification.

    ```
    apiVersion: v1
    kind: Service
    metadata:
      annotations:
        service.beta.kubernetes.io/alibaba-cloud-loadbalancer-spec: "slb.s1.small"
      name: nginx
      namespace: default
    spec:
      ports:
      - port: 443
        protocol: TCP
        targetPort: 443
      selector:
        run: nginx
      type: LoadBalancer
    ```

-   Use an existing SLB instance

    -   By default, CCM does not overwrite listeners of an existing SLB instance. To forcibly overwrite listeners of an existing SLB instance, set the annotation `service.beta.kubernetes.io/alibaba-cloud-loadbalancer-force-override-listeners` to true.

        **Note:** The following list explains why CCM does not overwrite listeners of an existing SLB instance:

        -   If you forcibly overwrite the listeners that are handling traffic to a service, service interruption may occur.
        -   CCM supports limited VServer group configurations and cannot process complex configurations. If you require complex VServer group configurations, you can manually create listeners in the SLB console without overwriting the existing ones.
        Therefore, we recommend that you do not forcibly overwrite the listeners of existing SLB instances. However, you can forcibly overwrite an existing listener if the port of the listener is no longer in use.

    -   If you specify an existing SLB instance for a service, the annotation `service.beta.kubernetes.io/alibaba-cloud-loadbalancer-additional-resource-tags` is invalid. As a result, you cannot attach additional tags to the instance.
    ```
    apiVersion: v1
    kind: Service
    metadata:
      annotations:
        service.beta.kubernetes.io/alibaba-cloud-loadbalancer-id: "${YOUR_LOADBALACER_ID}"
      name: nginx
      namespace: default
    spec:
      ports:
      - port: 443
        protocol: TCP
        targetPort: 443
      selector:
        run: nginx
      type: LoadBalancer
    ```

-   Use an existing SLB instance and forcibly overwrite listeners of the SLB instance

    Use the annotations in the following template to overwrite an existing listener of the SLB instance. If you specify a different port number, the original port number is overwritten.

    ```
    apiVersion: v1
    kind: Service
    metadata:
      annotations:
        service.beta.kubernetes.io/alibaba-cloud-loadbalancer-id: "${YOUR_LOADBALACER_ID}"
        service.beta.kubernetes.io/alibaba-cloud-loadbalancer-force-override-listeners: "true"
      name: nginx
      namespace: default
    spec:
      ports:
      - port: 443
        protocol: TCP
        targetPort: 443
      selector:
        run: nginx
      type: LoadBalancer
    ```

-   Specify the primary and secondary zones for an SLB instance

    -   Primary and secondary zones are not supported by SLB instances deployed in specific regions, such as the Indonesia \(Jakarta\) region.
    -   You cannot modify the primary and secondary zones after the SLB instance is created.
    ```
    apiVersion: v1
    kind: Service
    metadata:
      annotations:
        service.beta.kubernetes.io/alibaba-cloud-loadbalancer-master-zoneid: "ap-southeast-5a"
        service.beta.kubernetes.io/alibaba-cloud-loadbalancer-slave-zoneid: "ap-southeast-5a"
      name: nginx
      namespace: default
    spec:
      ports:
      - port: 80
        protocol: TCP
        targetPort: 80
      selector:
        run: nginx
      type: LoadBalancer
    ```

-   Create an internal SLB instance

    -   The two annotations in the following template are required.
    -   Internal SLB instances support virtual private clouds \(VPCs\) and the classic network. For more information, see [SLB instance overview](/intl.en-US/User Guide/Instance/SLB instance overview.md).
    ```
    apiVersion: v1
    kind: Service
    metadata:
      annotations:
        service.beta.kubernetes.io/alibaba-cloud-loadbalancer-address-type: "intranet"
        service.beta.kubernetes.io/alibaba-cloud-loadbalancer-network-type: "vpc"
      name: nginx
      namespace: default
    spec:
      ports:
      - port: 443
        protocol: TCP
        targetPort: 443
      selector:
        run: nginx
      type: LoadBalancer
    ```

-   Create an SLB instance that incurs fees by bandwidth

    -   Only public-facing SLB instances incur fees by bandwidth.

        For more information about limits on billing methods for public-facing SLB instances, see [ModifyLoadBalancerInstanceSpec](/intl.en-US/User Guide/Developer Guide/Server Load Balancer (SLB) instances/ModifyLoadBalancerInstanceSpec.md).

    -   The two annotations in the following template are required.

        Modify the annotation values based on your needs.

    ```
    apiVersion: v1
    kind: Service
    metadata:
      annotations:
        service.beta.kubernetes.io/alibaba-cloud-loadbalancer-charge-type: "paybybandwidth"
        service.beta.kubernetes.io/alibaba-cloud-loadbalancer-bandwidth: "2"
      name: nginx
      namespace: default
    spec:
      ports:
      - port: 443
        protocol: TCP
        targetPort: 443
      selector:
        run: nginx
      type: LoadBalancer
    ```

    **Note:** The annotation service.beta.kubernetes.io/alibaba-cloud-loadbalancer-bandwidth specifies the upper limit of the bandwidth.

-   Enable the health check feature for an SLB instance
    -   Enable the health check feature for a TCP-based SLB instance

        -   By default, the health check feature is enabled for TCP listeners and cannot be disabled. The annotation `service.beta.kubernetes.io/alibaba-cloud-loadbalancer-health-check-flag` is invalid.
        -   All annotations in the following template are required.
        ```
        apiVersion: v1
        kind: Service
        metadata:
          annotations:
            service.beta.kubernetes.io/alibaba-cloud-loadbalancer-health-check-type: "tcp"
            service.beta.kubernetes.io/alibaba-cloud-loadbalancer-health-check-connect-timeout: "8"
            service.beta.kubernetes.io/alibaba-cloud-loadbalancer-healthy-threshold: "4"
            service.beta.kubernetes.io/alibaba-cloud-loadbalancer-unhealthy-threshold: "4"
            service.beta.kubernetes.io/alibaba-cloud-loadbalancer-health-check-interval: "3"
          name: nginx
          namespace: default
        spec:
          ports:
          - port: 80
            protocol: TCP
            targetPort: 80
          selector:
            run: nginx
          type: LoadBalancer
        ```

    -   Enable the health check feature for an HTTP-based SLB instance

        All annotations in the following template are required.

        ```
        apiVersion: v1
        kind: Service
        metadata:
          annotations:
            service.beta.kubernetes.io/alibaba-cloud-loadbalancer-health-check-flag: "on"
            service.beta.kubernetes.io/alibaba-cloud-loadbalancer-health-check-type: "http"
            service.beta.kubernetes.io/alibaba-cloud-loadbalancer-health-check-uri: "/test/index.html"
            service.beta.kubernetes.io/alibaba-cloud-loadbalancer-healthy-threshold: "4"
            service.beta.kubernetes.io/alibaba-cloud-loadbalancer-unhealthy-threshold: "4"
            service.beta.kubernetes.io/alibaba-cloud-loadbalancer-health-check-timeout: "10"
            service.beta.kubernetes.io/alibaba-cloud-loadbalancer-health-check-interval: "3"
            service.beta.kubernetes.io/alibaba-cloud-loadbalancer-protocol-port: "http:80"
          name: nginx
          namespace: default
        spec:
          ports:
          - port: 80
            protocol: TCP
            targetPort: 80
          selector:
            run: nginx
          type: LoadBalancer
        ```

-   Specify a scheduling algorithm for an SLB instance

    -   rr: the default scheduling algorithm. Requests are sequentially distributed to backend servers.
    -   wrr: Backend servers that have higher weights receive more requests than those that have lower weights.
    -   wlc: Requests are distributed based on the combination of the weights and connections of backend servers. If two backend servers have the same weight, the backend server that has fewer connections receives more requests.
    ```
    apiVersion: v1
    kind: Service
    metadata:
      annotations:
        service.beta.kubernetes.io/alibaba-cloud-loadbalancer-scheduler: "wlc"
      name: nginx
      namespace: default
    spec:
      ports:
      - port: 443
        protocol: TCP
        targetPort: 443
      selector:
        run: nginx
      type: LoadBalancer
    ```

-   Specify a VSwitch for an SLB instance

    -   Obtain the ID of a specific VSwitch in the VPC console. Then, use the annotations in the following template to specify the VSwitch for an SLB instance.
    -   The specified VSwitch and the cluster must be deployed in the same VPC.
    -   All annotations in the following template are required.
    ```
    apiVersion: v1
    kind: Service
    metadata:
      annotations:
       service.beta.kubernetes.io/alibaba-cloud-loadbalancer-address-type: "intranet"
       service.beta.kubernetes.io/alibaba-cloud-loadbalancer-vswitch-id: "${YOUR_VSWITCH_ID}"
      name: nginx
      namespace: default
    spec:
      ports:
      - port: 443
        protocol: TCP
        targetPort: 443
      selector:
        run: nginx
      type: LoadBalancer
    ```

-   Attach additional tags to an SLB instance

    Separate multiple tags with commas \(,\). For example, `"k1=v1,k2=v2"`.

    ```
    apiVersion: v1
    kind: Service
    metadata:
      annotations:
        service.beta.kubernetes.io/alibaba-cloud-loadbalancer-additional-resource-tags: "Key1=Value1,Key2=Value2" 
      name: nginx
      namespace: default
    spec:
      ports:
      - port: 80
        protocol: TCP
        targetPort: 80
      selector:
        run: nginx
      type: LoadBalancer
    ```

-   Create an IPv6-based SLB instance

    -   The kube-proxy mode of the cluster must be IPVS.
    -   The assigned IPv6 address can be used only in an IPv6 network.
    -   You cannot change the IP address type after you create the SLB instance.
    ```
    apiVersion: v1
    kind: Service
    metadata:
      annotations:
        service.beta.kubernetes.io/alibaba-cloud-loadbalancer-ip-version: "ipv6"
      name: nginx
    spec:
      ports:
      - port: 80
        protocol: TCP
        targetPort: 80
      selector:
        app: nginx
      type: LoadBalancer
    ```

-   Enable deletion protection for an SLB instance

    By default, deletion protection is enabled for an SLB instance.

    **Note:** Assume that an SLB instance is created for a LoadBalancer service and deletion protection is enabled for the instance in the SLB console. To delete the SLB instance, run the `kubectl delete svc {your-svc-name}` command.

    ```
    apiVersion: v1
    kind: Service
    metadata:
      annotations:
        service.beta.kubernetes.io/alibaba-cloud-loadbalancer-delete-protection: "on"
      name: nginx
    spec:
      externalTrafficPolicy: Local
      ports:
      - port: 80
        protocol: TCP
        targetPort: 80
      selector:
        app: nginx
      type: LoadBalancer
    ```

-   Enable the configuration read-only mode for an SLB instance

    By default, the configuration read-only mode is enabled for an SLB instance.

    ```
    apiVersion: v1
    kind: Service
    metadata:
      annotations:
        service.beta.kubernetes.io/alibaba-cloud-loadbalancer-modification-protection: "ConsoleProtection"
      name: nginx
    spec:
      externalTrafficPolicy: Local
      ports:
      - port: 80
        protocol: TCP
        targetPort: 80
      selector:
        app: nginx
      type: LoadBalancer
    ```

-   Set the name of an SLB instance

    ```
    apiVersion: v1
    kind: Service
    metadata:
      annotations:
        service.beta.kubernetes.io/alibaba-cloud-loadbalancer-name: "your-svc-name"
      name: nginx
    spec:
      externalTrafficPolicy: Local
      ports:
      - port: 80
        protocol: TCP
        targetPort: 80
      selector:
        app: nginx
      type: LoadBalancer
    ```

-   Specify a resource group for an SLB instance

    Log on to the [Resource Management console](https://resourcemanager.console.aliyun.com/) to obtain the ID of a specific resource group. Then, use the annotation in the following template to specify the resource group for an SLB instance.

    **Note:** You cannot modify the resource group after the SLB instance is created.

    ```
    apiVersion: v1
    kind: Service
    metadata:
      annotations:
        service.beta.kubernetes.io/alibaba-cloud-loadbalancer-resource-group-id: "rg-xxxx"
      name: nginx
    spec:
      externalTrafficPolicy: Local
      ports:
      - port: 80
        protocol: TCP
        targetPort: 80
      selector:
        app: nginx
      type: LoadBalancer
    ```


|Annotation|Type|Description|Default value|Supported CCM version|
|----------|----|-----------|-------------|---------------------|
|service.beta.kubernetes.io/alibaba-cloud-loadbalancer-address-type|string|The type of an SLB instance. Valid values: internet and intranet.|internet|CCM 1.9.3 and later|
|service.beta.kubernetes.io/alibaba-cloud-loadbalancer-slb-network-type|string|The network type of an SLB instance. Valid values: classic and vpc.Set the annotation `service.beta.kubernetes.io/alibaba-cloud-loadbalancer-address-type` to intranet if you want to create an SLB instance in a VPC.

|classic|CCM 1.9.3 and later|
|service.beta.kubernetes.io/alibaba-cloud-loadbalancer-charge-type|string|The billing method of an SLB instance. Valid values: paybytraffic and paybybandwidth.|paybytraffic|CCM 1.9.3 and later|
|service.beta.kubernetes.io/alibaba-cloud-loadbalancer-id|string|The ID of an SLB instance. You can specify an existing SLB instance by using the annotation service.beta.kubernetes.io/alibaba-cloud-loadbalancer-id. By default, if you specify an existing SLB instance, CCM does not overwrite the listeners of the SLB instance. To forcibly overwrite the listeners, set the annotation service.beta.kubernetes.io/alibaba-cloud-loadbalancer-force-override-listeners to true.|N/A|V1.9.3.81-gca19cd4-aliyun and later|
|service.beta.kubernetes.io/alibaba-cloud-loadbalancer-spec|string|The specification of an SLB instance. For more information, see [CreateLoadBalancer](/intl.en-US/User Guide/Developer Guide/Server Load Balancer (SLB) instances/CreateLoadBalancer.md).|N/A|CCM 1.9.3 and later|
|service.beta.kubernetes.io/alibaba-cloud-loadbalancer-master-zoneid|string|The ID of the zone where the primary backend servers are deployed.|N/A|V1.9.3.10-gfb99107-aliyun and later|
|service.beta.kubernetes.io/alibaba-cloud-loadbalancer-slave-zoneid|string|The ID of the zone where the secondary backend servers are deployed.|N/A|V1.9.3.10-gfb99107-aliyun and later|
|service.beta.kubernetes.io/alibaba-cloud-loadbalancer-force-override-listeners|string|Specifies whether to overwrite the listeners of an existing SLB instance that you specify for a service.|false: The listeners of the existing SLB instance are not overwritten.|V1.9.3.81-gca19cd4-aliyun and later|
|service.beta.kubernetes.io/alibaba-cloud-loadbalancer-bandwidth|string|The upper limit of the bandwidth of an SLB instance. This annotation applies only to public-facing SLB instances.|50|V1.9.3.10-gfb99107-aliyun and later|
|service.beta.kubernetes.io/alibaba-cloud-loadbalancer-scheduler|string|The scheduling algorithm. Valid values: wrr, wlc, and rr. -   wrr: Backend servers that have higher weights receive more requests than those that have lower weights.
-   wlc: Requests are distributed based on the combination of the weights and connections of backend servers. If two backend servers have the same weight, the backend server that has fewer connections receives more requests.
-   rr: the default scheduling algorithm. Requests are sequentially distributed to backend servers.

|rr|CCM 1.9.3 and later|
|service.beta.kubernetes.io/alibaba-cloud-loadbalancer-vswitch-id|string|The ID of the VSwitch to which an SLB instance belongs. To specify this annotation, set the annotation service.beta.kubernetes.io/alibaba-cloud-loadbalancer-address-type to intranet.|N/A|CCM 1.9.3 and later|
|service.beta.kubernetes.io/alibaba-cloud-loadbalancer-additional-resource-tags|string|The tags that you attach to an SLB instance. Separate multiple tags with commas \(,\). For example, `"k1=v1,k2=v2"`.|N/A|CCM 1.9.3 and later|
|service.beta.kubernetes.io/alibaba-cloud-loadbalancer-ip-version|string|The IP address type of an SLB instance. Valid values: ipv4 and ipv6.|ipv4|V1.9.3.220-g24b1885-aliyun and later|
|service.beta.kubernetes.io/alibaba-cloud-loadbalancer-delete-protection|string|Specifies whether to enable deletion protection for an SLB instance. Valid values: on and off.|on|V1.9.3.313-g748f81e-aliyun and later|
|service.beta.kubernetes.io/alibaba-cloud-loadbalancer-modification-protection|string|Specifies whether to enable the configuration read-only mode for an SLB instance. Valid values: ConsoleProtection and NonProtection.|ConsoleProtection|V1.9.3.313-g748f81e-aliyun and later|
|service.beta.kubernetes.io/alibaba-cloud-loadbalancer-resource-group-id|string|The resource group to which an SLB instance belongs.|N/A|V1.9.3.313-g748f81e-aliyun and later|
|service.beta.kubernetes.io/alibaba-cloud-loadbalancer-name|string|The name of an SLB instance.|N/A|V1.9.3.313-g748f81e-aliyun and later|

## Listeners

**Common annotations for configuring listeners**

-   Set the session persistence period for a TCP-based SLB instance

    -   The annotation `service.beta.kubernetes.io/alibaba-cloud-loadbalancer-persistence-timeout` applies only to TCP listeners.
    -   If an SLB instance has multiple TCP listeners, the setting takes effect on all the TCP listeners.
    ```
    apiVersion: v1
    kind: Service
    metadata:
      annotations:
        service.beta.kubernetes.io/alibaba-cloud-loadbalancer-persistence-timeout: "1800"
      name: nginx
      namespace: default
    spec:
      ports:
      - port: 443
        protocol: TCP
        targetPort: 443
      selector:
        run: nginx
      type: LoadBalancer
    ```

-   Enable session persistence for an HTTP-based or HTTPS-based SLB instance by using the insert cookie method

    -   The annotations in the following template apply only to HTTP-based or HTTPS-based SLB instances.
    -   If an SLB instance has multiple HTTP or HTTPS listeners, the setting takes effect on all the HTTP or HTTPS listeners.
    -   All annotations in the following template are required.
    ```
    apiVersion: v1
    kind: Service
    metadata:
      annotations:
        service.beta.kubernetes.io/alibaba-cloud-loadbalancer-sticky-session: "on"
        service.beta.kubernetes.io/alibaba-cloud-loadbalancer-sticky-session-type: "insert"
        service.beta.kubernetes.io/alibaba-cloud-loadbalancer-cookie-timeout: "1800"
        service.beta.kubernetes.io/alibaba-cloud-loadbalancer-protocol-port: "http:80"
      name: nginx
      namespace: default
    spec:
      ports:
      - port: 80
        protocol: TCP
        targetPort: 80
      selector:
        run: nginx
      type: LoadBalancer
    ```

-   Enable access control for an SLB instance

    -   Create an access control list \(ACL\) in the SLB console and note down the ACL ID. Then, use the following annotations to enable access control for an SLB instance.
    -   A whitelist allows only access from specific IP addresses. A blacklist restricts only access from specific IP addresses.
    -   All annotations in the following template are required.
    ```
    apiVersion: v1
    kind: Service
    metadata:
      annotations:
        service.beta.kubernetes.io/alibaba-cloud-loadbalancer-acl-status: "on"
        service.beta.kubernetes.io/alibaba-cloud-loadbalancer-acl-id: "${YOUR_ACL_ID}"
        service.beta.kubernetes.io/alibaba-cloud-loadbalancer-acl-type: "white"
      name: nginx
      namespace: default
    spec:
      ports:
      - port: 443
        protocol: TCP
        targetPort: 443
      selector:
        run: nginx
      type: LoadBalancer
    ```

-   Configure port forwarding for an SLB instance

    -   Port forwarding enables an SLB instance to forward requests from an HTTP port to an HTTPS port.
    -   Create a certificate in the SLB console and note down the certificate ID. Then, use the following annotations to configure port forwarding for an SLB instance.
    -   Annotations in the following template are required.
    ```
    apiVersion: v1
    kind: Service
    metadata:
      annotations:
        service.beta.kubernetes.io/alibaba-cloud-loadbalancer-protocol-port: "https:443,http:80"
        service.beta.kubernetes.io/alibaba-cloud-loadbalancer-cert-id: "${YOUR_CERT_ID}"
        service.beta.kubernetes.io/alibaba-cloud-loadbalancer-forward-port: "80:443"
      name: nginx
      namespace: default
    spec:
      ports:
      - name: https
        port: 443
        protocol: TCP
        targetPort: 443
      - name: http
        port: 80
        protocol: TCP
        targetPort: 80
      selector:
        run: nginx
      type: LoadBalancer
    ```


|Annotation|Type|Description|Default value|Supported CCM version|
|----------|----|-----------|-------------|---------------------|
|service.beta.kubernetes.io/alibaba-cloud-loadbalancer-protocol-port|string|The listener port. Separate multiple ports with commas \(,\). For example, `https:443,http:80`.|N/A|CCM 1.9.3 and later|
|service.beta.kubernetes.io/alibaba-cloud-loadbalancer-persistence-timeout|string|The session persistence period. Unit: seconds. This annotation applies only to TCP listeners. Valid values: 0 to 3600.

Default value: 0. By default, session persistence is disabled.

For more information, see [CreateLoadBalancerTCPListener](/intl.en-US/User Guide/Developer Guide/TCP listeners/CreateLoadBalancerTCPListener.md).

|0|CCM 1.9.3 and later|
|service.beta.kubernetes.io/alibaba-cloud-loadbalancer-sticky-session|string|Specifies whether to enable session persistence. Valid values: on and off.**Note:** This annotation applies only to HTTP and HTTPS listeners.

For more information, see [CreateLoadBalancerHTTPListener](/intl.en-US/User Guide/Developer Guide/HTTP listeners/CreateLoadBalancerHTTPListener.md) and [CreateLoadBalancerHTTPSListener](/intl.en-US/User Guide/Developer Guide/HTTPS listeners/CreateLoadBalancerHTTPSListener.md).

|off|CCM 1.9.3 and later|
|service.beta.kubernetes.io/alibaba-cloud-loadbalancer-sticky-session-type|string|The method used to handle a cookie. Valid values: -   insert: inserts a cookie.
-   server: rewrites a cookie.

**Note:**

-   This annotation applies only to HTTP and HTTPS listeners.
-   You must specify this annotation if the annotation service.beta.kubernetes.io/alibaba-cloud-loadbalancer-sticky-session is set to on.

For more information, see [CreateLoadBalancerHTTPListener](/intl.en-US/User Guide/Developer Guide/HTTP listeners/CreateLoadBalancerHTTPListener.md) and [CreateLoadBalancerHTTPSListener](/intl.en-US/User Guide/Developer Guide/HTTPS listeners/CreateLoadBalancerHTTPSListener.md).

|N/A|CCM 1.9.3 and later|
|service.beta.kubernetes.io/alibaba-cloud-loadbalancer-cookie-timeout|string|The timeout period of a cookie. Unit: seconds. Valid values: 1 to 86400.**Note:** You must specify this annotation if service.beta.kubernetes.io/alibaba-cloud-loadbalancer-sticky-session is set to on and service.beta.kubernetes.io/alibaba-cloud-loadbalancer-sticky-session-type is set to insert.

For more information, see [CreateLoadBalancerHTTPListener](/intl.en-US/User Guide/Developer Guide/HTTP listeners/CreateLoadBalancerHTTPListener.md) and [CreateLoadBalancerHTTPSListener](/intl.en-US/User Guide/Developer Guide/HTTPS listeners/CreateLoadBalancerHTTPSListener.md).

|N/A|CCM 1.9.3 and later|
|service.beta.kubernetes.io/alibaba-cloud-loadbalancer-cookie|string|The name of a cookie configured on a backend server. The name must be 1 to 200 characters in length, and can contain ASCII letters and digits only. It cannot contain commas \(,\), semicolons \(;\), or spaces. It cannot start with a dollar sign \($\).

**Note:**

You must specify this annotation if service.beta.kubernetes.io/alibaba-cloud-loadbalancer-sticky-session is set to on and service.beta.kubernetes.io/alibaba-cloud-loadbalancer-sticky-session-type is set to server.

For more information, see [CreateLoadBalancerHTTPListener](/intl.en-US/User Guide/Developer Guide/HTTP listeners/CreateLoadBalancerHTTPListener.md) and [CreateLoadBalancerHTTPSListener](/intl.en-US/User Guide/Developer Guide/HTTPS listeners/CreateLoadBalancerHTTPSListener.md).

|N/A|CCM 1.9.3 and later|
|service.beta.kubernetes.io/alibaba-cloud-loadbalancer-cert-id|string|The ID of a certificate for an SLB instance. You must upload the certificate in the [SLB console](https://slb.console.aliyun.com/slb/cn-shenzhen/certs).|N/A|CCM 1.9.3.164-g2105d2e-aliyun and later|
|service.beta.kubernetes.io/alibaba-cloud-loadbalancer-health-check-flag|string|Specifies whether to enable the health check feature. Valid values: on and off.-   Default value for TCP listeners: on. You cannot modify the value.
-   Default value for HTTP listeners: off.

|Default value: off. This annotation is not required for TCP listeners. By default, the health check feature is enabled for TCP listeners. You cannot disable it.|CCM 1.9.3 and later|
|service.beta.kubernetes.io/alibaba-cloud-loadbalancer-health-check-type|string|The type of health checks. Valid values: tcp and http. For more information, see [CreateLoadBalancerTCPListener](/intl.en-US/User Guide/Developer Guide/TCP listeners/CreateLoadBalancerTCPListener.md).

|tcp|CCM 1.9.3 and later|
|service.beta.kubernetes.io/alibaba-cloud-loadbalancer-health-check-uri|string|The URI used for health checks. **Note:** This annotation is not required when the type of health checks is TCP.

For more information, see [CreateLoadBalancerTCPListener](/intl.en-US/User Guide/Developer Guide/TCP listeners/CreateLoadBalancerTCPListener.md).

|N/A|CCM 1.9.3 and later|
|service.beta.kubernetes.io/alibaba-cloud-loadbalancer-health-check-connect-port|string|The port used for health checks. Valid values: 1 to 65535For more information, see [CreateLoadBalancerTCPListener](/intl.en-US/User Guide/Developer Guide/TCP listeners/CreateLoadBalancerTCPListener.md).

|N/A|CCM 1.9.3 and later|
|service.beta.kubernetes.io/alibaba-cloud-loadbalancer-healthy-threshold|string|The number of consecutive successes of health checks before a backend server is declared as healthy. Valid values: 2 to 10.

For more information, see [CreateLoadBalancerTCPListener](/intl.en-US/User Guide/Developer Guide/TCP listeners/CreateLoadBalancerTCPListener.md).

|3|CCM 1.9.3 and later|
|service.beta.kubernetes.io/alibaba-cloud-loadbalancer-unhealthy-threshold|string|The number of consecutive failures of health checks before a backend server is declared as unhealthy. Valid values: 2~10

For more information, see [CreateLoadBalancerTCPListener](/intl.en-US/User Guide/Developer Guide/TCP listeners/CreateLoadBalancerTCPListener.md).

|3|CCM 1.9.3 and later|
|service.beta.kubernetes.io/alibaba-cloud-loadbalancer-health-check-interval|string|The interval between two consecutive health checks. Unit: seconds. Valid values: 1 to 50.

For more information, see [CreateLoadBalancerTCPListener](/intl.en-US/User Guide/Developer Guide/TCP listeners/CreateLoadBalancerTCPListener.md).

|2|CCM 1.9.3 and later|
|service.beta.kubernetes.io/alibaba-cloud-loadbalancer-health-check-connect-timeout|string|The timeout period for waiting for a health check response. Unit: seconds. This annotation applies to TCP health checks. If a backend server does not respond within the specified timeout period, the health check fails. Valid values: 1 to 300.

**Note:** If the value of service.beta.kubernetes.io/alibaba-cloud-loadbalancer-health-check-connect-timeout is smaller than that of service.beta.kubernetes.io/alibaba-cloud-loadbalancer-health-check-interval, service.beta.kubernetes.io/alibaba-cloud-loadbalancer-health-check-connect-timeout is invalid. In this case, the value of service.beta.kubernetes.io/alibaba-cloud-loadbalancer-health-check-interval is used as the timeout period.

For more information, see [CreateLoadBalancerTCPListener](/intl.en-US/User Guide/Developer Guide/TCP listeners/CreateLoadBalancerTCPListener.md).

|5|CCM 1.9.3 and later|
|service.beta.kubernetes.io/alibaba-cloud-loadbalancer-health-check-timeout|string|The timeout period for waiting for a health check response. Unit: seconds. This annotation applies to HTTP health checks. If a backend server does not respond within the specified timeout period, the health check fails. Valid values: 1 to 300.

**Note:** If the value of service.beta.kubernetes.io/alibaba-cloud-loadbalancer-health-check-timeout is smaller than that of service.beta.kubernetes.io/alibaba-cloud-loadbalancer-health-check-interval, service.beta.kubernetes.io/alibaba-cloud-loadbalancer-health-check-timeout is invalid. In this case, the value of service.beta.kubernetes.io/alibaba-cloud-loadbalancer-health-check-interval is used as the timeout period.

For more information, see [CreateLoadBalancerTCPListener](/intl.en-US/User Guide/Developer Guide/TCP listeners/CreateLoadBalancerTCPListener.md).

|5|CCM 1.9.3 and later|
|service.beta.kubernetes.io/alibaba-cloud-loadbalancer-health-check-domain|string|The domain name used for health checks. -   $\_ip: the private IP address of a backend server. If you do not specify this annotation or you set it to $\_ip, the SLB instance uses the private IP address of each backend server as the domain name for health checks.
-   domain: The domain name must be 1 to 80 characters in length, and can contain only letters, digits, periods \(.\), and hyphens \(-\).

|N/A|CCM 1.9.3 and later|
|service.beta.kubernetes.io/alibaba-cloud-loadbalancer-health-check-httpcode|string|The HTTP status code that specifies a successful health check. Separate multiple HTTP status codes with commas \(,\). Valid values: -   http\_2xx
-   http\_3xx
-   http\_4xx
-   http\_5xx

Default value: http\_2xx.|http\_2xx|CCM 1.9.3 and later|
|service.beta.kubernetes.io/alibaba-cloud-loadbalancer-acl-status|string|Specifies whether to enable the access control feature. Valid values: on and off.|off|CCM 1.9.3.164-g2105d2e-aliyun and later|
|service.beta.kubernetes.io/alibaba-cloud-loadbalancer-acl-id|string|The ID of the ACL to which listeners of an SLB instance are bound. This annotation is required if the annotation service.beta.kubernetes.io/alibaba-cloud-loadbalancer-acl-status is set to on.|N/A|CCM 1.9.3.164-g2105d2e-aliyun and later|
|service.beta.kubernetes.io/alibaba-cloud-loadbalancer-acl-type|string|The type of the ACL. Valid values: white and black.

-   white: specifies the ACL as a whitelist. Only requests from the IP addresses or CIDR blocks in the ACL are forwarded. Whitelists are applicable in scenarios where you want to allow only specific IP addresses to access an application. Risks may arise if you specify the ACL as a whitelist. After a whitelist is set, the SLB instance forwards only requests from the IP addresses in the whitelist. If you set a whitelist that has no IP addresses, listeners of the SLB instance forward all requests.
-   black: specifies the ACL as a blacklist. All requests from the IP addresses or CIDR blocks in the ACL are rejected. Blacklists are applicable in scenarios where you want to forbid specific IP addresses from accessing an application. If you set a blacklist that has no IP addresses, listeners of the SLB instance forward all requests. This annotation is required if the annotation service.beta.kubernetes.io/alibaba-cloud-loadbalancer-acl-status is set to on.

|N/A|CCM 1.9.3.164-g2105d2e-aliyun and later|
|service.beta.kubernetes.io/alibaba-cloud-loadbalancer-forward-port|string|Redirects HTTP requests to the specified port of an HTTPS listener. For example, `80:443`.|N/A|CCM 1.9.3.164-g2105d2e-aliyun and later|

## VServer groups

**Common annotations for configuring backend servers**

-   Add worker nodes that have specified labels as backend servers of an SLB instance

    Separate multiple labels with commas \(,\). For example, `"k1=v1,k2=v2"`. A node must have all specified labels before you can add it as a backend server.

    ```
    apiVersion: v1
    kind: Service
    metadata:
      annotations:
        service.beta.kubernetes.io/alibaba-cloud-loadbalancer-backend-label: "failure-domain.beta.kubernetes.io/zone=ap-southeast-5a"
      name: nginx
      namespace: default
    spec:
      ports:
      - port: 443
        protocol: TCP
        targetPort: 443
      selector:
        run: nginx
      type: LoadBalancer
    ```

-   Add the nodes where pods that run a service reside as the backend servers of an SLB instance

    -   By default, `externalTrafficPolicy` is set to Cluster for a service. In Cluster mode, all nodes in the cluster are added as backend servers of the SLB instance. In Local mode, only nodes where pods that run the service reside are added as backend servers of the SLB instance.

    -   In Local mode, you must set the scheduling algorithm to weighted round-robin \(WRR\).

        **Note:**

        For CCM 1.9.3.164-g2105d2e-aliyun and later, node weights are calculated based on the number of pods deployed on each node for services whose externalTrafficPolicy is set to **Local**. For more information about node weight calculation, see [How does CCM calculate node weights in Local mode?](/intl.en-US/User Guide for Kubernetes Clusters/Network management/FAQ.md).

    ```
    apiVersion: v1
    kind: Service
    metadata:
      annotations:
        service.beta.kubernetes.io/alibaba-cloud-loadbalancer-scheduler: "wrr"
      name: nginx
      namespace: default
    spec:
      externalTrafficPolicy: Local
      ports:
      - port: 80
        protocol: TCP
        targetPort: 80
      selector:
        run: nginx
      type: LoadBalancer
    ```

-   Remove backend servers in the **unschedulable** state from an SLB instance

    You can run the `kubectl cordon` and `kubectl drain` commands to set a node to the **unschedulable** state. By default, the annotation `service.beta.kubernetes.io/alibaba-cloud-loadbalancer-remove-unscheduled-backend` is set to off. In this case, nodes in the **unschedulable** state cannot be removed from VServer groups of an SLB instance. To removed backend servers in the **unschedulable** state from VServer groups of an SLB instance, set the annotation `service.beta.kubernetes.io/alibaba-cloud-loadbalancer-remove-unscheduled-backend` to on.

    ```
    apiVersion: v1
    kind: Service
    metadata:
      annotations:
        service.beta.kubernetes.io/alibaba-cloud-loadbalancer-remove-unscheduled-backend: "on"
      name: nginx
    spec:
      externalTrafficPolicy: Local
      ports:
      - name: http
        port: 30080
        protocol: TCP
        targetPort: 80
      selector:
        app: nginx
      type: LoadBalancer
    ```

-   Add pods assigned with elastic network interface \(ENIs\) as the backend servers of an SLB instance

    In Terway mode, you can use the annotation`service.beta.kubernetes.io/backend-type: "eni"` to add pods assigned with ENIs as the backend servers of an SLB instance. This improves network forwarding performance.

    ```
      apiVersion: v1
      kind: Service
      metadata:
        annotations:
          service.beta.kubernetes.io/backend-type: "eni"
        name: nginx
      spec:
        ports:
        - name: http
          port: 30080
          protocol: TCP
          targetPort: 80
        selector:
          app: nginx
        type: LoadBalancer
    ```


|Annotation|Type|Description|Default value|Supported CCM version|
|----------|----|-----------|-------------|---------------------|
|service.beta.kubernetes.io/alibaba-cloud-loadbalancer-backend-label|string|Specifies that worker nodes that have matching labels are added as backend servers of an SLB instance.|N/A|CCM 1.9.3 and later|
|externalTrafficPolicy|string|The policy for adding nodes as backend servers. Valid values: -   Cluster: adds all nodes as backend servers.
-   Local: adds the nodes where pods are deployed as backend servers.

|Cluster|CCM 1.9.3 and later|
|service.beta.kubernetes.io/alibaba-cloud-loadbalancer-remove-unscheduled-backend|string|Removes backend servers in the unschedulable state from an SLB instance. Valid values: on and off.|off|CCM 1.9.3.164-g2105d2e-aliyun and later|
|service.beta.kubernetes.io/backend-type|string|Adds pods assigned with ENIs as the backend servers of an SLB instance. You must set the annotation to `eni` in Terway mode.|N/A|CCM 1.9.3.164-g2105d2e-aliyun and later|

