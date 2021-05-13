---
keyword: [network policies, network policy, K8s]
---

# Use network policies for access control

You can use Kubernetes network policies to implement policy-based access control. If you want to control the traffic that is received by specific IP addresses or ports, you can configure network policies for applications that use these IP addresses or ports. This topic describes how to use network policies in a Container Service for Kubernetes \(ACK\) cluster.

Make sure that the following prerequisites are met:

-   [Create a managed Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Create Kubernetes clusters/Create a managed Kubernetes cluster.md).

    **Note:** **Terway** is used as the **network plug-in** of the created ACK cluster.

-   [Use kubectl to connect to an ACK cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Access clusters/Use kubectl to connect to an ACK cluster.md).

## Verify that an NGINX application can be accessed by other pods

1.  Run the following command to create an NGINX application. Then, create a Service named NGINX to expose the application.

    ```
    kubectl run nginx --image=nginx
    ```

    Expected output:

    ```
    pod/nginx created
    ```

    ```
    kubectl get pod
    ```

    Expected output:

    ```
    NAME                     READY   STATUS    RESTARTS   AGE
    nginx                    1/1     Running   0          45s
    ```

    ```
    kubectl expose pod nginx --port=80
    ```

    Expected output:

    ```
    service/nginx exposed
    ```

    ```
    kubectl get service
    ```

    Expected output:

    ```
    NAME         TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)   AGE
    kubernetes   ClusterIP   172.XX.XX.1     <none>        443/TCP   30m
    nginx        ClusterIP   172.XX.XX.48    <none>        80/TCP    12s
    ```

