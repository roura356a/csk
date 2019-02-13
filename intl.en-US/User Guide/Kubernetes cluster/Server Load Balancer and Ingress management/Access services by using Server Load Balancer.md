# Access services by using Server Load Balancer {#concept_sxm_l3s_vdb .concept}

You can access services by using Alibaba Cloud Server Load Balancer \(SLB\).

## Check the cloud-controller-manager version {#section_x5s_2th_mfb .section}

If you specify an existing SLB in a cluster that has a cloud-controller-manager component of v1.9.3 or later versions, the system does not process listeners for this SLB by default. You must manually configure listeners for this SLB.

To view the cloud-controller-manager version, run the following command:

```
root@master # kubectl get po -n kube-system -o yaml|grep image:|grep cloud-con|uniq

  image: registry-vpc.cn-hangzhou.aliyuncs.com/acs/cloud-controller-manager-amd64:v1.9.3
```

## Use a command-line tool {#section_umc_p3s_vdb .section}

Method 1

1.  Create an Nginx application by using a command-line tool.

    ```
    root@master # kubectl run nginx --image=registry.aliyuncs.com/acs/netdia:latest
    root@master # kubectl get po 
    NAME                                   READY     STATUS    RESTARTS   AGE
    nginx-2721357637-dvwq3                 1/1       Running   1          6s
    ```

2.  Create an SLB service for the Nginx application and specify `type=LoadBalancer` to expose the Nginx service to the Internet.

    ```
    root@master # kubectl expose deployment nginx --port=80 --target-port=80 --type=LoadBalancer
    root@master # kubectl get svc
    NAME                  CLUSTER-IP      EXTERNAL-IP      PORT(S)                        AGE
    nginx                 172.19.10.209   101.37.192.20   80:31891/TCP                   4s
    ```

3.  Visit `http://101.37.192.20` in a browser to access your Nginx service.

Method 2

1.  Save the following yml code to the `nginx-svc.yml` file:

    ```
    apiVersion: v1
    kind: Service
    metadata:
      labels:
        run: nignx
      name: nginx-01
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

2.  Run the `kubectl apply -f nginx-svc.yml` command.

    ```
    root@master # kubectl apply -f nginx-svc.yml
    root@master # kubectl get service
    NAME           TYPE           CLUSTER-IP      EXTERNAL-IP      PORT(S)        AGE9d
    ngi-01nx       LoadBalancer   172.19.9.243    101.37.192.129   80:32325/TCP   3h
    ```

3.  Visit `http://101.37.192.129` in a browser to access your Nginx service.

## Use the Kubernetes dashboard {#section_lb5_p3s_vdb .section}

1.  Save the following yml code to the `nginx-svc.yml` file:

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

2.  Log on to the [Container Service console](https://partners-intl.console.aliyun.com/#/cs) and click **Dashboard** on the right of the target cluster.
3.  Click **CREATE** in the upper-right corner to create an application.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16677/15500756159066_en-US.png)

4.  Click the **CREATE FROM FILE** tab. Select the nginx-svc.yml file you saved.
5.  Click **UPLOAD**.

    An SLB instance that points to the created Nginx application is created. The service name is `http-svc`.

6.  In the left-side navigation pane on the dashboard page, select the default namespace, and then click Services.

    You can view the created Nginx service `http-svc` and the SLB address `http://114.55.79.24:80`.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16677/15500756159067_en-US.png) 

7.  Open this address in your browser to access the service.

## Use the Container Service console {#section_bpm_jth_mfb .section}

1.  Log on to the [Container Service console](https://partners-intl.console.aliyun.com/#/cs).
2.  In the left-side navigation pane under Kubernetes, choose **Application** \> **Deployment**.
3.  Select the target cluster and namespace, and then click **Create by Template** in the upper-right corner.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16677/155007561513797_en-US.png)

