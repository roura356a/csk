# Access services by using SLB {#task_1425948 .task}

You can access services by using Alibaba Cloud Server Load Balancer \(SLB\).

If you specify an existing SLB instance for your cluster that uses Cloud Controller Manager \(CCM\) of V1.9.3 or later, CCM will not configure listeners for this SLB instance. You can set the `service.beta.kubernetes.io/alicloud-loadbalancer-force-override-listeners` annotation to true to enable CCM to configure listeners for this SLB instance. Alternatively, you can manually configure listeners for this SLB instance.

You can run the following command to query the CCM version:

``` {#codeblock_agw_mjd_s5c}
root@master # kubectl get po -n kube-system -o yaml|grep image:|grep cloud-con|uniq

  image: registry-vpc.cn-hangzhou.aliyuncs.com/acs/cloud-controller-manager-amd64:v1.9.3
```

## Note {#section_0iv_2kv_rv0 .section}

-   If the type of a service is `LoadBalancer`, CCM creates or configures an SLB instance for the service, including resources such as the **SLB instance**, **listener**, and **VServer group**.
-   If the type of a service is not LoadBalancer, CCM will not configure an SLB instance for the service. Especially, if you change the type setting of a service from `type=LoadBalancer` to `type! =LoadBalancer`, CCM deletes the SLB instance that has been created for the service. If the SLB instance is an existing SLB instance specified through the `service.beta.kubernetes.io/alicloud-loadbalancer-id` annotation, CCM will not delete the SLB instance.
-   Automatic configuration update: CCM uses a declarative API and automatically updates the configuration of an SLB instance according to the service configuration under certain conditions. If you modify the configuration of the SLB instance in the SLB console, the modification may be overwritten when CCM updates the configuration. The modification is not overwritten only in the scenario where the SLB instance is an existing SLB instance specified by you and the service.beta.kubernetes.io/alicloud-loadbalancer-force-override-listeners annotation is set to false. Do not modify the configuration of an SLB instance created and maintained by CCM in the SLB console.
-   You can specify an existing SLB instance for a service, or enable CCM to create an SLB instance for a service. The SLB instances specified or created in the two methods are managed differently.

    Existing SLB instance

    -   You can set the `service.beta.kubernetes.io/alibaba-cloud-loadbalancer-id` annotation to specify an existing SLB instance for a service.
    -   SLB instance configuration: CCM uses this SLB instance and configures it based on other annotations. CCM creates multiple VServer groups for this SLB instance. When the nodes of your cluster change, CCM updates the nodes in the VServer groups accordingly.
    -   Listener configuration: Whether CCM configures listeners for this SLB instance depends on the setting of the `service.beta.kubernetes.io/alicloud-loadbalancer-force-override-listeners` annotation. If this annotation is set to false, CCM will not configure listeners for this SLB instance. If this annotation is set to true, CCM tries to update listeners for this SLB instance. CCM checks whether a listener of the SLB instance is one managed by Kubernetes based on the name of the listener. If the listener name is in the format of k8s/Port/ServiceName/Namespace/ClusterID, the listener is managed by Kubernetes. If the listening port declared by the service conflicts with an existing listening port, CCM reports an error.
    -   SLB instance deletion: When the service is deleted, CCM will not delete the existing SLB instance specified through the service.beta.kubernetes.io/alibaba-cloud-loadbalancer-id annotation.
    SLB instance managed by CCM

    -   CCM can automatically create and configure resources such as the **SLB instance**, **listener** and **VServer groups** for a service based on the service configuration. All these resources are managed by CCM. Do not manually modify the configurations of these resources in the SLB console. If the configurations are modified, CCM will restore the configurations to those declared by the service at the next reconciliation, which causes unexpected results.
    -   SLB instance deletion: When the service is deleted, CCM deletes the SLB instance.