2.  Run the following command to create a pod named busybox. Then, use the pod to access the NGINX application that is created in [Step 1](#step_av6_77t_0ze).

    ```
    kubectl run busybox --rm -ti --image=busybox /bin/sh
    ```

    Expected output:

    ```
    If you don't see a command prompt, try pressing enter.
    / #
    ```

    ```
    wget nginx
    ```

    Expected output:

    ```
    Connecting to nginx (172.21.x.114:80)
    saving to 'index.html'
    index.html           100% |****************************************************************************************************************************************************|   612  0:00:00 ETA
    'index.html' saved
    ```


## Configure a network policy to make the NGINX application accessible to only applications with a specified label

1.  Run the `vim policy.yaml` command and use the following YAML template to create a file named policy.yaml.

    ```
    vim policy.yaml
    # The following content provides an example. 
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

2.  Run the following command to create a network policy by using the policy.yaml file that is created in Step 1:

    ```
    kubectl apply -f policy.yaml 
    ```

    Expected output:

    ```
    networkpolicy.networking.k8s.io/access-nginx created
    ```

3.  Run the following command to access the NGINX application. A connection timeout occurs because the pod is not added with the specified label.

    ```
    kubectl run busybox --rm -ti --image=busybox /bin/sh
    ```

    ```
    wget nginx
    ```

    Expected output:

    ```
    Connecting to nginx (172.19.8.48:80)
    wget: can't connect to remote host (172.19.8.48): Connection timed out
    ```

4.  Run the following command to add the specified label to the pod and access the NGINX application again:

    ```
    kubectl run busybox --rm -ti --labels="access=true" --image=busybox /bin/sh
    ```

    ```
    wget nginx
    ```

    Expected output:

    ```
    Connecting to nginx (172.21.x.114:80)
    saving to 'index.html'
    index.html           100% |****************************************************************************************************************************************************|   612  0:00:00 ETA
    'index.html' saved
    ```

    The output shows that the progress of the connection is 100%, which indicates that the pod can access the NGINX application as expected.


## Configure a network policy to expose the NGINX application to access from a specified CIDR block through Internet-facing SLB instances

1.  Run the following command to create a Server Load Balancer \(SLB\) instance for the preceding NGINX application. Set the Service type to `LoadBalancer` to make the NGINX application accessible over the Internet.

    The following template provides an example of the nginx-service.yaml file:

    ```
    vim nginx-service.yaml
    # The following content is an example. 
    
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

    Expected output:

    ```
    service/nginx-slb created
    ```

    ```
    kubectl get service nginx-slb
    ```

    Expected output:

    ```
    NAME        TYPE           CLUSTER-IP      EXTERNAL-IP      PORT(S)        AGE
    nginx-slb   LoadBalancer   172.19.xx.xxx   47.110.xxx.xxx   80:32240/TCP   8m
    ```

2.  Run the following command to connect to the IP address of the created SLB instance: 47.110.xxx.xxx. The output shows that the connection request is denied.

    ```
    wget 47.110.xxx.xxx
    ```

    Expected output:

    ```
    --2018-11-21 11:46:05--  http://47.110.xx.xxx/
    Connecting to 47.110.XX.XX:80... failed: Connection refused.
    ```

    **Note:**

    The access failed due to the following reasons:

    -   The NGINX application can be accessed by only applications that are labeled with `access=true`.
    -   You have attempted to access the IP address of the SLB instance from outside the Kubernetes cluster. Only specified IP addresses can be used to access the NGINX application over the Internet. This is different from how you can use a network policy to allow only applications with specified labels to access the NGINX application.
    Solution: Modify the network policy to allow the source IP address to access the NGINX application.

3.  Run the following command to check the IP address of your on-premises machine:

    ```
    curl myip.ipip.net
    ```

    Expected output:

    ```
    IP address: 10.0.x.x. From: China Beijing Beijing        # This is an example. Use the actual IP address. 
    ```

4.  Run the following command to modify the policy.yaml file:

    ```
    vim policy.yaml
    # The following content is an example. 
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

    Expected output:

    ```
    networkpolicy.networking.k8s.io/access-nginx unchanged
    ```

    **Note:**

    -   The request may come from different IP addresses. Therefore, specify a CIDR block with a prefix length of 24.
    -   The IP address used by the SLB instance for health checks is within the `100.64.0.0/10` CIDR block. Therefore, make sure that you have also specified `100.64.0.0/10` in the network policy.
5.  Run the following command to test access to the NGINX application:

    ```
    kubectl run busybox --rm -ti --labels="access=true" --image=busybox /bin/sh
    ```

    ```
    wget 47.110.XX.XX
    ```

    Expected output:

    ```
    Connecting to 47.110.XX.XX (47.110.XX.XX:80)
    index.html           100% |***********************************************************|   612  0:00:00 ETA
    ```

    The output shows that the progress of the connection is 100%, which indicates that the pod can access the NGINX application as expected.


## Create a network policy to allow a pod to access only www.aliyun.com

1.  Run the following command to query the IP addresses into which the domain name www.aliyun.com is resolved:

    ```
    dig +short www.aliyun.com
    ```

    Expected output:

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
    # The following content is an example. 
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
        - namespaceSelector: {}
        ports:
        - protocol: UDP
          port: 53
    ```

    **Note:** Egress rules are configured in the busybox-policy file to restrict the external services that the application can access. You must specify a User Datagram Protocol \(UDP\) port. Otherwise, the domain name cannot be resolved.

3.  Run the following command to create a network policy by using the busybox-policy file:

    ```
    kubectl apply -f busybox-policy.yaml 
    ```

    Expected output:

    ```
    networkpolicy.networking.k8s.io/busybox-policy created
    ```

4.  Run the following command to access a website other than www.aliyun.com, such as www.taobao.com:

    ```
    kubectl run busybox --rm -ti --image=busybox /bin/sh
    ```

    ```
    wget www.taobao.com
    ```

    Expected output:

    ```
    Connecting to www.taobao.com (64.13.XX.XX:80)
    wget: can't connect to remote host (64.13.XX.XX): Connection timed out
    ```

    The output shows can't connect to remote host, which indicates that the application failed to access the domain name.

5.  Run the following command to access www.aliyun.com:

    ```
    wget www.aliyun.com
    ```

    Expected output:

    ```
    Connecting to www.aliyun.com (140.205.XX.XX:80)
    Connecting to www.aliyun.com (140.205.XX.XX:443)
    wget: note: TLS certificate validation not implemented
    index.html           100% |***********************************************************|  462k  0:00:00 ETA
    ```

    The output shows that the progress of the connection is 100%, which indicates that the application can access the domain name as expected.


## Create a network policy to control access to the Internet for pods in a specified namespace

1.  Run the following command to create a namespace for testing:

    \# Create a namespace named test-np.

    ```
    kubectl create ns test-np
    ```

    Expected output:

    ```
    namespace/test-np created
    ```

2.  Run the following command to set the default network policy that allows pods in the test-np namespace to access only internal services:

    ```
    vim default-deny.yaml
    # The following content is an example. 
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

    Expected output:

    ```
    networkpolicy.networking.k8s.io/deny-public-net created
    ```

    ```
    kubectl get networkpolicy -n test-np
    ```

    Expected output:

    ```
    NAME                              POD-SELECTOR          AGE
    deny-public-net                   <none>                1m
    ```

3.  Run the following command to allow pods with specified labels to access the Internet.

    In this example, the `public-network=true` label is specified.

    ```
    vim allow-specify-label.yaml
    # The following content is an example. 
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

    Expected output:

    ```
    networkpolicy.networking.k8s.io/allow-public-network-for-labels created
    ```

    ```
    kubectl get networkpolicy -n test-np
    ```

    Expected output:

    ```
    NAME                              POD-SELECTOR          AGE
    allow-public-network-for-labels   public-network=true    1m
    deny-public-net                   <none>                 3m
    ```

4.  Run the following command to test whether a pod without the specified label can access the Internet:

    ```
    kubectl run -it --namespace test-np --rm --image busybox  busybox-intranet
    ```

    ```
    ping aliyun.com
    ```

    Expected output:

    ```
    PING aliyun.com (106.11.2xx.xxx): 56 data bytes
    ^C
    --- aliyun.com ping statistics ---
    9 packets transmitted, 0 packets received, 100% packet loss
    ```

    The output shows 0 packets received, which indicates that the pod failed to access the Internet.

    **Note:** Cause: The deny-public-net network policy controls access to the Internet for the pods in the test-np namespace. Therefore, the pods with default labels cannot access the Internet.

5.  Run the following command to test whether a pod with the public-network=true label can access the Internet.

    ```
    kubectl run -it --namespace test-np --labels public-network=true --rm --image busybox  busybox-internet
    ```

    ```
    ping aliyun.com
    ```

    Expected output:

    ```
    PING aliyun.com (106.11.1xx.xx): 56 data bytes
    64 bytes from 106.11.1xx.xx: seq=0 ttl=47 time=4.235 ms
    64 bytes from 106.11.1xx.xx: seq=1 ttl=47 time=4.200 ms
    64 bytes from 106.11.1xx.xx: seq=2 ttl=47 time=4.182 ms
    ^C
    --- aliyun.com ping statistics ---
    3 packets transmitted, 3 packets received, 0% packet loss
    round-trip min/avg/max = 4.182/4.205/4.235 ms
    ```

    The output shows 0% packet loss, which indicates that the pod can access the Internet.

    **Note:** The pod can access the Internet because the allow-public-network-for-labels network policy allows the pods with the public-network=true label to access the Internet. Therefore, the busybox-internet pod with this label can access the Internet.


