# Use annotations to configure load balancing

You can add annotations to the YAML file of a Service to configure load balancing. This topic describes how to use annotations to configure load balancing. You can configure the Server Load Balancer \(SLB\) instance, listeners, and vServer groups.

## Usage notes

-   The values of annotations are case-sensitive.
-   In `annotations`, the keyword `alicloud` was changed to `alibaba-cloud` on September 11, 2019.

    Examples:

    Before the update: `service.beta.kubernetes.io/alicloud-loadbalancer-id`

    After the update: `service.beta.kubernetes.io/alibaba-cloud-loadbalancer-id`

    The system still supports annotations that use the `alicloud` keyword. You do not need to change the existing annotations.


## SLB

**Common annotations used to configure load balancing**

-   Create an Internet-facing SLB instance

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

-   Create an internal-facing SLB instance

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

    You must first create a certificate in the SLB console. Then, you can use the certificate ID and the following template to create a LoadBalancer Service and an HTTPS-based SLB instance.

    **Note:** HTTPS listeners of the SLB instance decrypt HTTPS requests into HTTP requests and forward the requests to pods on the backend servers.

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

    For more information about specifications of SLB instances, see [CreateLoadBalancer](/intl.en-US/Classic Load Balancer/CLB Developer Guide/Developer Guide/Server Load Balancer (SLB) instances/CreateLoadBalancer.md). Use the following template to create a LoadBalancer Service and an SLB instance of a specified specification. You can also use the template to update the specification of an existing SLB instance.

    **Note:** If you modify the specification of the SLB instance in the SLB console, the modification may be restored by the cloud controller manager \(CCM\). Proceed with caution.

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

    -   By default, the CCM does not overwrite listeners of an existing SLB instance. To overwrite listeners of an existing SLB instance, set the annotation `service.beta.kubernetes.io/alibaba-cloud-loadbalancer-force-override-listeners` to true.

        **Note:** The following list explains why CCM does not overwrite listeners of an existing SLB instance:

        -   If you overwrite the listeners of an existing SLB instance that distributes network traffic to a Service, a Service interruption may occur.
        -   CCM supports limited backend configurations and cannot handle complex configurations. If the vServer group requires complex configurations, you can manually create listeners in the SLB console instead of overwriting the existing listeners.
        In both cases, we recommend that you do not overwrite the listeners of existing SLB instances. However, you can overwrite an existing listener if the port of the listener is no longer in use.

    -   You cannot add additional tags to an existing SLB instance by using the `annotation: service.beta.kubernetes.io/alibaba-cloud-loadbalancer-additional-resource-tags` annotation.
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

    Use the following template to create a LoadBalancer Service and forcibly overwrite an existing listener of the SLB instance. If you specify a different port number, the original port number is overwritten.

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

-   Specify primary and secondary zones for an SLB instance

    -   Primary and secondary zones are not supported by SLB instances that are deployed in some regions, such as the Indonesia \(Jakarta\) region.
    -   After you specify the primary and secondary zones for an SLB instance, the zones cannot be changed.
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

-   Create a pay-by-bandwidth SLB instance

    -   Only Internet-facing SLB instances support the pay-by-bandwidth billing method.

        For more information about limits on billing methods for Internet-facing SLB instances, see [ModifyLoadBalancerInstanceSpec](/intl.en-US/Classic Load Balancer/CLB Developer Guide/Developer Guide/Server Load Balancer (SLB) instances/ModifyLoadBalancerInstanceSpec.md).

    -   The annotations in the following template are required.

        Modify the annotation values based on your business requirements.

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

    **Note:** The annotation service.beta.kubernetes.io/alibaba-cloud-loadbalancer-bandwidth specifies the maximum bandwidth value.

-   Create an SLB instance that has the health check feature enabled
    -   Enable TCP health checks

        -   By default, the health check feature is enabled for TCP listeners and cannot be disabled. The annotation `service.beta.kubernetes.io/alibaba-cloud-loadbalancer-health-check-flag annotation` is invalid.
        -   To enable TCP health checks, all annotations that are specified in the following example are required.
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

    -   Enable HTTP health checks

        To enable HTTP health checks, all annotations that are specified in the following example are required.

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