4.  Select the custom **Resource Type** and then copy the following code to the **Template**.

    ```
    apiVersion: v1
    kind: Service
    metadata:
      labels:
        run: nginx
      name: ngnix
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

5.  Click **DEPLOY**.
6.  Click **Kubernetes Dashboard** to check the deployment progress on the dashboard page.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16677/155007561513798_en-US.png)

    Alternatively, choose **Application** \> **Service** in the left-side navigation pane, and select the target cluster and namespace to view the deployed service.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16677/155007561513800_en-US.png)


## More information {#section_a31_43s_vdb .section}

Alibaba Cloud SLB also supports a lot of parameters such as health checks, billing methods, and SLB types. For more information, see [SLB configuration parameters](reseller.en-US/User Guide/Kubernetes cluster/Server Load Balancer/Access services by using Server Load Balancer.md#table_s31_43s_vdb).

## Annotations {#section_b31_43s_vdb .section}

Alibaba Cloud supports plenty of SLB features by using `annotations`.

**Use an existing intranet SLB instance**

You must specify three annotations. Replace "your-loadbalancer-id" with your SLB instance ID.

**Note:** 

Multiple Kubernetes services can reuse the same SLB instance.

-   The SLB instances created by Kubernetes through a service cannot be reused. Otherwise, the reused SLB instances may be removed incidentally. Only the SLB instances manually created in the console or created by calling API can be reused.
-   The multiple services that reuse the same SLB instance cannot have the same frontend listening port. Otherwise, port conflicts will occur.
-   If you reuse an SLB instance, you must use the listener name and the virtual server group name to martk the SLB instance. Do not modify the listener names or virtual server group names.
-   You can modify SLB instance names.
-   SLB instances cannot be reused across clusters.

```
apiVersion: v1
kind: Service
metadata:
  annotations:
    service.beta.kubernetes.io/alicloud-loadbalancer-address-type: "intranet"
    service.beta.kubernetes.io/alicloud-loadbalancer-id: "your-loadbalancer-id"
    service.beta.kubernetes.io/alicloud-loadbalancer-force-override-listeners: "true" 
  labels:
    run: nginx
  name: nginx
  namespace: default
spec:
  ports:
  - port: 80
    protocol: TCP
    targetPort: 80
  selector:
    run: nginx
  sessionAffinity: None
  type: LoadBalancer
```

**Create an HTTP-type SLB instance**

```
apiVersion: v1
kind: Service
metadata:
  annotations:
    service.beta.kubernetes.io/alicloud-loadbalancer-protocol-port: "http:80"
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

**Create an HTTPS-type SLB instance**

You must first create a certificate in the Alibaba Cloud console before creating an HTTPS-type SLB instance by using the following template \(the certificate ID is required by the annotations in the template\):

```
apiVersion: v1
kind: Service
metadata:
  annotations:
    service.beta.kubernetes.io/alicloud-loadbalancer-cert-id: "your-cert-id"
    service.beta.kubernetes.io/alicloud-loadbalancer-protocol-port: "https:443"
  name: nginx
  namespace: default
spec:
  ports:
  - port: 443
    protocol: TCP
    targetPort: 443
  selector:
    run: nginx
  sessionAffinity: None
  type: LoadBalancer
```

**Limit SLB instance bandwidth**

