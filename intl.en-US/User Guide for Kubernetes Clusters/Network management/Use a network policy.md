# Use a network policy {#concept_tym_gtb_wfb .concept}

## Prerequisites {#section_mjt_5tb_wfb .section}

-   You have created a Kubernetes cluster. For more information, see [Create a Kubernetes cluster](reseller.en-US//Create a Kubernetes cluster.md#).

-   You have selected the **Terway****network plugin** when creating the Kubernetes cluster. For more information, see [Create a Kubernetes cluster](reseller.en-US//Create a Kubernetes cluster.md#).
-   You have connected to the Kubernetes cluster by using kubectl, see [Connect to a Kubernetes cluster by using kubectl](reseller.en-US//Connect to a Kubernetes cluster by using kubectl.md#).

## Verify that an Nginx service is accessible to pods {#section_m2s_hyh_wfb .section}

1.  Run the following command to create an Nginx application and expose it through a service named Nginx:

    ``` {#codeblock_44r_sz6_lkr}
    $ kubectl run nginx --image=nginx
    deployment.apps/nginx created
    $ kubectl get pod
    NAME                     READY   STATUS    RESTARTS   AGE
    nginx-64f497f8fd-znbxb   1/1     Running   0          45s
    
    $ kubectl expose deployment nginx --port=80
    service/nginx exposed
    $ kubectl get service
    NAME         TYPE        CLUSTER-IP    EXTERNAL-IP   PORT(S)   AGE
    kubernetes   ClusterIP   172.19.0.1    <none>        443/TCP   3h
    nginx        ClusterIP   172.19.8.48   <none>        80/TCP    10s
    ```

2.  Run the following command to create a pod named busybox and use the pod to access the Nginx service created in step 1:

    ``` {#codeblock_1dg_sw6_vki}
    $ kubectl run busybox --rm -ti --image=busybox /bin/sh
    kubectl run --generator=deployment/apps.v1beta1 is DEPRECATED and will be removed in a future version. Use kubectl create instead.
    If you don't see a command prompt, try pressing enter.
    / # wget nginx
    Connecting to nginx (172.19.8.48:80)
    index.html           100% |***************************************************************************************************|   612  0:00:00 ETA
    / #
    ```


## Use a network policy to set the Nginx service to be accessible only to a specifically labeled application {#section_sxs_n5b_wfb .section}

1.  Run the following command to create apolicy.yaml file:

    ``` {#codeblock_nao_uwg_g3t}
    $ vim policy.yaml
    kind: NetworkPolicy
    apiVersion: networking.k8s.io/v1
    metadata:
      name: access-nginx
    spec:
      podSelector:
        matchLabels:
          run: nginx
      ingress:
      - from:
        - podSelector:
            matchLabels:
              access: "true"
    ```

2.  Run the following command to create a network policy according to the policy.yaml file created in step 1:

    ``` {#codeblock_tgn_hb5_8iy}
    $ kubectl apply -f policy.yaml 
    networkpolicy.networking.k8s.io/access-nginx created
    ```

3.  Run the following command to verify that the Nginx service cannot be accessed if you do not define any access label in the command:

    ``` {#codeblock_ybi_nao_x9v}
    $ kubectl run busybox --rm -ti --image=busybox /bin/sh
    If you don't see a command prompt, try pressing enter.
    / # wget nginx
    Connecting to nginx (172.19.8.48:80)
    wget: can't connect to remote host (172.19.8.48): Connection timed out
    / #
    ```

4.  Run the following command to verify that the Nginx service can be accessed if an access label is defined in the command:

    ``` {#codeblock_lns_bjh_wyb}
    $ kubectl run busybox --rm -ti --labels="access=true" --image=busybox /bin/sh
    If you don't see a command prompt, try pressing enter.
    / # wget nginx
    Connecting to nginx (172.19.8.48:80)
    index.html           100% |***************************************************************************************************|   612  0:00:00 ETA
    / #
    ```


## Use a network policy to specify a source IP CIDR block that can access a service exposed by an SLB service over the Internet {#section_pdz_jyh_wfb .section}

1.  Run the following command to create an Alibaba Cloud SLB service for the preceding Nginx application, that is, specify `type=LoadBalancer` to expose the Nginx service to the Internet:

    ``` {#codeblock_b8h_ack_qzm}
    $ vim nginx-service.yaml
    apiVersion: v1
    kind: Service
    metadata:
      labels:
        run: nginx
      name: nginx-slb
    spec:
      externalTrafficPolicy: Local
      ports:
      - port: 80
        protocol: TCP
        targetPort: 80
      selector:
        run: nginx
      type: LoadBalancer
    
    $ kubectl apply -f nginx-service.yaml 
    service/nginx-slb created
    
    $ kubectl get service nginx-slb
    NAME        TYPE           CLUSTER-IP      EXTERNAL-IP      PORT(S)        AGE
    nginx-slb   LoadBalancer   172.19.12.254   47.110.200.119   80:32240/TCP   8m
    ```

2.  Run the following command to verify that the IP address of the created SLB service, that is, 47.110.200.119, cannot be accessed:

    ``` {#codeblock_4ka_snt_6rb}
    $ wget 47.110.200.119
    --2018-11-21 11:46:05--  http://47.110.200.119/
    Connecting to 47.110.200.119:80... failed: Connection refused.
    ```

    **Note:** 

    Access failure occurs due to the following reasons:

    -   You have configured access to the Nginx service only for the applications labeled with `access=true`.
    -   You have attempted to access the IP address of the SLB instance from outside the Kubernetes system. This is different from [Use a network policy to set the Nginx service to be accessible only to a specifically labeled application](#section_sxs_n5b_wfb).
    Solution: Modify the network policy and add a source IP CIDR block that is allowed to access the Nginx service.

3.  Run the following command to view your local IP address:

    ``` {#codeblock_asd_p7z_u9x}
    $ curl myip.ipip.net
    
    IP address: 10.0.0.1 from: China Beijing Beijing        #The local IP address varies by devices.
    ```

4.  Run the following command to modify the created policy.yaml file:

    ``` {#codeblock_u36_4mq_cey}
    $ vim policy.yaml
    kind: NetworkPolicy
    apiVersion: networking.k8s.io/v1
    metadata:
      name: access-nginx
    spec:
      podSelector:
        matchLabels:
          run: nginx
      ingress:
      - from:
        - podSelector:
            matchLabels:
              access: "true"
        - ipBlock:
            cidr: 100.64.0.0/10
        - ipBlock:
            cidr: 10.0.0.1/24      #Set the CIDR block to which the local IP address belongs. This is an example. Set the required parameters according to your device.
    
    $ kubectl apply -f policy.yaml 
    networkpolicy.networking.k8s.io/access-nginx unchanged
    ```

    **Note:** 

    -   The outgoing interface of a network may have multiple IP addresses. We recommend that you specify an entire CIDR block.
    -   The SLB health check address belongs to the `100.64.0.0/10` CIDR block. Therefore, you must specify the `100.64.0.0/10` CIDR block.
5.  Run the following command to verify that the Nginx service can be accessed:

    ``` {#codeblock_9pd_2b7_ffw}
    $  kubectl run busybox --rm -ti --labels="access=true" --image=busybox /bin/sh
    
    If you don't see a command prompt, try pressing enter.
    / # wget 47.110.200.119
    Connecting to 47.110.200.119 (47.110.200.119:80)
    index.html           100% |***********************************************************|   612  0:00:00 ETA
    / #
    ```


## Use a network policy to set a pod that can access only www.aliyun.com {#section_lzz_s4j_wfb .section}

1.  Run the following command to obtain the IP address list resolved from the domain name of www.aliyun.com:

    ``` {#codeblock_hea_cz2_ga7}
    $ dig +short www.aliyun.com
    www-jp-de-intl-adns.aliyun.com.
    www-jp-de-intl-adns.aliyun.com.gds.alibabadns.com.
    v6wagbridge.aliyun.com.
    v6wagbridge.aliyun.com.gds.alibabadns.com.
    106.11.93.21
    140.205.32.4
    140.205.230.13
    140.205.34.3
    ```

2.  Run the following command to create abusybox-policy file:

    ``` {#codeblock_iy4_j71_4mw}
    $ vim busybox-policy.yaml
    kind: NetworkPolicy
    apiVersion: networking.k8s.io/v1
    metadata:
      name: busybox-policy
    spec:
      podSelector:
        matchLabels:
          run: busybox
      egress:
      - to:
        - ipBlock:
            cidr: 106.11.93.21/32
        - ipBlock:
            cidr: 140.205.32.4/32
        - ipBlock:
            cidr: 140.205.230.13/32
        - ipBlock:
            cidr: 140.205.34.3/32
      - to:
        - ipBlock:
            cidr: 0.0.0.0/0
        ports:
        - protocol: UDP
          port: 53
    ```

    **Note:** In the preceding busybox-policy file, an egress rule is set to specify the CIDR blocks that can be accessed by cluster applications. You need to set the condition that UDP requests are allowed. Otherwise, DNS resolution will fail.

3.  Run the following command to create a network policy according to the busybox-policy file:

    ``` {#codeblock_nm1_u1r_l73}
    $ kubectl apply -f busybox-policy.yaml 
    networkpolicy.networking.k8s.io/busybox-policy created
    ```

4.  Run the following command to verify that no website \(for example, www.google.com\) can be accessed except for www.aliyun.com:

    ``` {#codeblock_p2b_vap_rvq}
    $ kubectl run busybox --rm -ti --image=busybox /bin/sh
    If you don't see a command prompt, try pressing enter.
    / # wget www.google.com
    Connecting to www.google.com (64.13.192.74:80)
    wget: can't connect to remote host (64.13.192.74): Connection timed out
    ```

5.  Run the following command to verify that www.aliyun.com can be accessed:

    ``` {#codeblock_vg6_p6y_h6i}
    / # wget www.aliyun.com
    Connecting to www.aliyun.com (140.205.34.3:80)
    Connecting to www.aliyun.com (140.205.34.3:443)
    wget: note: TLS certificate validation not implemented
    index.html           100% |***********************************************************|  462k  0:00:00 ETA
    / #
    ```


