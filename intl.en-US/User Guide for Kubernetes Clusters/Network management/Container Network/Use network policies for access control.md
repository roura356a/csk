# Use network policies for access control

This topic describes how to use network policies of Container Service for Kubernetes \(ACK\) to control access to your ACK resources.

Before you configure network policies, make sure that you have performed the following steps:

-   [Create a managed Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster management/Create Kubernetes clusters/Create a managed Kubernetes cluster.md).

    **Note:** The **Terway** network plug-in is used for the created ACK cluster.****

-   [Use kubectl to connect to an ACK cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster management/Access clusters/Use kubectl to connect to an ACK cluster.md).

## Verify that an NGINX service can be accessed by other pods

1.  Run the following command to create an NGINX application. Then, create an NGINX service that external clients can use to access the NGINX application.

    ```
    kubectl run nginx --image=nginx
    ```

    ```
    # Output:
    deployment.apps/nginx created
    ```

    ```
    kubectl get pod
    ```

    ```
    # Output:
    NAME                     READY   STATUS    RESTARTS   AGE
    nginx-64f497f8fd-z****   1/1     Running   0          45s
    ```

    ```
    kubectl expose deployment nginx --port=80
    ```

    ```
    # Output:
    service/nginx exposed
    ```

    ```
    kubectl get service
    ```

    ```
    # Output:
    NAME         TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)   AGE
    kubernetes   ClusterIP   172.XX.XX.1     <none>        443/TCP   3h
    nginx        ClusterIP   172.XX.XX.48    <none>        80/TCP    10s
    ```