Only the total bandwidth of the SLB instance can be limited, and all listeners share this bandwidth. For more information, see [共享实例带宽](../../../../../reseller.en-US/User Guide/Listeners/共享实例带宽.md#).

```
apiVersion: v1
kind: Service
metadata:
  annotations:
    service.beta.kubernetes.io/alicloud-loadbalancer-charge-type: "paybybandwidth"
    service.beta.kubernetes.io/alicloud-loadbalancer-bandwidth: "100"
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

**Specify the SLB instance specification**

```
apiVersion: v1
kind: Service
metadata:
  annotations:
    service.beta.kubernetes.io/alicloud-loadbalancer-spec: "slb.s1.small"
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

**Use an existing SLB instance**

By default, listeners are not overridden if you use an existing SLB instance. To forcibly override the existing listeners, set service.beta.kubernetes.io/alicloud-loadbalancer-force-override-listeners to true.

**Note:** 

Multiple Kubernetes services can reuse the same SLB instance.

-   The SLB instances created by Kubernetes through a service cannot be reused. Otherwise, the reused SLB instances may be removed incidentally. Only the SLB instances manually created in the console or created by calling API can be reused.
-   The multiple services that reuse the same SLB instance cannot have the same frontend listening port. Otherwise, port conflicts will occur.
-   If you reuse an SLB instance, you must use the listener name and the virtual server group name to martk the SLB instance. Do not modify the listener names or virtual server group names.
-   You can modify SLB instance names.
-   SLB instances cannot be reused across clusters.

```
apiVersion: v1
kind: Service
metadata:
  annotations:
    service.beta.kubernetes.io/alicloud-loadbalancer-id: "your_loadbalancer_id"
  name: nginx
  namespace: default
spec:
  ports:
  - port: 443
    protocol: TCP
    targetPort: 443
  selector:
    run: nginx
  type: LoadBalancer: LoadBalancer
```

****Use an existing SLB instance and forcibly override existing listeners****

If you forcibly override the existing listeners, they are removed.

**Note:** 

Multiple Kubernetes services can reuse the same SLB instance.

-   The SLB instances created by Kubernetes through a service cannot be reused. Otherwise, the reused SLB instances may be removed incidentally. Only the SLB instances manually created in the console or created by calling API can be reused.
-   The multiple services that reuse the same SLB instance cannot have the same frontend listening port. Otherwise, port conflicts will occur.
-   If you reuse an SLB instance, you must use the listener name and the virtual server group name to martk the SLB instance. Do not modify the listener names or virtual server group names.
-   You can modify SLB instance names.
-   SLB instances cannot be reused across clusters.

```
apiVersion: v1
kind: Service
metadata:
  annotations:
    service.beta.kubernetes.io/alicloud-loadbalancer-id: "your_loadbalancer_id"
    service.beta.kubernetes.io/alicloud-loadbalancer-force-override-listeners: "true"
  name: nginx
  namespace: default
spec:
  ports:
  - port: 443
    protocol: TCP
    targetPort: 443
  selector:
    run: nginx
  type: LoadBalancere: LoadBalancer
```

****Use the Worker node with specified labels as a backend server****

Use a comma \(,\) to separate two labels, for example, K1: V1, K2: V2.

The relationship between multiple labels is `and`.

```
apiVersion: v1
kind: Service
metadata:
  annotations:
    service.beta.kubernetes.io/alicloud-loadbalancer-backend-label: "failure-domain.beta.kubernetes.io/zone:ap-southeast-5a"
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

**Set the session persistence timeout for a TCP-type SLB instance**

The parameter service.beta.kubernetes.io/alicloud-loadbalancer-persistence-tim applies only to TCP listeners.

If the SLB instance is configured with multiple TCP listener ports, this parameter setting applies to all the ports by default.

```
apiVersion: v1
kind: Service
metadata:
  annotations:
    service.beta.kubernetes.io/alicloud-loadbalancer-persistence-timeout: "1800"
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

**Set session persistence for HTTP-type and HTTPS-type SLB instances \(insert cookie\)**

Only HTTP-type and HTTPS-type SLB instances support this setting.

If an instance is configured with multiple HTTP or HTTPS listener ports, the session persistence setting applies to all the HTTP or HTTPS listener ports by default.

```
apiVersion: v1
kind: Service
metadata:
  annotations:
    service.beta.kubernetes.io/alicloud-loadbalancer-sticky-session: "on"
    service.beta.kubernetes.io/alicloud-loadbalancer-sticky-session-type: "insert"
    service.beta.kubernetes.io/alicloud-loadbalancer-cookie-timeout: "1800"
    service.beta.kubernetes.io/alicloud-loadbalancer-protocol-port: "http:80"
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

**Set session persistence for HTTP-type and HTTPS-type SLB instances \(server cookie\)**

Only HTTP-type and HTTPS-type SLB instances support this setting.

If an instance is configured with multiple HTTP or HTTPS listener ports, the session persistence setting applies to all the HTTP or HTTPS listener ports by default.

```
apiVersion: v1
kind: Service
metadata:
  annotations:
    service.beta.kubernetes.io/alicloud-loadbalancer-sticky-session: "on"
    service.beta.kubernetes.io/alicloud-loadbalancer-sticky-session-type: "server"
    service.beta.kubernetes.io/alicloud-loadbalancer-cooyour_cookie: "your_cookie"
    service.beta.kubernetes.io/alicloud-loadbalancer-protocol-port: "http:80"
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

**Specify the primary and secondary zones when creating an SLB instance**

Support for the primary and secondary zones varies according to region, for example, the ap-southeast-5.

Once created, the primary and secondary zones cannot be changed.

```
apiVersion: v1
kind: Service
metadata:
  annotations:
    service.beta.kubernetes.io/alicloud-loadbalancer-master-zoneid: "ap-southeast-5a"
    service.beta.kubernetes.io/alicloud-loadbalancer-slave-zoneid: "ap-southeast-5a"
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

**Use the node where the pod is located as a backend server**

```
apiVersion: v1
kind: Service
metadata:
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

**Note:** The annotations are case sensitive.

|Annotation|Description|Default value|
|----------|-----------|-------------|
|service.beta.kubernetes.io/alicloud-loadbalancer-protocol-port|Use a comma \(,\) to separate two values, for example, https:443,http:80.|None|
|service.beta.kubernetes.io/alicloud-loadbalancer-address-type|Valid values: internet or intranet.|internet|
|service.beta.kubernetes.io/alicloud-loadbalancer-slb-network-type|SLB instance network type. Valid values: classic or vpc.|classic|
|service.beta.kubernetes.io/alicloud-loadbalancer-charge-type|Valid values: paybytraffic or paybybandwidth.|paybytraffic|
|service.beta.kubernetes.io/alicloud-loadbalancer-id|SLB instance ID. You can specify an existing SLB instance by using service.beta.kubernetes.io/alicloud-loadbalancer-id, and existing listeners will be overridden. Note that the SLB instance will not be deleted if you delete the service.|None|
|service.beta.kubernetes.io/alicloud-loadbalancer-backend-label|Use labels to specify the Worker nodes to be mounted to the backend of the SLB instance.|None|
|service.beta.kubernetes.io/alicloud-loadbalancer-spec|SLB instance specification. For more information, see [../../../../../dita-oss-bucket/SP\_23/DNSLB11870158/EN-US\_TP\_4182.md\#](../../../../../reseller.en-US/Developer Guide/Server Load Balancer instances/CreateLoadBalancer.md#).|None|
|service.beta.kubernetes.io/alicloud-loadbalancer-persistence-timeout|Session persistence timeout \(in seconds\).This parameter setting applies only to TCP listeners and the value can be 0 to 3600.

The default value is 0, indicating that the session remains disabled.

For more information, see [../../../../../dita-oss-bucket/SP\_23/DNSLB11870158/EN-US\_TP\_4205.md\#](../../../../../reseller.en-US/Developer Guide/TCP listeners/CreateLoadBalancerTCPListener.md#).

|0|
|service.beta.kubernetes.io/alicloud-loadbalancer-sticky-session|Whether to enable session persistence. Valid value: on | off.**Note:** It applies only to HTTP and HTTPS listeners.

For more information, see [../../../../../dita-oss-bucket/SP\_23/DNSLB11870158/EN-US\_TP\_4203.md\#](../../../../../reseller.en-US/Developer Guide/HTTP listeners/CreateLoadBalancerHTTPListener.md#) and [../../../../../dita-oss-bucket/SP\_23/DNSLB11870158/EN-US\_TP\_4204.md\#](../../../../../reseller.en-US/Developer Guide/HTTPS listeners/CreateLoadBalancerHTTPSListener.md#).

|off|
|service.beta.kubernetes.io/alicloud-loadbalancer-sticky-session-type|Method used to handle the cookie. Valid values:-   insert: Insert the cookie.
-   server: Rewrite the cookie.

**Note:** 

-   It applies only to HTTP and HTTPS listeners.
-   If you set the value of the parameter service.beta.kubernetes.io/alicloud-loadbalancer-sticky-session to on, you must specify this parameter.

For more information, see [../../../../../dita-oss-bucket/SP\_23/DNSLB11870158/EN-US\_TP\_4203.md\#](../../../../../reseller.en-US/Developer Guide/HTTP listeners/CreateLoadBalancerHTTPListener.md#) and [../../../../../dita-oss-bucket/SP\_23/DNSLB11870158/EN-US\_TP\_4204.md\#](../../../../../reseller.en-US/Developer Guide/HTTPS listeners/CreateLoadBalancerHTTPSListener.md#).

|None|
|service.beta.kubernetes.io/alicloud-loadbalancer-cookie-timeout|Cookie timeout period \(in seconds\). Value range: 1 to 86400.**Note:** If the service.beta.kubernetes.io/alicloud-loadbalancer-sticky-session parameter is set to on and the service.beta.kubernetes.io/alicloud-loadbalancer-sticky-session-type parameter is set to insert, this parameter is mandatory.

For more information, see [../../../../../dita-oss-bucket/SP\_23/DNSLB11870158/EN-US\_TP\_4203.md\#](../../../../../reseller.en-US/Developer Guide/HTTP listeners/CreateLoadBalancerHTTPListener.md#) and [../../../../../dita-oss-bucket/SP\_23/DNSLB11870158/EN-US\_TP\_4204.md\#](../../../../../reseller.en-US/Developer Guide/HTTPS listeners/CreateLoadBalancerHTTPSListener.md#).

|None|
|service.beta.kubernetes.io/alicloud-loadbalancer-cookie|Cookie configured on the server.The cookie must be a string of 1 to 200 characters and can only contain ASCII letters and numeric characters. It cannot contain commas \(,\), semicolons \(;\), or spaces, and it cannot start with a dollar sign \($\).

**Note:** 

If the service.beta.kubernetes.io/alicloud-loadbalancer-sticky-session parameter is set to on and the service.beta.kubernetes.io/alicloud-loadbalancer-sticky-session-type parameter is set to server, this parameter is mandatory.

For more information, see [../../../../../dita-oss-bucket/SP\_23/DNSLB11870158/EN-US\_TP\_4203.md\#](../../../../../reseller.en-US/Developer Guide/HTTP listeners/CreateLoadBalancerHTTPListener.md#) and [../../../../../dita-oss-bucket/SP\_23/DNSLB11870158/EN-US\_TP\_4204.md\#](../../../../../reseller.en-US/Developer Guide/HTTPS listeners/CreateLoadBalancerHTTPSListener.md#).

|None|
|service.beta.kubernetes.io/alicloud-loadbalancer-master-zoneid|Zone ID of the primary backend server.|None|
|service.beta.kubernetes.io/alicloud-loadbalancer-slave-zoneid|Zone ID of the secondary backend server.|None|
|externalTrafficPolicy|Nodes that can be used as backend servers. Valid values:-   Cluster: Use all backend nodes as back-end servers.

-   Local: Use the nodes where pods are located as backend servers.


|Cluster|
|service.beta.kubernetes.io/alicloud-loadbalancer-force-override-listeners|Determines whether to override the listeners when you specify an existing SLB instance.|false: Do not override.|
|service.beta.kubernetes.io/alicloud-loadbalancer-region|Region where the SLB instance is located.|None|
|service.beta.kubernetes.io/alicloud-loadbalancer-bandwidth|SLB instance bandwidth.|50|
|service.beta.kubernetes.io/alicloud-loadbalancer-cert-id|ID of a certificate on Alibaba Cloud. You must upload a certificate first.|None|
|service.beta.kubernetes.io/alicloud-loadbalancer-health-check-flag|Valid values: on | off.|The default value is off. Modifying this parameter is not required for TCP, because the health check function is enabled for TCP by default and this parameter cannot be set.|
|service.beta.kubernetes.io/alicloud-loadbalancer-health-check-type|Health check type. Valid values: tcp | http.For more information, see [../../../../../dita-oss-bucket/SP\_23/DNSLB11870158/EN-US\_TP\_4205.md\#](../../../../../reseller.en-US/Developer Guide/TCP listeners/CreateLoadBalancerTCPListener.md#).

|tcp|
|service.beta.kubernetes.io/alicloud-loadbalancer-health-check-uri|URI used for health checks.**Note:** If the health check type is TCP, you do not need to set this parameter.

For more information, see [../../../../../dita-oss-bucket/SP\_23/DNSLB11870158/EN-US\_TP\_4205.md\#](../../../../../reseller.en-US/Developer Guide/TCP listeners/CreateLoadBalancerTCPListener.md#).

|None|
|service.beta.kubernetes.io/alicloud-loadbalancer-health-check-connect-port|Port used for health checks. Valid values:-   -520: The backend port configured for the listener is used by default.
-   1-65535: The port opened on the backend server for health checks is used.

For more information, see [../../../../../dita-oss-bucket/SP\_23/DNSLB11870158/EN-US\_TP\_4205.md\#](../../../../../reseller.en-US/Developer Guide/TCP listeners/CreateLoadBalancerTCPListener.md#).

|None|
|service.beta.kubernetes.io/alicloud-loadbalancer-healthy-threshold| For more information, see [../../../../../dita-oss-bucket/SP\_23/DNSLB11870158/EN-US\_TP\_4205.md\#](../../../../../reseller.en-US/Developer Guide/TCP listeners/CreateLoadBalancerTCPListener.md#).

 |None|
|service.beta.kubernetes.io/alicloud-loadbalancer-unhealthy-threshold|The number of consecutive health check successes before the backend server is determined healthy \(from failure to success\). Value range:2 to 10

For more information, see [../../../../../dita-oss-bucket/SP\_23/DNSLB11870158/EN-US\_TP\_4205.md\#](../../../../../reseller.en-US/Developer Guide/TCP listeners/CreateLoadBalancerTCPListener.md#).

|None|
|service.beta.kubernetes.io/alicloud-loadbalancer-health-check-interval| Time interval between two consecutive health checks \(seconds\).

 Value range: 1 to 50.

 For more information, see [../../../../../dita-oss-bucket/SP\_23/DNSLB11870158/EN-US\_TP\_4205.md\#](../../../../../reseller.en-US/Developer Guide/TCP listeners/CreateLoadBalancerTCPListener.md#).

 |None|
|service.beta.kubernetes.io/alicloud-loadbalancer-health-check-connect-timeout| Time period required by waiting for a health check response \(in seconds\). If the backend ECS instance does not send a valid response within a specified period of time, the system determines that the health check has failed.

 Value range: 1 to 300.

 **Note:** If the value of the parameter service.beta.kubernetes.io/alicloud-loadbalancer-health-check-connect-timeout is less than the value of the parameter service.beta.kubernetes.io/alicloud-loadbalancer-health-check-interval, service.beta.kubernetes.io/alicloud-loadbalancer-health-check-connect-timeout is invalid and the timeout period equals the value of service.beta.kubernetes.io/alicloud-loadbalancer-health-check-interval.

 For more information, see [../../../../../dita-oss-bucket/SP\_23/DNSLB11870158/EN-US\_TP\_4205.md\#](../../../../../reseller.en-US/Developer Guide/TCP listeners/CreateLoadBalancerTCPListener.md#).

 |None|
|service.beta.kubernetes.io/alicloud-loadbalancer-health-check-timeout|Time period required by waiting for a health check response \(in seconds\). If the backend ECS instance does not send a valid response within a specified period of time, the system determines that the health check has failed.Value range: 1 to 300.

**Note:** If the value of the parameter service.beta.kubernetes.io/alicloud-loadbalancer-health-check-timeout is less than that of the parameter service.beta.kubernetes.io/alicloud-loadbalancer-health-check-interval, service.beta.kubernetes.io/alicloud-loadbalancer-health-check-timeout is invalid, and the timeout period equals the value of the parameter service.beta.kubernetes.io/alicloud-loadbalancer-health-check-interval.

For more information, see [../../../../../dita-oss-bucket/SP\_23/DNSLB11870158/EN-US\_TP\_4203.md\#](../../../../../reseller.en-US/Developer Guide/HTTP listeners/CreateLoadBalancerHTTPListener.md#).

|None|

