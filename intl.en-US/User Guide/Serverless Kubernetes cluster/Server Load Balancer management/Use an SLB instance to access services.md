# Use an SLB instance to access services {#concept_d1b_y31_ydb .concept}

This topic describes how to access services by using an Alibaba Cloud Server Load Balancer \(SLB\) instance.

## Operate by using command line {#section_pwb_bj1_ydb .section}

1.  Create an Nginx application by using command line.

    ``` {#codeblock_zz6_zxm_968}
    root@master # kubectl run nginx --image=registry.aliyuncs.com/acs/netdia:latest
    root@master # kubectl get po 
    NAME                                   READY     STATUS    RESTARTS   AGE
    nginx-2721357637-d****                 1/1       Running   1          6s
    ```

2.  Create Alibaba Cloud Server Load Balancer service for the Nginx application and specify `type=LoadBalancer` to expose the Nginx service to the Internet.

    ``` {#codeblock_zb1_fr9_26w}
    root@master # kubectl expose deployment nginx --port=80 --target-port=80 --type=LoadBalancer
    root@master # kubectl get svc
    NAME                  CLUSTER-IP      EXTERNAL-IP      PORT(S)                        AGE
    nginx                 172.**.**.***   101.**.***.**   80:3****/TCP                   4s
    ```

3.  Visit `http://101.**.***.**` in the browser to access your Nginx service.

## More information {#section_krn_1j1_ydb .section}

Alibaba Cloud Server Load Balancer also supports parameter configurations such as health check, billing method, and load balancing. For more information, see [Server Load Balancer configuration parameters](#table_csn_1j1_ydb).

## Annotations {#section_mrn_1j1_ydb .section}

Alibaba Cloud supports a lot of Server Load Balancer features by using annotations.

**Use existing intranet Server Load Balancer instance**

1.  Use [Cloud Shell](intl.en-US/User Guide/Kubernetes cluster/Cluster management/Use kubectl commands in Cloud Shell to manage a Kubernetes cluster.md#) to access the target Kubernetes cluster.
2.  Create a file slb.svc, copy the following code into the file, and run the `kubectl apply -f slb.svc` command.

    ``` {#codeblock_rfv_0z9_7ov}
    apiVersion: v1
    kind: Service
    metadata:
      annotations:
        service.beta.kubernetes.io/alicloud-loadbalancer-address-type: intranet
        service.beta.kubernetes.io/alicloud-loadbalancer-id: your-loadbalancer-id
        service.beta.kubernetes.io/alicloud-loadbalancer-force-override-listeners: "true"
      labels:
        run: nginx
      name: nginx
      namespace: default
    spec:
      ports:
      - name: web
        port: 80
        protocol: TCP
        targetPort: 80
      selector:
        run: nginx
      sessionAffinity: None
      type: LoadBalancer
    ```

    **Note:** You must set three annotations.


**Create an HTTPS type Server Load Balancer instance**

Create a certificate in the Alibaba Cloud console and record the cert-id. Then, use the following annotation to create an HTTPS type Server Load Balancer instance.

``` {#codeblock_tl0_t8n_a9z}
apiVersion: v1
kind: Service
metadata:
  annotations:
    service.beta.kubernetes.io/alicloud-loadbalancer-cert-id: your-cert-id
    service.beta.kubernetes.io/alicloud-loadbalancer-protocol-port: "https:443"
  labels:
    run: nginx
  name: nginx
  namespace: default
spec:
  ports:
  - name: web
    port: 443
    protocol: TCP
    targetPort: 443
  selector:
    run: nginx
  sessionAffinity: None
  type: LoadBalancer
```

**Note:** Annotations are case sensitive.

|Annotation|Description|Default value|
|----------|-----------|-------------|
|service.beta.kubernetes.io/alicloud-loadbalancer-protocol-port|Use commas \(,\) to separate multiple values. For example, https:443,http:80.|None|
|service.beta.kubernetes.io/alicloud-loadbalancer-address-type|The value is Internet or intranet.|Internet|
|service.beta.kubernetes.io/alicloud-loadbalancer-slb-network-type|Server Load Balancer network type. The value is classic or VPC.|Classic|
|service.beta.kubernetes.io/alicloud-loadbalancer-charge-type|The value is paybytraffic or paybybandwidth.|paybybandwidth|
|service.beta.kubernetes.io/alicloud-loadbalancer-id|The Server Load Balancer instance ID. Specify an existing Server Load Balance with the loadbalancer-id, and the existing listener is overwritten. Server Load Balancer is not deleted when the service is deleted.|None|
|service.beta.kubernetes.io/alicloud-loadbalancer-backend-label|Use label to specify which nodes are mounted to the Server Load Balancer backend.|None|
|service.beta.kubernetes.io/alicloud-loadbalancer-region|The region in which Server Load Balancer resides.|None|
|service.beta.kubernetes.io/alicloud-loadbalancer-bandwidth|Server Load Balancer bandwidth.|50|
|service.beta.kubernetes.io/alicloud-loadbalancer-cert-id|ID of a certificate on Alibaba Cloud. You must have uploaded a certificate first.|“”|
|service.beta.kubernetes.io/alicloud-loadbalancer-health-check-flag|The value is on or off.|The default value is off. No need to modify the TCP parameters because TCP enables health check by default and you cannot configure it.|
|service.beta.kubernetes.io/alicloud-loadbalancer-health-check-type|See [HealthCheck](../../../../intl.en-US/Developer Guide/TCP listeners/CreateLoadBalancerTCPListener.md#table_u2n_zrk_cz).|None|
|service.beta.kubernetes.io/alicloud-loadbalancer-health-check-uri|See [HealthCheck](../../../../intl.en-US/Developer Guide/TCP listeners/CreateLoadBalancerTCPListener.md#table_u2n_zrk_cz).|None|
|service.beta.kubernetes.io/alicloud-loadbalancer-health-check-connect-port|See [HealthCheck](../../../../intl.en-US/Developer Guide/TCP listeners/CreateLoadBalancerTCPListener.md#table_u2n_zrk_cz).|None|
|service.beta.kubernetes.io/alicloud-loadbalancer-healthy-threshold|See [HealthCheck](../../../../intl.en-US/Developer Guide/TCP listeners/CreateLoadBalancerTCPListener.md#table_u2n_zrk_cz).|None|
|service.beta.kubernetes.io/alicloud-loadbalancer-unhealthy-threshold|See [HealthCheck](../../../../intl.en-US/Developer Guide/TCP listeners/CreateLoadBalancerTCPListener.md#table_u2n_zrk_cz).|None|
|service.beta.kubernetes.io/alicloud-loadbalancer-health-check-interval|See [HealthCheck](../../../../intl.en-US/Developer Guide/TCP listeners/CreateLoadBalancerTCPListener.md#table_u2n_zrk_cz).|None|
|service.beta.kubernetes.io/alicloud-loadbalancer-health-check-connect-timeout|See [HealthCheck](../../../../intl.en-US/Developer Guide/TCP listeners/CreateLoadBalancerTCPListener.md#table_u2n_zrk_cz).|None|
|service.beta.kubernetes.io/alicloud-loadbalancer-health-check-timeout|See [HealthCheck](../../../../intl.en-US/Developer Guide/TCP listeners/CreateLoadBalancerTCPListener.md#table_u2n_zrk_cz).|None|