-   Backend server update
    -   CCM automatically updates the backend VServer group for the SLB instance of a service. When the backend endpoint of the service changes or nodes in the cluster change, CCM updates the backend servers accordingly.
    -   If `spec.externalTrafficPoilcy` is set to Cluster for a service, CCM adds all nodes of the cluster as backend servers of the SLB instance. CCM will not do so if the backend servers have been specified for the service through backend labels. SLB limits the number of SLB instances to which an Elastic Compute Service \(ECS\) instance can attach. This quota is consumed quickly if CCM adds backend servers in the preceding way. When the quota is used up, service reconciliation fails. To resolve this issue, you can set spec.ExternalTrafficPoilcy to Local for a service.
    -   If `spec.externalTrafficPoilcy` is set to Local for a service, CCM adds only the nodes where the pods corresponding to the service are located as backend servers to the SLB instance. This significantly reduces the consumption speed of the quota. In addition, Layer-4 source IP addresses can be retained in this mode.
    -   Under no circumstances will CCM add the master node of the cluster as a backend server to the SLB instance.
    -   If any node is drained or marked as unschedulable through the kubectl drain or kubectl cordon command, CCM removes it as a backend server from the SLB instance.

## Use the command line {#section_td0_1qf_nnc .section}

Method 1:

1.  Create an NGINX application on the command line. 

    ``` {#codeblock_92a_nwl_hhe}
    root@master # kubectl run nginx --image=registry.aliyuncs.com/acs/netdia:latest
    root@master # kubectl get po 
    NAME                                   READY     STATUS    RESTARTS   AGE
    nginx-2721357637-dvwq3                 1/1       Running   1          6s
    ```

2.  Create a service for the NGINX application and specify `type=LoadBalancer` to expose the NGINX service to the public network through an SLB instance. 

    ``` {#codeblock_qp1_5hu_2k3}
    root@master # kubectl expose deployment nginx --port=80 --target-port=80 --type=LoadBalancer
    root@master # kubectl get svc
    NAME                  CLUSTER-IP      EXTERNAL-IP      PORT(S)                        AGE
    nginx                 172.19.XX.XX    101.37.XX.XX     80:31891/TCP                   4s
    ```

3.  Visit `http://101.37.XX.XX` in a browser to access your NGINX service.

Method 2:

