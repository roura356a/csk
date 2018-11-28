# Server Load Balancer {#concept_d1b_y31_ydb .concept}

You can access services by using Alibaba Cloud Server Load Balancer.

## Operate by using command line {#section_pwb_bj1_ydb .section}

1.  Create an Nginx application by using command line.

    ```
    root@master # kubectl run nginx --image=registry.aliyuncs.com/acs/netdia:latest
    root@master # kubectl get po 
    NAME READY STATUS RESTARTS AGE
    nginx-2721357637-dvwq3 1/1 Running 1 6s
    ```

2.  Create Alibaba Cloud Server Load Balancer service for the Nginx application and specify `type=LoadBalancer`  to expose the Nginx service to the Internet.

    ```
    root@master # kubectl expose deployment nginx --port=80 --target-port=80 --type=LoadBalancer
    root@master # kubectl get svc
    NAME CLUSTER-IP EXTERNAL-IP PORT(S) AGE
    nginx 172.19.10.209 101.37.192.20 80:31891/TCP 4s
    ```

3.  Visit `http://101.37.192.20` in the browser to access your Nginx service.

## More information {#section_krn_1j1_ydb .section}

Alibaba Cloud Server Load Balancer also supports parameter configurations such as health check, billing method, and load balancing. For more information, see [Server Load Balancer configuration parameters](#table_csn_1j1_ydb).

## Annotations {#section_lrn_1j1_ydb .section}

Alibaba Cloud supports a lot of Server Load Balancer features by using annotations.

## Use existing intranet Server Load Balancer instance {#section_mrn_1j1_ydb .section}

You must specify two annotations. Replace with your own Server Load Balancer instance ID.

```
apiVersion: v1
kind: Service
metadata:
  annotations:
    service.beta.kubernetes.io/alicloud-loadbalancer-address-type: intranet
    service.beta.kubernetes.io/alicloud-loadbalancer-id: your-loadbalancer-id
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

Save the preceding contents as slb.svc and then run the command `kubectl apply -f slb.svc`.

## Create an HTTPS type Server Load Balancer instance {#section_urn_1j1_ydb .section}

Create a certificate in the Alibaba Cloud console and record the cert-id. Then, use the following annotation to create an HTTPS type Server Load Balancer instance.

```
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
|service.beta.kubernetes.io/alicloud-loadbalancer-region|The region in which Server Load Balancer resides.| None|
|service.beta.kubernetes.io/alicloud-loadbalancer-bandwidth|Server Load Balancer bandwidth.|50|
|service.beta.kubernetes.io/alicloud-loadbalancer-cert-id|ID of a certificate on Alibaba Cloud. You must have uploaded a certificate first.|“”|
|service.beta.kubernetes.io/alicloud-loadbalancer-health-check-flag|The value is on or off.|The default value is off. No need to modify the TCP parameters because TCP enables health check by default and you cannot configure it.|
|service.beta.kubernetes.io/alicloud-loadbalancer-health-check-type|See [HealthCheck](../../../../reseller.en-US/Developer Guide/TCP listener/CreateLoadBalancerTCPListener.md#table_u2n_zrk_cz).| |
|service.beta.kubernetes.io/alicloud-loadbalancer-health-check-uri|See [HealthCheck](../../../../reseller.en-US/Developer Guide/TCP listener/CreateLoadBalancerTCPListener.md#table_u2n_zrk_cz).| |
|service.beta.kubernetes.io/alicloud-loadbalancer-health-check-connect-port|See [HealthCheck](../../../../reseller.en-US/Developer Guide/TCP listener/CreateLoadBalancerTCPListener.md#table_u2n_zrk_cz).| |
|service.beta.kubernetes.io/alicloud-loadbalancer-healthy-threshold|See [HealthCheck](../../../../reseller.en-US/Developer Guide/TCP listener/CreateLoadBalancerTCPListener.md#table_u2n_zrk_cz).| |
|service.beta.kubernetes.io/alicloud-loadbalancer-unhealthy-threshold|See [HealthCheck](../../../../reseller.en-US/Developer Guide/TCP listener/CreateLoadBalancerTCPListener.md#table_u2n_zrk_cz).| |
|service.beta.kubernetes.io/alicloud-loadbalancer-health-check-interval|See [HealthCheck](../../../../reseller.en-US/Developer Guide/TCP listener/CreateLoadBalancerTCPListener.md#table_u2n_zrk_cz).| |
|service.beta.kubernetes.io/alicloud-loadbalancer-health-check-connect-timeout|See [HealthCheck](../../../../reseller.en-US/Developer Guide/TCP listener/CreateLoadBalancerTCPListener.md#table_u2n_zrk_cz).| |
|service.beta.kubernetes.io/alicloud-loadbalancer-health-check-timeout|See [HealthCheck](../../../../reseller.en-US/Developer Guide/TCP listener/CreateLoadBalancerTCPListener.md#table_u2n_zrk_cz).| |