-   Set the routing algorithm for an SLB instance

    -   rr: Requests are distributed to backend servers in sequence. This is the default routing algorithm.
    -   wrr: Backend servers that have higher weights receive more requests than those that have lower weights.
    -   wlc: Requests are distributed based on the weight and load of each backend server. The load refers to the number of connections to a backend server. If two backend servers have the same weight, the backend server that has fewer connections is expected to receive more requests.
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

-   Specify a vSwitch for an SLB instance

    -   Obtain the ID of a specific vSwitch in the Virtual Private Cloud \(VPC\) console. Then, use the annotations in the following template to specify the vSwitch for an SLB instance.
    -   The specified vSwitch and the cluster must be deployed in the same VPC.
    -   All annotations specified in the following template are required.
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

-   Add additional tags to an SLB instance

    Separate multiple tags with commas \(,\). Example: `"k1=v1,k2=v2"`.

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

-   Create an IPv6 SLB instance

    -   Use the following template to create a LoadBalancer Service and an IPv6 SLB instance. The kube-proxy mode must be set to IPVS.
    -   The assigned IPv6 address can be used only in an IPv6 network.
    -   You cannot change the IP address type after you create the IPv6 SLB instance.
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

    By default, deletion protection is enabled for SLB instances.

    **Note:** You may manually enable deletion protection in the SLB console for an SLB instance that is created by a LoadBalancer Service. In this case, you can run the `kubectl delete svc {your-svc-name}` command to delete the SLB instance.

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

    By default, the configuration read-only mode is enabled for SLB instances.

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

-   Specify the name of an SLB instance

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