1.  Copy the following YAML code to the `nginx-svc.yml` file and save the file: 

    ``` {#codeblock_o3d_fcy_zf9}
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

2.  Run the following command to create an NGINX application: 

    ``` {#codeblock_gek_c9f_gdh}
    kubectl apply -f nginx-svc.yml
    ```

3.  Run the following command to expose the NGINX service to the public network: 

    ``` {#codeblock_vrc_n9a_8ua}
    root@master # kubectl get service
    NAME           TYPE           CLUSTER-IP      EXTERNAL-IP      PORT(S)        AGE9d
    ngi-01nx       LoadBalancer   172.19.XX.XX    101.37.XX.XX     80:32325/TCP   3h
    ```

4.  Visit `http://101.37.XX.XX` in a browser to access your NGINX service.

## Use the Kubernetes dashboard {#section_9pb_73z_j9u .section}

1.  Copy the following YAML code to the `nginx-svc.yml` file and save the file: 

    ``` {#codeblock_e3u_5pt_uw8}
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

2.  Log on to the [Container Service console](https://partners-intl.console.aliyun.com/#/cs) and click **Dashboard** on the right of the target cluster. The Kubernetes dashboard page appears.
3.  Click **CREATE** in the upper-right corner to create an application. 

    ![Create an application](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16677/15686067159066_en-US.png)

4.  Click the **CREATE FROM FILE** tab. Select the nginx-svc.yml file you saved.
5.  Click **UPLOAD**. An NGINX service of the LoadBalancer type is created for the NGINX application.The service name is `http-svc`.
6.  On the Kubernetes dashboard page, select the default namespace, and then click Services. You can find the NGINX service `http-svc`. Its public endpoint is `http://114.55.XX.XX:80`.

    ![Access services](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16677/15686067159067_en-US.png)

7.  Visit the endpoint in a browser to access the NGINX service.

## Use the Container Service console {#section_jcf_02c_7om .section}

1.  Log on to the [Container Service console](https://partners-intl.console.aliyun.com/#/cs).
2.  In the left-side navigation pane under Container Service - Kubernetes, choose **Applications** \> **Deployments**. The Deployments page appears.
3.  Select the target cluster and namespace, and click **Create from Template** in the upper-right corner. 

    ![Create an application](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16677/156860671513797_en-US.png)

4.  Select Custom in **Sample Template** and copy the following code to the **Template** section: 

    ``` {#codeblock_z6q_bix_jre}
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

5.  Click **Create**.
6.  Click **Kubernetes Dashboard** to check the creation progress on the Kubernetes dashboard page. 

    ![Kubernetes dashboard](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16677/156860671813798_en-US.png)

7.  Alternatively, choose **Ingresses and Load Balancing** \> **Services** in the left-side navigation pane, and select the target cluster and namespace to check the deployed service. 

    ![Deploy a service](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16677/156860672113800_en-US.png)


## More information {#section_jnq_v5w_nla .section}

Alibaba Cloud SLB supports a variety of parameters for you to configure, such as the health check, billing method, and SLB instance type. For more information, see [SLB configuration parameters](reseller.en-US/User Guide/Kubernetes cluster/Server Load Balancer/Access services by using Server Load Balancer.md#table_s31_43s_vdb).

## Annotations {#section_75c_ujo_g9c .section}

Alibaba Cloud allows you to use SLB features by specifying `annotations`.

-   Create a public SLB instance

    ``` {#codeblock_06o_6ob_zzd}
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

    ``` {#codeblock_hhp_pp9_mw4}
    apiVersion: v1
    kind: Service
    metadata:
      annotations:
        service.beta.kubernetes.io/alicloud-loadbalancer-address-type: "intranet"
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

-   Create an HTTP-type SLB instance

    ``` {#codeblock_a4r_eds_ecj}
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

-   Create an HTTPS-type SLB instance

    Create a certificate in the Alibaba Cloud console and record the certificate ID. Then create an HTTPS-type SLB instance by specifying the required annotations.

    ``` {#codeblock_7te_4ag_g7n}
    apiVersion: v1
    kind: Service
    metadata:
      annotations:
        service.beta.kubernetes.io/alicloud-loadbalancer-protocol-port: "https:443"
        service.beta.kubernetes.io/alicloud-loadbalancer-cert-id: "${YOUR_CERT_ID}"
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

-   Limit the bandwidth of an SLB instance

    ``` {#codeblock_zqm_ct2_x65}
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

-   Specify the SLB instance specification

    For more information about SLB instance specifications, see [CreateLoadBalancer](../../../../reseller.en-US/Developer Guide/Server Load Balancer (SLB) instances/CreateLoadBalancer.md#).

    ``` {#codeblock_e8t_74y_rtz}
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

-   Use an existing SLB instance

    -   By default, listeners of an existing SLB instance are not overwritten. To forcibly overwrite listeners of an existing SLB instance, set `service.beta.kubernetes.io/alicloud-loadbalancer-force-override-listeners` to true.
    -   Currently, you cannot attach additional tags to an existing SLB instance by using the `service.beta.kubernetes.io/alibaba-cloud-loadbalancer-additional-resource-tags` annotation.
    ``` {#codeblock_03f_moe_yx1}
    apiVersion: v1
    kind: Service
    metadata:
      annotations:
        service.beta.kubernetes.io/alicloud-loadbalancer-id: "${YOUR_LOADBALACER_ID}"
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

    If you choose to forcibly overwrite listeners of an existing SLB instance, all existing listeners of the SLB instance are deleted.

    ``` {#codeblock_z43_dsq_fv1}
    apiVersion: v1
    kind: Service
    metadata:
      annotations:
        service.beta.kubernetes.io/alicloud-loadbalancer-id: "${YOUR_LOADBALACER_ID}"
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

-   Use backend labels to specify the worker nodes to be used as backend servers

    Separate multiple labels with commas \(,\). Example: "k1=v1,k2=v2". A node must meet all the specified labels to be added as a backend server.

    ``` {#codeblock_io4_ggi_prz}
    apiVersion: v1
    kind: Service
    metadata:
      annotations:
        service.beta.kubernetes.io/alicloud-loadbalancer-backend-label: "failure-domain.beta.kubernetes.io/zone=ap-southeast-5a"
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

-   Set the session persistence time for a TCP-type SLB instance

    -   The `service.beta.kubernetes.io/alicloud-loadbalancer-persistence-time` annotation applies only to TCP listeners.
    -   If an SLB instance has multiple TCP listeners, the setting takes effect on all the TCP listeners by default.
    ``` {#codeblock_7d4_d5o_2hy}
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

-   Configure session persistence based on cookie inserting for an HTTP-type or HTTPS-type SLB instance

    -   Only HTTP-type and HTTPS-type SLB instances support this setting.
    -   If an SLB instance has multiple HTTP or HTTPS listeners, the setting takes effect on all the HTTP or HTTPS listeners by default.
    -   All the annotations specified in the following example are required to configure session persistence based on cookie inserting.
    ``` {#codeblock_q9z_594_lce}
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

-   Configure session persistence based on the server cookie for an HTTP-type or HTTPS-type SLB instance

    -   Only HTTP-type and HTTPS-type SLB instances support this setting.
    -   If an SLB instance has multiple HTTP or HTTPS listeners, the setting takes effect on all the HTTP or HTTPS listeners by default.
    -   All the annotations specified in the following example are required to configure session persistence based on the server cookie.
    -   The cookie specified by the service.beta.kubernetes.io/alicloud-loadbalancer-cookie annotation can contain only letters, digits, underscores \(\_\), and hyphens \(-\).
    ``` {#codeblock_66o_vyu_lgv}
    apiVersion: v1
    kind: Service
    metadata:
      annotations:
        service.beta.kubernetes.io/alicloud-loadbalancer-sticky-session: "on"
        service.beta.kubernetes.io/alicloud-loadbalancer-sticky-session-type: "server"
        service.beta.kubernetes.io/alicloud-loadbalancer-cookie: "${YOUR_COOKIE}"
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

-   Specify the primary and secondary zones when creating an SLB instance

    -   SLB instances in some regions do not support primary and secondary zones, such as ap-southeast-5.
    -   Once created, the primary and secondary zones cannot be changed.
    ``` {#codeblock_124_g1h_4j8}
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

-   Use the nodes where pods are located as backend servers

    By default, `externalTrafficPolicy` is set to Cluster for a service. In Cluster mode, all nodes in the cluster are used as backend servers. You can set externalTrafficPolicy to Local for a service so that only nodes where pods are located are used as backend servers.

    ``` {#codeblock_5w9_3i9_7a0}
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

-   Create an SLB instance of the Virtual Private Cloud \(VPC\) network type

    -   All the annotations specified in the following example are required to create an SLB instance of the VPC network type.
    -   Internal SLB instances support the VPC and classic network types. For more information, see [SLB instance overview](../../../../reseller.en-US/Instance/SLB instance overview.md#).
    ``` {#codeblock_eyl_08w_ay7}
    apiVersion: v1
    kind: Service
    metadata:
      annotations:
        service.beta.kubernetes.io/alicloud-loadbalancer-address-type: "intranet"
        service.beta.kubernetes.io/alicloud-loadbalancer-network-type: "vpc"
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

-   Create an SLB instance that is charged by bandwidth

    -   Only public SLB instances support billing by bandwidth.
    -   All the annotations specified in the following example are required to create an SLB instance that is charged by bandwidth.
    ``` {#codeblock_eqb_8pp_xiv}
    apiVersion: v1
    kind: Service
    metadata:
      annotations:
        service.beta.kubernetes.io/alicloud-loadbalancer-bandwidth: "45"
        service.beta.kubernetes.io/alicloud-loadbalancer-charge-type: "paybybandwidth"
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

-   Create an SLB instance with the health check feature enabled
    -   Create a TCP-type SLB instance with the health check feature enabled

        -   By default, the health check feature is enabled for TCP listeners and cannot be disabled. That is, the `service.beta.kubernetes.io/alicloud-loadbalancer-health-check-flag` annotation is invalid.
        -   All the annotations specified in the following example are required to create a TCP-type SLB instance with the health check feature enabled.
        ``` {#codeblock_49r_i4b_hpu}
        apiVersion: v1
        kind: Service
        metadata:
          annotations:
            service.beta.kubernetes.io/alicloud-loadbalancer-health-check-type: "tcp"
            service.beta.kubernetes.io/alicloud-loadbalancer-health-check-connect-timeout: "8"
            service.beta.kubernetes.io/alicloud-loadbalancer-healthy-threshold: "4"
            service.beta.kubernetes.io/alicloud-loadbalancer-unhealthy-threshold: "4"
            service.beta.kubernetes.io/alicloud-loadbalancer-health-check-interval: "3"
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

    -   Create an HTTP-type SLB instance with the health check feature enabled

        All the annotations specified in the following example are required to create an HTTP-type SLB instance with the health check feature enabled.

        ``` {#codeblock_z1w_ob3_lm0}
        apiVersion: v1
        kind: Service
        metadata:
          annotations:
            service.beta.kubernetes.io/alicloud-loadbalancer-health-check-flag: "on"
            service.beta.kubernetes.io/alicloud-loadbalancer-health-check-type: "http"
            service.beta.kubernetes.io/alicloud-loadbalancer-health-check-uri: "/test/index.html"
            service.beta.kubernetes.io/alicloud-loadbalancer-healthy-threshold: "4"
            service.beta.kubernetes.io/alicloud-loadbalancer-unhealthy-threshold: "4"
            service.beta.kubernetes.io/alicloud-loadbalancer-health-check-timeout: "10"
            service.beta.kubernetes.io/alicloud-loadbalancer-health-check-interval: "3"
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

-   Specify the scheduling algorithm for an SLB instance

    -   wrr \(default\): Backend servers with higher weights receive more requests than those with lower weights.
    -   wlc: Requests are forwarded based on the weight and load \(the number of connections\) of each backend server. If the weights are the same, backend servers with fewer connections receive more requests than those with more connections.
    -   rr: Requests are sequentially distributed to backend servers.
    ``` {#codeblock_yrx_skr_fez}
    apiVersion: v1
    kind: Service
    metadata:
      annotations:
        service.beta.kubernetes.io/alicloud-loadbalancer-scheduler: "wlc"
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

-   Create an SLB instance with the access control feature enabled

    -   Create an access control list \(ACL\) in the Alibaba Cloud console and record the ACL ID. Then create an SLB instance with the access control feature enabled by specifying the required annotations.
    -   A whitelist allows only access from specific IP addresses, while a blacklist restricts only access from specific IP addresses.
    -   All the annotations specified in the following example are required to create an SLB instance with the access control feature enabled.
    ``` {#codeblock_u1l_mvx_vw3}
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

-   Specify a VSwitch for an SLB instance

    -   Search for the ID of a VSwitch in the Alibaba Cloud VPC console. Then specify the VSwitch for an SLB instance by using the required annotations.
    -   All the annotations specified in the following example are required to specify a VSwitch for an SLB instance.
    ``` {#codeblock_vlr_0jn_7kn}
    apiVersion: v1
    kind: Service
    metadata:
      annotations:
       service.beta.kubernetes.io/alicloud-loadbalancer-address-type: "intranet"
       service.beta.kubernetes.io/alicloud-loadbalancer-vswitch-id: "${YOUR_VSWITCH_ID}"
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
    -   To configure port forwarding, you need to first create a certificate in the Alibaba Cloud console and record the certificate ID.
    -   All the annotations specified in the following example are required to configure port forwarding for an SLB instance.
    ``` {#codeblock_cdm_1az_99w}
    apiVersion: v1
    kind: Service
    metadata:
      annotations:
        service.beta.kubernetes.io/alicloud-loadbalancer-protocol-port: "https:443,http:80"
        service.beta.kubernetes.io/alicloud-loadbalancer-cert-id: "${YOUR_CERT_ID}"
        service.beta.kubernetes.io/alicloud-loadbalancer-forward-port: "80:443"
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

-   Attach additional tags to an SLB instance

    Separate multiple tags with commas \(,\), for example, "k1=v1,k2=v2".

    ``` {#codeblock_ru0_j41_tcf}
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


**Note:** The values of the annotations are case sensitive.

|Annotation|Type|Description|Default value|
|----------|----|-----------|-------------|
|service.beta.kubernetes.io/alicloud-loadbalancer-protocol-port|String|The listening port. Separate multiple ports with commas \(,\), for example, https:443,http:80.|None|
|service.beta.kubernetes.io/alicloud-loadbalancer-address-type|String|The type of the SLB instance. Valid values: internet and intranet.|internet|
|service.beta.kubernetes.io/alicloud-loadbalancer-slb-network-type|String|The network type of the SLB instance. Valid values: classic and vpc. Set the `service.beta.kubernetes.io/alicloud-loadbalancer-address-type` annotation to intranet if you need to select vpc as the network type.

 |classic|
|service.beta.kubernetes.io/alicloud-loadbalancer-charge-type|String|The billing method of the SLB instance. Valid values: paybytraffic and paybybandwidth.|paybytraffic|
|service.beta.kubernetes.io/alicloud-loadbalancer-id|String|The ID of the SLB instance. You can specify an existing SLB instance by setting the service.beta.kubernetes.io/alicloud-loadbalancer-id annotation. If you specify an existing SLB instance, its listeners are not overwritten by default. To forcibly overwrite the listeners of an existing SLB instance, set the service.beta.kubernetes.io/alicloud-loadbalancer-force-override-listeners annotation to true.|None|
|service.beta.kubernetes.io/alicloud-loadbalancer-backend-label|String|The labels for specifying the worker nodes to be added as backend servers of the SLB instance.|None|
|service.beta.kubernetes.io/alicloud-loadbalancer-spec|String|The specification of the SLB instance. For more information, see [CreateLoadBalancer](../../../../reseller.en-US/Developer Guide/Server Load Balancer (SLB) instances/CreateLoadBalancer.md#)|None|
|service.beta.kubernetes.io/alicloud-loadbalancer-persistence-timeout|String|The session persistence time. Unit: seconds. This annotation applies only to TCP listeners. Valid values: 0 to 3600.

 The default value is 0, indicating that session persistence is disabled.

 For more information, see [CreateLoadBalancerTCPListener](../../../../reseller.en-US/Developer Guide/TCP listeners/CreateLoadBalancerTCPListener.md#)

 |0|
|service.beta.kubernetes.io/alicloud-loadbalancer-sticky-session|String|Specifies whether to enable session persistence. Valid values: on and off. **Note:** This annotation applies only to HTTP and HTTPS listeners.

 For more information, see [CreateLoadBalancerHTTPListener](../../../../reseller.en-US/Developer Guide/HTTP listeners/CreateLoadBalancerHTTPListener.md#) and [CreateLoadBalancerHTTPSListener](../../../../reseller.en-US/Developer Guide/HTTPS listeners/CreateLoadBalancerHTTPSListener.md#)

 |off|
|service.beta.kubernetes.io/alicloud-loadbalancer-sticky-session-type|String|The method for processing cookies. Valid values: -   insert: inserts a cookie.
-   server: rewrites a cookie.

 **Note:** 

-   This annotation applies only to HTTP and HTTPS listeners.
-   You must specify this annotation if the service.beta.kubernetes.io/alicloud-loadbalancer-sticky-session annotation is set to on.

For more information, see [CreateLoadBalancerHTTPListener](../../../../reseller.en-US/Developer Guide/HTTP listeners/CreateLoadBalancerHTTPListener.md#) and [CreateLoadBalancerHTTPSListener](../../../../reseller.en-US/Developer Guide/HTTPS listeners/CreateLoadBalancerHTTPSListener.md#)

 |None|
|service.beta.kubernetes.io/alicloud-loadbalancer-cookie-timeout|String|The cookie timeout period. Unit: seconds. Valid values: 1 to 86400. **Note:** You must specify this annotation if the service.beta.kubernetes.io/alicloud-loadbalancer-sticky-session annotation is set to on and the service.beta.kubernetes.io/alicloud-loadbalancer-sticky-session-type annotation is set to insert.

 For more information, see [CreateLoadBalancerHTTPListener](../../../../reseller.en-US/Developer Guide/HTTP listeners/CreateLoadBalancerHTTPListener.md#) and [CreateLoadBalancerHTTPSListener](../../../../reseller.en-US/Developer Guide/HTTPS listeners/CreateLoadBalancerHTTPSListener.md#)

 |None|
|service.beta.kubernetes.io/alicloud-loadbalancer-cookie|String|The cookie configured on the backend server. The cookie must be 1 to 200 characters in length. It can contain only ASCII letters and digits. It cannot contain commas \(,\), semicolons \(;\), or spaces. It cannot start with a dollar sign \($\).

 **Note:** 

You must specify this annotation if the service.beta.kubernetes.io/alicloud-loadbalancer-sticky-session annotation is set to on and the service.beta.kubernetes.io/alicloud-loadbalancer-sticky-session-type annotation is set to server.

 For more information, see [CreateLoadBalancerHTTPListener](../../../../reseller.en-US/Developer Guide/HTTP listeners/CreateLoadBalancerHTTPListener.md#) and [CreateLoadBalancerHTTPSListener](../../../../reseller.en-US/Developer Guide/HTTPS listeners/CreateLoadBalancerHTTPSListener.md#)

 |None|
|service.beta.kubernetes.io/alicloud-loadbalancer-master-zoneid|String|The ID of the primary zone of backend servers.|None|
|service.beta.kubernetes.io/alicloud-loadbalancer-slave-zoneid|String|The ID of the secondary zone of backend servers.|None|
|externalTrafficPolicy|String|The policy for adding nodes as backend servers. Valid values: -   Cluster: adds all nodes as backend servers.
-   Local: adds the nodes where pods are located as backend servers.

 |Cluster|
|service.beta.kubernetes.io/alicloud-loadbalancer-force-override-listeners|String|Specifies whether to overwrite the listeners of an existing SLB instance that is specified.|false: The listeners of the existing SLB instance are not overwritten.|
|service.beta.kubernetes.io/alicloud-loadbalancer-bandwidth|String|The bandwidth of the SLB instance. This annotation applies only to public SLB instances.|50|
|service.beta.kubernetes.io/alicloud-loadbalancer-cert-id|String|The ID of the certificate in Alibaba Cloud. You must upload a certificate first.|None|
|service.beta.kubernetes.io/alicloud-loadbalancer-health-check-flag|String|Specifies whether to enable the health check feature. Valid values: on and off. -   The default value is on for TCP listeners, and cannot be changed.
-   The default value is off for HTTP listeners.

 |off. This annotation is not required for TCP listeners. By default, the health check feature is enabled for TCP listeners and cannot be disabled.|
|service.beta.kubernetes.io/alicloud-loadbalancer-health-check-type|String|The type of health checks. Valid values: tcp and http. For more information, see [CreateLoadBalancerTCPListener](../../../../reseller.en-US/Developer Guide/TCP listeners/CreateLoadBalancerTCPListener.md#)

 |tcp|
|service.beta.kubernetes.io/alicloud-loadbalancer-health-check-uri|String|The URI used for health checks. **Note:** This annotation is not required when the type of health checks is set to TCP.

 For more information, see [CreateLoadBalancerTCPListener](../../../../reseller.en-US/Developer Guide/TCP listeners/CreateLoadBalancerTCPListener.md#)

 |None|
|service.beta.kubernetes.io/alicloud-loadbalancer-health-check-connect-port|String|The port number used for health checks. Valid values: -   -520: uses the backend port number configured for the listener.
-   1-65535: the backend port number used for health checks.

 For more information, see [CreateLoadBalancerTCPListener](../../../../reseller.en-US/Developer Guide/TCP listeners/CreateLoadBalancerTCPListener.md#)

 |None|
|service.beta.kubernetes.io/alicloud-loadbalancer-healthy-threshold|String|The number of consecutive health check successes before the backend server is declared to be healthy \(from fail to success\). Valid values: 2 to 10.

 For more information, see [CreateLoadBalancerTCPListener](../../../../reseller.en-US/Developer Guide/TCP listeners/CreateLoadBalancerTCPListener.md#)

 |3|
|service.beta.kubernetes.io/alicloud-loadbalancer-unhealthy-threshold|String|The number of consecutive health check failures before the backend server is declared to be unhealthy \(from success to fail\). Valid values: 2 to 10.

 For more information, see [CreateLoadBalancerTCPListener](../../../../reseller.en-US/Developer Guide/TCP listeners/CreateLoadBalancerTCPListener.md#)

 |3|
|service.beta.kubernetes.io/alicloud-loadbalancer-health-check-interval|String|The interval between two consecutive health checks. Unit: seconds. Valid values: 1 to 50.

 For more information, see [CreateLoadBalancerTCPListener](../../../../reseller.en-US/Developer Guide/TCP listeners/CreateLoadBalancerTCPListener.md#)

 |2|
|service.beta.kubernetes.io/alicloud-loadbalancer-health-check-connect-timeout|String|The timeout period for waiting for a health check response. Unit: seconds. This annotation applies to TCP health checks. If a backend server does not respond within the specified timeout period, the server fails the health check. Valid values: 1 to 300.

 **Note:** If the value of the service.beta.kubernetes.io/alicloud-loadbalancer-health-check-connect-timeout annotation is less than that of the service.beta.kubernetes.io/alicloud-loadbalancer-health-check-interval annotation, the service.beta.kubernetes.io/alicloud-loadbalancer-health-check-connect-timeout annotation is invalid. In this case, the value of the service.beta.kubernetes.io/alicloud-loadbalancer-health-check-interval annotation is used as the timeout period.

 For more information, see [CreateLoadBalancerTCPListener](../../../../reseller.en-US/Developer Guide/TCP listeners/CreateLoadBalancerTCPListener.md#)

 |5|
|service.beta.kubernetes.io/alicloud-loadbalancer-health-check-timeout|String|The timeout period for waiting for a health check response. Unit: seconds. This annotation applies to HTTP health checks. If a backend server does not respond within the specified timeout period, the server fails the health check. Valid values: 1 to 300.

 **Note:** If the value of the service.beta.kubernetes.io/alicloud-loadbalancer-health-check-timeout annotation is less than that of the service.beta.kubernetes.io/alicloud-loadbalancer-health-check-interval annotation, the service.beta.kubernetes.io/alicloud-loadbalancer-health-check-timeout annotation is invalid. In this case, the value of the service.beta.kubernetes.io/alicloud-loadbalancer-health-check-interval annotation is used as the timeout period.

 For more information, see [CreateLoadBalancerHTTPListener](../../../../reseller.en-US/Developer Guide/HTTP listeners/CreateLoadBalancerHTTPListener.md#)

 |5|
|service.beta.kubernetes.io/alibaba-cloud-loadbalancer-health-check-domain|String|The domain name used for health checks. -   $\_ip: the private IP address of the backend server. If you do not specify this annotation or you set it to $\_ip, the SLB instance uses the private IP address of each backend server as the domain name for health checks.
-   domain: The domain name must be 1 to 80 characters in length. It can contain only letters, digits, periods \(.\), and hyphens \(-\).

 |None|
|service.beta.kubernetes.io/alibaba-cloud-loadbalancer-health-check-httpcode|String|The HTTP status code indicating that a health check is successful. Separate multiple HTTP status codes with commas \(,\). Valid values: -   http\_2xx
-   http\_3xx
-   http\_4xx
-   http\_5xx

 Default value: http\_2xx.|http\_2xx|
|service.beta.kubernetes.io/alicloud-loadbalancer-scheduler|String|The scheduling algorithm. Valid values: wrr, wlc, and rr. -   wrr \(default\): Backend servers with higher weights receive more requests than those with lower weights.
-   wlc: Requests are forwarded based on the weight and load \(the number of connections\) of each backend server. If the weights are the same, backend servers with fewer connections receive more requests than those with more connections.
-   rr: Requests are sequentially distributed to backend servers.

 |wrr|
|service.beta.kubernetes.io/alibaba-cloud-loadbalancer-acl-status|String|Specifies whether to enable the access control feature. Valid values: on and off.|off|
|service.beta.kubernetes.io/alibaba-cloud-loadbalancer-acl-id|String|The ID of the ACL to which the listener is bound. This annotation is required if the service.beta.kubernetes.io/alibaba-cloud-loadbalancer-acl-status annotation is set to on.|None|
|service.beta.kubernetes.io/alibaba-cloud-loadbalancer-acl-type|String|The type of the ACL. Valid values: white and black.

 -   white: specifies the ACL as a whitelist. Only requests from the IP addresses or classless inter-domain routing \(CIDR\) blocks in the ACL are forwarded. Whitelists are applicable in scenarios where you want to allow only specific IP addresses to access an application. Risks may arise if you specify the ACL as a whitelist. Once a whitelist is set, the SLB instance forwards only requests from the IP addresses in the whitelist. If a whitelist is set without any IP addresses specified in it, the SLB instance forwards all requests.
-   black: specifies the ACL as a blacklist. All requests from the IP addresses or CIDR blocks in the ACL are rejected. Blacklists are applicable in scenarios where you want to forbid specific IP addresses from accessing an application. If a blacklist is set without any IP addresses specified in it, the SLB instance forwards all requests. This annotation is required if the service.beta.kubernetes.io/alibaba-cloud-loadbalancer-acl-status annotation is set to on.

 |None|
|service.beta.kubernetes.io/alibaba-cloud-loadbalancer-vswitch-id|String|The ID of the VSwitch to which the SLB instance belongs. To specify this annotation, you must set the service.beta.kubernetes.io/alicloud-loadbalancer-address-type annotation to intranet.|None|
|service.beta.kubernetes.io/alibaba-cloud-loadbalancer-forward-port|String|The port forwarding configuration for forwarding HTTP requests to the specified HTTPS port. Example: 80:443.|None|
|service.beta.kubernetes.io/alibaba-cloud-loadbalancer-additional-resource-tags|String|The tags to be attached to the SLB instance. Separate multiple tags with commas \(,\). Example: "k1=v1,k2=v2".|None|

