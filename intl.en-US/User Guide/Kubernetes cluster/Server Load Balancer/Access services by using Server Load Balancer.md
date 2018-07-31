# Access services by using Server Load Balancer {#concept_sxm_l3s_vdb .concept}

You can access services by using Alibaba Cloud Server Load Balancer.

## Operate by using command lines {#section_umc_p3s_vdb .section}

1.  Create an Nginx application by using command lines.

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

## Operate by using Kubernetes dashboard {#section_lb5_p3s_vdb .section}

1.  Save the following yml codes to the `nginx-svc.yml` file.

    ```
    apiVersion: v1
    kind: Service
    metadata:
      labels:
        run: nginx
      name: http-svc
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

2.  Log on to the [Container Service console](https://cs.console.aliyun.com). Under Kubernetes, click Clusters in the left-side navigation pane. Click **Dashboard** at the right of the cluster to enter the Kubernetes dashboard.
3.  Click **CREATE** in the upper-right corner to create an application.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/6930/15330249254623_en-US.png)

4.  The Resource creation page appears. Click the **CREATE FROM FILE** tab and then upload the nginx-svc.yml file you saved.
5.  Click **UPLOAD**.

    An Alibaba Cloud Server Load Balancer instance that points to the created Nginx application is created. The service name is `http-svc`.

6.  Select default under Namespace in the left-side navigation pane. Click Services in the left-side navigation pane.

    You can view the created Nginx service `http-svc` and the Server Load Balancer address `http://120.55.179.145:80`.

     ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/6930/15330249264625_en-US.png) 

7.  Copy the address to the browser to access the service.

## More information {#section_a31_43s_vdb .section}

Alibaba Cloud Server Load Balancer also supports parameter configurations such as health check, billing method, and load balancing type. For more information, see [Server Load Balancer configuration parameters](intl.en-US/User Guide/Kubernetes cluster/Server Load Balancer/Access services by using Server Load Balancer.md#table_s31_43s_vdb).

## Annotations {#section_b31_43s_vdb .section}

Alibaba Cloud supports a plenty of Server Load Balancer features by using annotations.

**Use existing intranet Server Load Balancer instance**

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

**Create an HTTPS type Server Load Balancer instance**

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
|service.beta.kubernetes.io/alicloud-loadbalancer-slb-network-type|Server Load Balancer network type. The value is classic or VPC.|classic|
|service.beta.kubernetes.io/alicloud-loadbalancer-charge-type|The value is paybytraffic or paybybandwidth.|paybybandwidth|
|service.beta.kubernetes.io/alicloud-loadbalancer-id|The Server Load Balancer instance ID. Use loadbalancer-id to specify an existing Server Load Balancer instance. The existing listener will be overwritten. The Server Load Balancer instance will not be deleted when the service is deleted.|None|
|service.beta.kubernetes.io/alicloud-loadbalancer-backend-label|Use label to specify what nodes are mounted to the Server Load Balancer backend.| None|
|service.beta.kubernetes.io/alicloud-loadbalancer-region|The region in which Server Load Balancer resides.| None|
|service.beta.kubernetes.io/alicloud-loadbalancer-bandwidth|Server Load Balancer bandwidth.|50|
|service.beta.kubernetes.io/alicloud-loadbalancer-cert-id|Authentication ID on Alibaba Cloud. Upload the certificate first.|“”|
|service.beta.kubernetes.io/alicloud-loadbalancer-health-check-flag|The value is on or off.|The default value is off. No need to modify the TCP parameters because TCP enables health check by default and you cannot configure it.|
|service.beta.kubernetes.io/alicloud-loadbalancer-health-check-type|See [HealthCheck](https://help.aliyun.com/document_detail/27594.html).| |
|service.beta.kubernetes.io/alicloud-loadbalancer-health-check-uri|See [HealthCheck](https://www.alibabacloud.com/help/zh/doc-detail/27594.htm).| |
|service.beta.kubernetes.io/alicloud-loadbalancer-health-check-connect-port|See [HealthCheck](https://www.alibabacloud.com/help/zh/doc-detail/27594.htm).| |
|service.beta.kubernetes.io/alicloud-loadbalancer-healthy-threshold|See [HealthCheck](https://www.alibabacloud.com/help/zh/doc-detail/27594.htm).| |
|service.beta.kubernetes.io/alicloud-loadbalancer-unhealthy-threshold|See [HealthCheck](https://www.alibabacloud.com/help/zh/doc-detail/27594.htm).| |
|service.beta.kubernetes.io/alicloud-loadbalancer-health-check-interval|See [HealthCheck](https://www.alibabacloud.com/help/zh/doc-detail/27594.htm).| |
|service.beta.kubernetes.io/alicloud-loadbalancer-health-check-connect-timeout|See [HealthCheck](https://www.alibabacloud.com/help/zh/doc-detail/27594.htm).| |
|service.beta.kubernetes.io/alicloud-loadbalancer-health-check-timeout|See [HealthCheck](https://www.alibabacloud.com/help/zh/doc-detail/27594.htm).| |