-   Specify a resource group to which an SLB instance belongs

    Log on to the [Resource Management console](https://resourcemanager.console.aliyun.com/) to obtain the ID of a specific resource group. Then, use the annotation in the following template to specify the resource group to which the SLB instance belongs.

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


## Listener

**Common annotations used to configure listeners**

-   Set the session persistence period for a TCP-based SLB instance

    -   The annotation `service.beta.kubernetes.io/alibaba-cloud-loadbalancer-persistence-timeout` applies to only TCP listeners.
    -   If an SLB instance has multiple TCP listeners, the changes apply to all the TCP listeners.
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

-   Enable session persistence for an HTTP/HTTPS-based SLB instance by inserting a cookie

    -   The annotations in the following template apply to only HTTP-based and HTTPS-based SLB instances.
    -   If an SLB instance has multiple HTTP or HTTPS listeners, the changes apply to all the HTTP or HTTPS listeners.
    -   To configure session persistence by inserting a cookie, all annotations in the following example are required.
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

    -   Create an access control list \(ACL\) in the SLB console and take note of the ACL ID. Then, use the annotations in the following template to enable access control for an SLB instance.
    -   A whitelist allows access from only specified IP addresses. A blacklist denies access from only specific IP addresses.
    -   All annotations specified in the following template are required.
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

    -   Port forwarding allows an SLB instance to forward requests from an HTTP port to an HTTPS port.
    -   Create a certificate in the SLB console and take note of the certificate ID. Then, use the following annotations to configure port forwarding for an SLB instance.
    -   All annotations specified in the following example are required.
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


## vServer groups

**Common annotations used to configure backend servers**

-   Add worker nodes that have specified labels as backend servers of an SLB instance

    Separate multiple labels with commas \(,\). Example: `"k1=v1,k2=v2"`. A node must have all specified labels before you can add the node as a backend server.

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

-   Add the nodes where the pods are deployed as the backend servers of an SLB instance

    -   By default, `externalTrafficPolicy` is set to Cluster for a Service. In Cluster mode, all nodes in the cluster are added as backend servers of the SLB instance. In Local mode, only nodes where the pods are deployed are added as backend servers of the SLB instance.

    -   In Local mode, you must set the routing algorithm to weighted round-robin \(WRR\).

        **Note:**

        For CCM V1.9.3.164-g2105d2e-aliyun and later, node weights are calculated based on the number of pods that run on each node for Services whose externalTrafficPolicy is set to **Local**. For more information about node weight calculation, see [t2039653.md\#section\_7xa\_hpn\_yfw]().

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

-   Remove backend servers in the **Unschedulable** state from an SLB instance

    -   You can run the `kubectl cordon` and `kubectl drain` commands to set a node to the **Unschedulable** state. By default, the annotation `service.beta.kubernetes.io/alibaba-cloud-loadbalancer-remove-unscheduled-backend` is set to off. In this case, nodes in the **Unschedulable** state cannot be removed from vServer groups of an SLB instance.
    -   To remove backend servers in the **Unschedulable** state from vServer groups of an SLB instance, set the annotation `service.beta.kubernetes.io/alibaba-cloud-loadbalancer-remove-unscheduled-backend` to on.
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

-   Add pods that are assigned elastic network interfaces \(ENIs\) as the backend servers of an SLB instance

    When the Terway network plug-in is used, you can use the annotation `service.beta.kubernetes.io/backend-type: "eni"` to add pods that are assigned ENIs as the backend servers of an SLB instance. This improves network forwarding performance.

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

    **Note:** You can also set `eni` in `service.beta.kubernetes.io/backend-type: "eni"` to `ecs`. This allows you to add Elastic Compute Service \(ECS\) instances as backend severs of an SLB instance.


## Common annotations

-   **Common annotations that are used to configure load balancing**

    |Annotation|Type|Description|Default|Supported CMM version|
    |----------|----|-----------|-------|---------------------|
    |service.beta.kubernetes.io/alibaba-cloud-loadbalancer-address-type|string|The type of SLB instance. Valid values: internet and intranet.     -   internet: accesses the Service over the Internet. This is the default value. The **address type** of the SLB instance must be set to **Internet**.
    -   intranet: accesses the Service over an internal network. The **address type** of the SLB instance must be set to **intranet**.
|internet|CCM V1.9.3 and later|
    |service.beta.kubernetes.io/alibaba-cloud-loadbalancer-charge-type|string|The billing method of an SLB instance. Valid values: paybytraffic and paybybandwidth.|paybytraffic|CCM V1.9.3 and later|
    |service.beta.kubernetes.io/alibaba-cloud-loadbalancer-id|string|The ID of an SLB instance. You can specify an existing SLB instance by using the annotation service.beta.kubernetes.io/alibaba-cloud-loadbalancer-id. By default, if you specify an existing SLB instance, the CCM does not overwrite the listeners of the SLB instance. To overwrite the listeners, set the annotation service.beta.kubernetes.io/alibaba-cloud-loadbalancer-force-override-listeners to true.|None|CCM V1.9.3.81-gca19cd4-aliyun and later|
    |service.beta.kubernetes.io/alibaba-cloud-loadbalancer-spec|string|The specification of an SLB instance. For more information, see [CreateLoadBalancer](/intl.en-US/Classic Load Balancer/CLB Developer Guide/Developer Guide/Server Load Balancer (SLB) instances/CreateLoadBalancer.md).|None|CCM V1.9.3 and later|
    |service.beta.kubernetes.io/alibaba-cloud-loadbalancer-master-zoneid|string|The ID of the zone for the primary backend server.|None|CCM V1.9.3.10-gfb99107-aliyun and later|
    |service.beta.kubernetes.io/alibaba-cloud-loadbalancer-slave-zoneid|string|The ID of the zone for the secondary backend server.|None|CCM V1.9.3.10-gfb99107-aliyun and later|
    |service.beta.kubernetes.io/alibaba-cloud-loadbalancer-force-override-listeners|string|Specifies whether to overwrite the listeners of an existing SLB instance that is specified.|false: The listeners of the existing SLB instance are not overwritten.|CCM V1.9.3.81-gca19cd4-aliyun and later|
    |service.beta.kubernetes.io/alibaba-cloud-loadbalancer-bandwidth|string|The bandwidth of the SLB instance. This annotation applies to only Internet-facing SLB instances.|50|CCM V1.9.3.10-gfb99107-aliyun and later|
    |service.beta.kubernetes.io/alibaba-cloud-loadbalancer-scheduler|string|The routing algorithm. Valid values: wrr, wlc, and rr.     -   wrr: Backend servers that have higher weights receive more requests than the backend servers that have lower weights.
    -   wlc: Requests are distributed based on the weight and load of each backend server. The load refers to the number of connections to a backend server. If two backend servers have the same weight, the backend server that has fewer connections is expected to receive more requests.
    -   rr: Requests are distributed to backend servers in sequence. This is the default routing algorithm.
|rr|CCM V1.9.3 and later|
    |service.beta.kubernetes.io/alibaba-cloud-loadbalancer-vswitch-id|string|The ID of the vSwitch to which the SLB instance belongs. To set this annotation, set the annotation service.beta.kubernetes.io/alibaba-cloud-loadbalancer-address-type to intranet.|None|CCM V1.9.3 and later|
    |service.beta.kubernetes.io/alibaba-cloud-loadbalancer-additional-resource-tags|string|The tags that you want to add to an SLB instance. Separate multiple tags with commas \(,\). Example: `"k1=v1,k2=v2"`.|None|CCM V1.9.3 and later|
    |service.beta.kubernetes.io/alibaba-cloud-loadbalancer-ip-version|string|The IP protocol of an SLB instance. Valid values: ipv4 and ipv6.|ipv4|CCM V1.9.3.220-g24b1885-aliyun and later|
    |service.beta.kubernetes.io/alibaba-cloud-loadbalancer-delete-protection|string|Specifies whether to enable deletion protection for an SLB instance. Valid values: on and off.|on|CCM V1.9.3.313-g748f81e-aliyun and later|
    |service.beta.kubernetes.io/alibaba-cloud-loadbalancer-modification-protection|string|Specifies whether to enable the configuration read-only mode for an SLB instance. Valid values: ConsoleProtection and NonProtection.|ConsoleProtection|CCM V1.9.3.313-g748f81e-aliyun and later|
    |service.beta.kubernetes.io/alibaba-cloud-loadbalancer-resource-group-id|string|The resource group to which an SLB instance belongs.|None|CCM V1.9.3.313-g748f81e-aliyun and later|
    |service.beta.kubernetes.io/alibaba-cloud-loadbalancer-name|string|The name of an SLB instance.|None|CCM V1.9.3.313-g748f81e-aliyun and later|

-   **Common annotations used to configure listeners**

    |Annotation|Type|Description|Default|Supported CMM version|
    |----------|----|-----------|-------|---------------------|
    |service.beta.kubernetes.io/alibaba-cloud-loadbalancer-protocol-port|string|The listening port. Separate multiple ports with commas \(,\). Example: `https:443,http:80`.|None|CCM V1.9.3 and later|
    |service.beta.kubernetes.io/alibaba-cloud-loadbalancer-persistence-timeout|string|The session persistence period. Unit: seconds. This annotation applies to only TCP listeners. Valid values: 0 to 3600.

Default value: 0. By default, session persistence is disabled.

For more information, see [CreateLoadBalancerTCPListener](/intl.en-US/Classic Load Balancer/CLB Developer Guide/Developer Guide/TCP listeners/CreateLoadBalancerTCPListener.md).

|0|CCM V1.9.3 and later|
    |service.beta.kubernetes.io/alibaba-cloud-loadbalancer-sticky-session|string|Specifies whether to enable session persistence. Valid values: on and off. **Note:** This annotation applies to only HTTP and HTTPS listeners.

For more information, see [CreateLoadBalancerHTTPListener](/intl.en-US/Classic Load Balancer/CLB Developer Guide/Developer Guide/HTTP listeners/CreateLoadBalancerHTTPListener.md) and [CreateLoadBalancerHTTPSListener](/intl.en-US/Classic Load Balancer/CLB Developer Guide/Developer Guide/HTTPS listeners/CreateLoadBalancerHTTPSListener.md).

|off|CCM V1.9.3 and later|
    |service.beta.kubernetes.io/alibaba-cloud-loadbalancer-sticky-session-type|string|The method that is used to process the cookie. Valid values:     -   insert: inserts a cookie.
    -   server: rewrites a cookie.
**Note:**

    -   This annotation applies to only HTTP and HTTPS listeners.
    -   If the annotation service.beta.kubernetes.io/alibaba-cloud-loadbalancer-sticky-session is set to on, you must specify this annotation.
For more information, see [CreateLoadBalancerHTTPListener](/intl.en-US/Classic Load Balancer/CLB Developer Guide/Developer Guide/HTTP listeners/CreateLoadBalancerHTTPListener.md) and [CreateLoadBalancerHTTPSListener](/intl.en-US/Classic Load Balancer/CLB Developer Guide/Developer Guide/HTTPS listeners/CreateLoadBalancerHTTPSListener.md).

|None|CCM V1.9.3 and later|
    |service.beta.kubernetes.io/alibaba-cloud-loadbalancer-cookie-timeout|string|The timeout period of a cookie. Unit: seconds. Valid values: 1 to 86400. **Note:** If service.beta.kubernetes.io/alibaba-cloud-loadbalancer-sticky-session is set to on and service.beta.kubernetes.io/alibaba-cloud-loadbalancer-sticky-session-type is set to insert, you must specify this annotation.

For more information, see [CreateLoadBalancerHTTPListener](/intl.en-US/Classic Load Balancer/CLB Developer Guide/Developer Guide/HTTP listeners/CreateLoadBalancerHTTPListener.md) and [CreateLoadBalancerHTTPSListener](/intl.en-US/Classic Load Balancer/CLB Developer Guide/Developer Guide/HTTPS listeners/CreateLoadBalancerHTTPSListener.md).

|None|CCM V1.9.3 and later|
    |service.beta.kubernetes.io/alibaba-cloud-loadbalancer-cookie|string|The name of a cookie configured on a backend server. The name must be 1 to 200 characters in length, and can contain only ASCII letters and digits. It cannot contain commas \(,\), semicolons \(;\), or spaces. It cannot start with a dollar sign \($\).

**Note:**

If service.beta.kubernetes.io/alibaba-cloud-loadbalancer-sticky-session is set to on and service.beta.kubernetes.io/alibaba-cloud-loadbalancer-sticky-session-type is set to server, you must specify this annotation.

For more information, see [CreateLoadBalancerHTTPListener](/intl.en-US/Classic Load Balancer/CLB Developer Guide/Developer Guide/HTTP listeners/CreateLoadBalancerHTTPListener.md) and [CreateLoadBalancerHTTPSListener](/intl.en-US/Classic Load Balancer/CLB Developer Guide/Developer Guide/HTTPS listeners/CreateLoadBalancerHTTPSListener.md).

|None|CCM V1.9.3 and later|
    |service.beta.kubernetes.io/alibaba-cloud-loadbalancer-cert-id|string|The ID of a certificate for an SLB instance. You must first upload the certificate in the [SLB console](https://slb.console.aliyun.com/slb/cn-shenzhen/certs).|None|CCM V1.9.3.164-g2105d2e-aliyun and later|
    |service.beta.kubernetes.io/alibaba-cloud-loadbalancer-health-check-flag|string|Specifies whether to enable the health check feature. Valid values: on and off.    -   Default value for TCP listeners: on. You cannot modify the value.
    -   Default value for HTTP listeners: off.
|Default value: off. This annotation is not required for TCP listeners. By default, the health check feature is enabled for TCP listeners. This feature cannot be disabled.|CCM V1.9.3 and later|
    |service.beta.kubernetes.io/alibaba-cloud-loadbalancer-health-check-type|string|The type of health check. Valid values: tcp and http. For more information, see [CreateLoadBalancerTCPListener](/intl.en-US/Classic Load Balancer/CLB Developer Guide/Developer Guide/TCP listeners/CreateLoadBalancerTCPListener.md).

|tcp|CCM V1.9.3 and later|
    |service.beta.kubernetes.io/alibaba-cloud-loadbalancer-health-check-uri|string|The URI that is used for health checks. **Note:** When the type of health check is TCP, this annotation is not required.

For more information, see [CreateLoadBalancerTCPListener](/intl.en-US/Classic Load Balancer/CLB Developer Guide/Developer Guide/TCP listeners/CreateLoadBalancerTCPListener.md).

|None|CCM V1.9.3 and later|
    |service.beta.kubernetes.io/alibaba-cloud-loadbalancer-health-check-connect-port|string|The port t that is used for health checks. Valid values: 1 to 65535.|None|CCM V1.9.3 and later|
    |service.beta.kubernetes.io/alibaba-cloud-loadbalancer-healthy-threshold|string|The number of consecutive successful health checks that must occur before a backend server is declared healthy. Valid values: 2 to 10.

For more information, see [CreateLoadBalancerTCPListener](/intl.en-US/Classic Load Balancer/CLB Developer Guide/Developer Guide/TCP listeners/CreateLoadBalancerTCPListener.md).

|3|CCM V1.9.3 and later|
    |service.beta.kubernetes.io/alibaba-cloud-loadbalancer-unhealthy-threshold|string|The number of consecutive failed health checks that must occur before a backend server is declared unhealthy. Valid values: 2~10

For more information, see [CreateLoadBalancerTCPListener](/intl.en-US/Classic Load Balancer/CLB Developer Guide/Developer Guide/TCP listeners/CreateLoadBalancerTCPListener.md).

|3|CCM V1.9.3 and later|
    |service.beta.kubernetes.io/alibaba-cloud-loadbalancer-health-check-interval|string|The health check interval between two consecutive health checks. Unit: seconds. Valid values: 1 to 50.

For more information, see [CreateLoadBalancerTCPListener](/intl.en-US/Classic Load Balancer/CLB Developer Guide/Developer Guide/TCP listeners/CreateLoadBalancerTCPListener.md).

|2|CCM V1.9.3 and later|
    |service.beta.kubernetes.io/alibaba-cloud-loadbalancer-health-check-connect-timeout|string|The timeout period to wait for a health check response. Unit: seconds. This annotation applies to TCP health checks. If a backend server does not respond within the specified time period, the health check fails. Valid values: 1 to 300.

**Note:** If the value of service.beta.kubernetes.io/alibaba-cloud-loadbalancer-health-check-connect-timeout is smaller than that of service.beta.kubernetes.io/alibaba-cloud-loadbalancer-health-check-interval, service.beta.kubernetes.io/alibaba-cloud-loadbalancer-health-check-connect-timeout is invalid. In this case, the value of service.beta.kubernetes.io/alibaba-cloud-loadbalancer-health-check-interval is used as the timeout period.

For more information, see [CreateLoadBalancerTCPListener](/intl.en-US/Classic Load Balancer/CLB Developer Guide/Developer Guide/TCP listeners/CreateLoadBalancerTCPListener.md).

|5|CCM V1.9.3 and later|
    |service.beta.kubernetes.io/alibaba-cloud-loadbalancer-health-check-timeout|string|The timeout period to wait for a health check response. Unit: seconds. This annotation applies to HTTP health checks. If a backend server does not respond within the specified time period, the health check fails. Valid values: 1 to 300.

**Note:** If the value of service.beta.kubernetes.io/alibaba-cloud-loadbalancer-health-check-timeout is smaller than that of service.beta.kubernetes.io/alibaba-cloud-loadbalancer-health-check-interval, service.beta.kubernetes.io/alibaba-cloud-loadbalancer-health-check-timeout is invalid. In this case, the value of service.beta.kubernetes.io/alibaba-cloud-loadbalancer-health-check-interval is used as the timeout period.

For more information, see [CreateLoadBalancerTCPListener](/intl.en-US/Classic Load Balancer/CLB Developer Guide/Developer Guide/TCP listeners/CreateLoadBalancerTCPListener.md).

|5|CCM V1.9.3 and later|
    |service.beta.kubernetes.io/alibaba-cloud-loadbalancer-health-check-domain|string|The domain name that is used for health checks.     -   $\_ip: the private IP address of a backend server. If you do not set this annotation or set the annotation to $\_ip, the SLB instance uses the private IP address of each backend server as the domain name for health checks.
    -   domain: The domain name must be 1 to 80 characters in length, and can contain only letters, digits, periods \(.\),and hyphens \(-\).
|None|CCM V1.9.3 and later|
    |service.beta.kubernetes.io/alibaba-cloud-loadbalancer-health-check-httpcode|string|The HTTP status code that specifies that a health check is successful. Separate multiple HTTP status codes with commas \(,\). Valid values:     -   http\_2xx
    -   http\_3xx
    -   http\_4xx
    -   http\_5xx
Default value: http\_2xx.|http\_2xx|CCM V1.9.3 and later|
    |service.beta.kubernetes.io/alibaba-cloud-loadbalancer-acl-status|string|Specifies whether to enable access control for the listeners. Valid values: on and off.|off|CCM V1.9.3.164-g2105d2e-aliyun and later|
    |service.beta.kubernetes.io/alibaba-cloud-loadbalancer-acl-id|string|The ID of the ACL to which the listeners are bound. If the annotation service.beta.kubernetes.io/alibaba-cloud-loadbalancer-acl-status is set to on, this annotation is required.|None|CCM V1.9.3.164-g2105d2e-aliyun and later|
    |service.beta.kubernetes.io/alibaba-cloud-loadbalancer-acl-type|string|The type of ACL. Valid values: white and black.

    -   white: specifies the ACL as a whitelist. Only requests from the IP addresses or CIDR blocks in the ACL are forwarded. Whitelists apply to scenarios when you want to allow only specified IP addresses to access an application. Proceed with caution when you specify the ACL as a whitelist.If you specify the ACL as a whitelist, errors may occur. After a whitelist is set, the SLB instance forwards only requests from the IP addresses in the whitelist. If you set an empty whitelist, the listeners of the SLB instance forward all requests.
    -   black: specifies the ACL as a blacklist. All requests from the IP addresses or CIDR blocks in the ACL are rejected. Blacklists apply to scenarios when you want to block access from specified IP addresses to an application. If you set an empty blacklist, the listeners of the SLB instance forward all requests. If the annotation service.beta.kubernetes.io/alibaba-cloud-loadbalancer-acl-status is set to on, this annotation is required.
|None|CCM V1.9.3.164-g2105d2e-aliyun and later|
    |service.beta.kubernetes.io/alibaba-cloud-loadbalancer-forward-port|string|Redirects HTTP requests to the specified port of an HTTPS listener. Example: `80:443`.|None|CCM V1.9.3.164-g2105d2e-aliyun and later|

-   **Common annotations that are used to configure vServer groups**

    |Annotation|Type|Description|Default|Supported CMM version|
    |----------|----|-----------|-------|---------------------|
    |service.beta.kubernetes.io/alibaba-cloud-loadbalancer-backend-label|string|Specifies that worker nodes that have matching labels are added as backend servers of an SLB instance.|None|CCM V1.9.3 and later|
    |externalTrafficPolicy|string|The policy that is used to add nodes as backend servers. Valid values:     -   Cluster: adds all nodes as backend servers.
    -   Local: adds the nodes where the pods are deployed as backend servers.
|Cluster|CCM V1.9.3 and later|
    |service.beta.kubernetes.io/alibaba-cloud-loadbalancer-remove-unscheduled-backend|string|Removes backend servers in the Unschedulable state from an SLB instance. Valid values: on and off.|off|CCM V1.9.3.164-g2105d2e-aliyun and later|
    |service.beta.kubernetes.io/backend-type|string|The type of backend servers attached to an SLB instance. Valid values:

    -   `eni`: adds pods as the backend servers of an SLB instance. This parameter takes effect only in Terway mode. This improves network forwarding performance.
    -   `ecs`: adds ECS instances as the backend servers of an SLB instance.
|When the Flannel network plug-in is used, the default value is `ecs`. When the Terway network plug-in is used:

    -   The default value is `ecs`. This applies to Container Service for Kubernetes \(ACK\) clusters that were created before August 10, 2020.
    -   The default value is `eni`. This applies to ACK clusters that are created after August 10, 2020.
|CCM V1.9.3.164-g2105d2e-aliyun and later|