2.  Run the following command to create a pod named busybox. Then, use the pod to access the NGINX service that is created in [Step 1](#step_av6_77t_0ze).

    ```
    kubectl run busybox --rm -ti --image=busybox /bin/sh
    ```

    ```
    # Output:
    kubectl run --generator=deployment/apps.v1 is DEPRECATED and will be removed in a future version. Use kubectl create instead.
    If you don't see a command prompt, try pressing enter.
    ```

    ```
    wget nginx
    ```

    ```
    # Output:
    Connecting to nginx (172.XX.XX.48:80)
    index.html           100% |***************************************************************************************************|   612  0:00:00 ETA
    ```


## Configure a network policy to make the NGINX service accessible to only an application with a specified label

1.  Run the following command to create a policy.yaml file:

    ```
    vim policy.yaml
    # Copy the following content to the YAML file:
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

2.  Run the following command to create a network policy based on the policy.yaml file that is created in Step 1:

    ```
    kubectl apply -f policy.yaml 
    ```

    ```
    # Output:
    networkpolicy.networking.k8s.io/access-nginx created
    ```

3.  Run the following command to access the NGINX service. A connection timeout is returned because the specified label is not added to the pod.

    ```
    kubectl run busybox --rm -ti --image=busybox /bin/sh
    ```

    ```
    wget nginx
    ```

    ```
    # Output:
    Connecting to nginx (172.19.8.48:80)
    wget: can't connect to remote host (172.19.8.48): Connection timed out
    ```

4.  Run the following command to add the specified label to the pod and test whether the NGINX service can be accessed.

    ```
    kubectl run busybox --rm -ti --labels="access=true" --image=busybox /bin/sh
    ```

    ```
    wget nginx
    ```

    ```
    # Output:
    Connecting to nginx (172.19.8.48:80)
    index.html           100% |***************************************************************************************************|   612  0:00:00 ETA
    ```

    The output shows that the progress of the connection to the NGINX service is 100%. This indicates that the pod can access the NGINX service as expected.


## Configure a network policy to limit IP addresses that can be used to access a public-facing Server Load Balancer \(SLB\) service over the Internet

1.  Run the following command to create an SLB service for the preceding NGINX application and set the service type to `LoadBalancer`. This makes the NGINX service accessible over the Internet.

    The following nginx-service.yaml template is used:

    ```
    vim nginx-service.yaml
    # Copy the following content to the YAML file:
    
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
    ```

    ```
    kubectl apply -f nginx-service.yaml 
    ```

    ```
    # Output:
    service/nginx-slb created
    ```

    ```
    kubectl get service nginx-slb
    ```

    ```
    # Output:
    NAME        TYPE           CLUSTER-IP      EXTERNAL-IP      PORT(S)        AGE
    nginx-slb   LoadBalancer   172.19.xx.xxx   47.110.xxx.xxx   80:32240/TCP   8m
    ```

2.  Run the following command to connect to the IP address of the created SLB service: 47.110.xxx.xxx. The output shows that the connection request is denied.

    ```
    wget 47.110.xxx.xxx
    ```

    ```
    # Output:
    --2018-11-21 11:46:05--  http://47.110.xx.xxx/
    Connecting to 47.110.XX.XX:80... failed: Connection refused.
    ```

    **Note:**

    The access failure occurs due to the following reasons:

    -   The NGINX service can be accessed by only applications that are labeled with `access=true`.
    -   You have attempted to access the IP address of the SLB instance from outside the Kubernetes cluster. Only specified IP addresses can be used to access the NGINX service over the Internet. This is different from the way you use a network policy to allow only specially labeled applications to access the NGINX service.
    Solution: Modify the network policy to allow the source IP address for access to the NGINX service.

3.  Run the following command to view your local IP address:

    ```
    curl myip.ipip.net
    ```

    ```
    IP address: 10.0.x.x. From: China Beijing Beijing        # This is an example. Use the actual IP address.
    ```

4.  Run the following command to modify the policy.yaml file:

    ```
    vim policy.yaml
    # Copy the following content to the YAML file:
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
            cidr: 10.0.0.1/24      # This is an example. Use the actual IP address.
    ```

    ```
    kubectl apply -f policy.yaml 
    ```

    ```
    # Output:
    networkpolicy.networking.k8s.io/access-nginx unchanged
    ```

    **Note:**

    -   The request may come from different IP addresses. Therefore, specify a CIDR block with a prefix length of 24.
    -   The IP address used by the SLB service for health checks is within the `100.64.0.0/10` CIDR block. Therefore, make sure that you have also specified `100.64.0.0/10`.
5.  Run the following command to test whether the NGINX service can be accessed:

    ```
    kubectl run busybox --rm -ti --labels="access=true" --image=busybox /bin/sh
    ```

    ```
    wget 47.110.XX.XX
    ```

    ```
    # Output:
    Connecting to 47.110.XX.XX (47.110.XX.XX:80)
    index.html           100% |***********************************************************|   612  0:00:00 ETA
    ```

    The output shows that the progress of the connection to the NGINX service is 100%. This indicates that the NGINX service can be accessed.


## Create a network policy to allow a pod to access only www.aliyun.com

1.  Run the following command to query IP addresses into which the domain name www.aliyun.com is resolved:

    ```
    dig +short www.aliyun.com
    ```

    ```
    www-jp-de-intl-adns.aliyun.com.
    www-jp-de-intl-adns.aliyun.com.gds.alibabadns.com.
    v6wagbridge.aliyun.com.
    v6wagbridge.aliyun.com.gds.alibabadns.com.
    106.XX.XX.21
    140.XX.XX.4
    140.XX.XX.13
    140.XX.XX.3
    ```

2.  Run the following command to create a busybox-policy file:

    ```
    vim busybox-policy.yaml
    # Copy the following content to the YAML file:
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
            cidr: 106.XX.XX.21/32
        - ipBlock:
            cidr: 140.XX.XX.4/32
        - ipBlock:
            cidr: 140.XX.XX.13/32
        - ipBlock:
            cidr: 140.XX.XX.3/32
      - to:
        - ipBlock:
            cidr: 0.0.0.0/0
        ports:
        - protocol: UDP
          port: 53
    ```

    **Note:** Egress rules are configured in the busybox-policy file to restrict the external services that the application can access. You must specify a User Datagram Protocol \(UDP\) port. Otherwise, the domain name cannot be resolved into IP addresses.

3.  Run the following command to create a network policy based on the busybox-policy file:

    ```
    kubectl apply -f busybox-policy.yaml 
    ```

    ```
    networkpolicy.networking.k8s.io/busybox-policy created
    ```

4.  Run the following command to access a website other than www.aliyun.com, for example, www.taobao.com:

    ```
    kubectl run busybox --rm -ti --image=busybox /bin/sh
    ```

    ```
    wget www.taobao.com
    ```

    ```
    # Output:
    Connecting to www.taobao.com (64.13.XX.XX:80)
    wget: can't connect to remote host (64.13.XX.XX): Connection timed out
    ```

    The output shows can't connect to remote host. This indicates that the application fails to access the domain name.

5.  Run the following command to access www.aliyun.com:

    ```
    wget www.aliyun.com
    ```

    ```
    # Output:
    Connecting to www.aliyun.com (140.205.XX.XX:80)
    Connecting to www.aliyun.com (140.205.XX.XX:443)
    wget: note: TLS certificate validation not implemented
    index.html           100% |***********************************************************|  462k  0:00:00 ETA
    ```

    The output shows 100%. This indicates that the application can access the domain name.


## Create a network policy to restrict access to the Internet for pods in a specified namespace

1.  Run the following command to create a namespace:

    \# Create a namespace named test-np.

    ```
    kubectl create ns test-np
    ```

    ```
    namespace/test-np created
    ```

2.  Run the following command to set the default network policy rule that allows pods to access only internal services:

    ```
    vim default-deny.yaml
    # Copy the following content to the YAML file:
    kind: NetworkPolicy
    apiVersion: networking.k8s.io/v1
    metadata:
      namespace: test-np
      name: deny-public-net
    spec:
      podSelector: {}
      ingress:
      - from:
        - ipBlock:
            cidr: 0.0.0.0/0
      egress:
      - to:
        - ipBlock:
            cidr: 192.168.0.0/16
        - ipBlock:
            cidr: 172.16.0.0/12
        - ipBlock:
            cidr: 10.0.0.0/8
    ```

    ```
    kubectl apply -f default-deny.yaml
    ```

    ```
    # Output:
    networkpolicy.networking.k8s.io/deny-public-net created
    ```

    ```
    kubectl get networkpolicy -n test-np
    ```

    ```
    # Output:
    NAME                              POD-SELECTOR          AGE
    deny-public-net                   <none>                1m
    ```

3.  Run the following command to allow only pods with a specified label to access the Internet.

    In this example, the pods are labeled with `public-network=true`.

    ```
    vim allow-specify-label.yaml
    # Copy the following content to the YAML file:
    kind: NetworkPolicy
    apiVersion: networking.k8s.io/v1
    metadata:
      name: allow-public-network-for-labels
      namespace: test-np
    spec:
      podSelector:
        matchLabels:
          public-network: "true"
      ingress:
      - from:
        - ipBlock:
            cidr: 0.0.0.0/0
      egress:
      - to:
        - ipBlock:
            cidr: 0.0.0.0/0
    ```

    ```
    kubectl apply -f allow-specify-label.yaml
    ```

    ```
    # Output:
    networkpolicy.networking.k8s.io/allow-public-network-for-labels created
    ```

    ```
    kubectl get networkpolicy -n test-np
    ```

    ```
    # Output:
    NAME                              POD-SELECTOR          AGE
    allow-public-network-for-labels   public-network=true    1m
    deny-public-net                   <none>                 3m
    ```

4.  Run the following command to test whether pods without the specified label can access the Internet:

    ```
    kubectl run -it --namespace test-np --rm --image busybox  busybox-intranet
    ```

    ```
    ping aliyun.com
    ```

    ```
    # Output:
    PING aliyun.com (106.11.248.146): 56 data bytes
    ^C
    --- aliyun.com ping statistics ---
    9 packets transmitted, 0 packets received, 100% packet loss
    ```

    The output shows 0 packets received. This indicates that the pod fails to access the Internet.

    **Note:** Cause of the failure: The deny-public-net network policy restricts access to the Internet for the pods in the test-np namespace. Therefore, the pods with default labels cannot access the Internet.

5.  Run the following command to test whether a pod with the public-network=true label can access the Internet:

    ```
    kubectl run -it --namespace test-np --labels public-network=true --rm --image busybox  busybox-internet
    ```

    ```
    ping aliyun.com
    ```

    ```
    PING aliyun.com (106.11.172.56): 56 data bytes
    64 bytes from 106.11.172.56: seq=0 ttl=47 time=4.235 ms
    64 bytes from 106.11.172.56: seq=1 ttl=47 time=4.200 ms
    64 bytes from 106.11.172.56: seq=2 ttl=47 time=4.182 ms
    ^C
    --- aliyun.com ping statistics ---
    3 packets transmitted, 3 packets received, 0% packet loss
    round-trip min/avg/max = 4.182/4.205/4.235 ms
    ```

    The output shows 0% packet loss. This indicates that the pod can access the Internet.

    **Note:** The pod can access the Internet because the allow-public-network-for-labels network policy allows the pods with the public-network=true label to access the Internet. Therefore, the pods with the busybox-internet label can access the Internet.


