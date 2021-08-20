---
keyword: [NetworkPolicy, network policy, K8s, Terway]
---

# Use network policies

You can use Kubernetes network policies to implement policy-based access control. When you use the Terway network plug-in, you can configure network policies to control access to specific applications if you want to control network traffic based on IP address or ports. This topic describes how to use network policies in a Container Service for Kubernetes \(ACK\) cluster and their common use scenarios.

-   [Create a managed Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Create Kubernetes clusters/Create a managed Kubernetes cluster.md).

    **Note:** You must select **Terway** as the **network plug-in** when you create the cluster.

-   [Connect to ACK clusters by using kubectl](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Access clusters/Connect to ACK clusters by using kubectl.md).

## Enable network policies

**Note:**

-   To use network policies through the ACK console, [Submit a ticket](https://workorder-intl.console.aliyun.com/console.htm) to apply to be added to a whitelist.
-   To use network policies through a CLI, you do not need to apply to be added to a whitelist.

You can enable network policies when you create the cluster or after the cluster is created. The cluster must use the Terway network plug-in.

-   To enable network policies when you create the cluster, select **Support for NetworkPolicy** in the Terway Mode section. For more information, see [Create a professional managed Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Professional Kubernetes clusters/Create a professional managed Kubernetes cluster.md).

-   To enable network policies after the cluster is created, go to the **CconfigMap** page, select the **eni-config** ConfigMap in the kube-system namespace, and then set disable\_network\_policy to false. For more information about ConfigMaps, see [Modify a ConfigMap](/intl.en-US/User Guide for Kubernetes Clusters/Application management/Configuration items and key/Manage ConfigMaps.md).

    ![ConfigMap](https://help-static-aliyun-doc.aliyuncs.com/assets/img/en-US/8960557261/p287848.png)

    After the **eni-config** ConfigMap is modified, you must restart Terway to enable network policies. For more information about how to restart Terway, see [Recreate Terway pods](/intl.en-US/User Guide for Kubernetes Clusters/Network/Container network/Increase the number of vSwitches in a cluster that uses the Terway plug-in.md).


## Scenarios

This section describes how to configure network policies in the following scenarios:

-   [Allow only applications with specific labels to access a Service](#section_yys_08i_fxv)
-   [Allow only specific source IP addresses to access a Service through an Internet-facing SLB instance](#section_887_r9y_k1b)
-   [Allow a pod to access a specific address](#section_i3o_oay_a0s)
-   [Control pod access to the Internet in specific namespaces](#section_zrr_z09_kq5)

The preceding scenarios all use the application that is created in [Create an NGINX application that allows access from other pods](#section_lso_lzv_tq4) as an example.

## Create an NGINX application that allows access from other pods

**Use the ACK console**

1.  Create an application named nginx and create a Service to expose the application. For more information, see [Create a Deployment from an image](/intl.en-US/User Guide for Kubernetes Clusters/Application management/Workloads/Create a stateless application by using a Deployment.md).

    The following table describes the sample configurations.

    |Configuration|Parameter|Example|
    |-------------|---------|-------|
    |Basic Information|Name|nginx|
    |Replicas|1|
    |Container|Image Name|nginx|
    |Service|Name|nginx|
    |Type|    -   Server Load Balancer
    -   Public Access
    -   Create SLB Instance |
    |Port Mapping|    -   Name: nginx
    -   Service Port: 80
    -   Container Port: 80
    -   Protocol: TCP |

2.  Create a client application named busybox to test access to the nginx Service created in the preceding step. For more information, see [Create a Deployment from an image](/intl.en-US/User Guide for Kubernetes Clusters/Application management/Workloads/Create a stateless application by using a Deployment.md).

    The following table describes the sample configurations.

    |Configuration|Parameter|Example|
    |-------------|---------|-------|
    |Basic Information|Name|busybox|
    |Replicas|1|
    |Container Start Parameter|Image Name|busybox|
    |Container Start Parameter|Select **stdin** and **tty**.|

3.  Check whether the busybox client application can access the nginx Service.

    1.  On the Deployments page, find the busybox application and click its name.

    2.  On the **Pods** page, select a pod named busybox-\{hash value\} and click **Terminal** in the Actions column.

        ![busybox](https://help-static-aliyun-doc.aliyuncs.com/assets/img/en-US/8960557261/p286133.png)

    3.  In the terminal, run the `wget nginx` command to access the nginx Service.

        ![connection](https://help-static-aliyun-doc.aliyuncs.com/assets/img/en-US/8960557261/p286241.png)

        The preceding output indicates that busybox can access the nginx Service.


**Use the CLI**

1.  Run the following command to create an application named nginx. Then, create a Service named nginx to expose the application.

    Create an application named nginx:

    ```
    kubectl run nginx --image=nginx
    ```

    Expected output:

    ```
    pod/nginx created
    ```

    Query whether the pod of the application is started:

    ```
    kubectl get pod
    ```

    Expected output:

    ```
    NAME                     READY   STATUS    RESTARTS   AGE
    nginx                    1/1     Running   0          45s
    ```

    Create a Service named nginx:

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

2.  Run the following command to create a pod named busybox and use the pod to access the nginx Service:

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
    Connecting to nginx (172.XX.XX.48:80)
    saving to 'index.html'
    index.html           100% |****************************************************************************************************************************************************|   612  0:00:00 ETA
    'index.html' saved
    ```


## Allow only applications with specific labels to access a Service

**Use the ACK console**

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane of the ACK console, click **Clusters**.

3.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  In the left-side navigation pane of the details page, choose **Network** \> **NetworkPolicies**.

5.  In the upper-right corner of the **NetworkPolicies** page, click **Create**. In the **Create** panel, set the parameters.

    |Parameter|Description|
    |---------|-----------|
    |Name|Set the name of the network policy. In this example, access-nginx is used.|
    |Namespace|Set the namespace to which the network policy is scoped.|
    |Pod Selectors|Set the pods to which the network policy applies. In this example, the following settings are used:

    -   Type: **Deployment**
    -   Workload: **nginx**
    -   Label: **app=nginx**
**Note:** If you do not set the pod selector, the network policy applies to all pods in the specified namespace. |
    |Source|Each network policy can include a whitelist of allowed ingress rules. Each rule allows network traffic that matches both the source and port sections.     -   **Rule**:
        -   podSelector: selects pods in the same namespace as the network policy and allows inbound traffic from these pods.
        -   namespaceSelector: selects pods from the specified namespaces and allows inbound traffic from these pods.
        -   ipBlock: selects the specified CIDR blocks and allows inbound traffic from these CIDR blocks.
    -   **Port**: TCP and UDP are supported.
**Note:** If you do not add rules, no pods are allowed to access the pods to which the network policy applies.

In this example, no ingress rules are added. |
    |Destination|Each network policy can include a whitelist of allowed egress rules. Each rule allows network traffic that matches both the destination and port sections.     -   **Rule**:
        -   podSelector: selects pods in the same namespace as the network policy and allows outbound traffic to these pods.
        -   namespaceSelector: selects pods from the specified namespaces and allows outbound traffic to these pods.
        -   ipBlock: selects the specified CIDR blocks and allows outbound traffic to these CIDR blocks.
    -   **Port**: TCP and UDP are supported.
In this example, no egress rules are added. |

6.  Go to the terminal of the busybox pod and run the `wget nginx` command to test access to the nginx Service. For more information, see [Step 3](#step_a7x_mb4_jf4).

    The connection timed out because the network policy does not allow access from the busybox pod.

    ![timeout](https://help-static-aliyun-doc.aliyuncs.com/assets/img/en-US/8960557261/p286629.png)

7.  Modify the network policy to allow access from the busybox pod.

    1.  On the NetworkPolicies page, find the access-nginx network policy and click **Edit** in the Actions column.

    2.  Add an ingress rule.

        Click **+ Add** to the right of **Source**, and then perform the following steps:

        -   Click **+ Add** to the right of **Rule**. The following table describes the rule parameters.

            |Parameter|Example|
            |---------|-------|
            |Selector|podSelector|
            |Type|Deployment|
            |Workload|busybox|

        -   Click **+ Add** to the right of **Port**. The following table describes the port parameters.

            |Parameter|Example|
            |---------|-------|
            |Protocol|TCP|
            |Port|80|

    3.  Click **Next** and then click **OK**.

    4.  Run the `wget -O /dev/null nginx` command to test access to the nginx Service from the busybox pod after the network policy is modified.

        After the ingress rule is added to the network policy, the busybox pod can access the nginx Service.

        ![Access is successful](https://help-static-aliyun-doc.aliyuncs.com/assets/img/en-US/9960557261/p286712.png)


**Use the CLI**

1.  Run the `vim policy.yaml` command to create a file named policy.yaml with the following YAML template.

    ```
    vim policy.yaml
    ```

    The following code block shows the content of the YAML template:

    ```
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

2.  Run the following command to create a network policy by using the preceding policy.yaml file:

    ```
    kubectl apply -f policy.yaml 
    ```

    Expected output:

    ```
    networkpolicy.networking.k8s.io/access-nginx created
    ```

3.  Run the following command to access the nginx Service. A connection timeout error is returned because the pod does not have the specified label.

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

4.  Run the following command to add the specified label to the pod and test whether the pod can access the nginx Service:

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

    The output shows that the progress of the connection is 100%. This indicates that the pod can access the nginx Service as expected.


## Allow only specific source IP addresses to access a Service through an Internet-facing SLB instance

**Use the ACK console**

1.  Create a network policy for the nginx Service. For more information, see [Allow only applications with specific labels to access a Service](#section_yys_08i_fxv).

2.  On the Services page, find the nginx Service created in [Create an NGINX application that allows access from other pods](#section_lso_lzv_tq4) and copy its IP address \(47.xxx.xx.x\) in the **External Endpoint** column. Visit the IP address in your browser.

    ![en](https://help-static-aliyun-doc.aliyuncs.com/assets/img/en-US/9960557261/p286719.png)

    The nginx Service cannot be accessed due to the preconfigured network policy.

3.  Modify the network policy to allow specific IP addresses to access the Service.

    1.  Visit [myip.ipip.net](https://myip.ipip.net/) in your browser to obtain the IP address of your on-premises machine.

    2.  On the NetworkPolicies page, find the access-nginx network policy and click **Edit** in the Actions column. Add an ingress rule in the **Edit** panel.

        Click **+ Add** to the right of **Source**, and then perform the following steps:

        -   Click **+ Add** to the right of **Rule**. Add the IP address of your on-premises machine to the rule.

            |Parameter|Example|
            |---------|-------|
            |Selector|ipBlock|
            |cidr|<IP address of your on-premises machine\>/32Example: 42.xx.xx.xx/32 |

        -   Click **+ Add** to the right of **Rule**. Add the CIDR block used by Server Load Balancer \(SLB\) for health checks to the rule.

            |Parameter|Example|
            |---------|-------|
            |Selector|ipBlock|
            |cidr|100.0.0.0/8|

        -   Click **+ Add** to the right of **Port**. The following table describes the port parameters.

            |Parameter|Example|
            |---------|-------|
            |Protocol|TCP|
            |Port|80|

        ![ipblock](https://help-static-aliyun-doc.aliyuncs.com/assets/img/en-US/9960557261/p286736.png)

    3.  Click **Next** and then click **OK**.

    4.  Access the nginx Service from your on-premises machine \(47.xxx.xx.x\).

        After the network policy is modified, the nginx Service can be accessed through an Internet-facing SLB instance from an on-premises machine.


**Use the CLI**

1.  Run the following command to create an SLB instance for the preceding nginx application. Set the Service type to `LoadBalancer` to make the application accessible over the Internet.

    In this example, a file named nginx-service.yaml is used.

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

2.  Run the following command to connect to the IP address of the created SLB instance: 47.110.xxx.xxx. The output shows that the connection failed.

    ```
    wget 47.110.xxx.xxx
    ```

    Expected output:

    ```
    --2018-11-21 11:46:05--  http://47.110.xx.xxx/
    Connecting to 47.110.XX.XX:80... failed: Connection refused.
    ```

    **Note:**

    The connection failed due to the following reasons:

    -   The nginx Service can be accessed only by applications that have the `access=true` label.
    -   When you connect to the IP address of the SLB instance from your on-premises machine, you are accessing the nginx Service from outside the Kubernetes cluster. This is not the same as when you use a network policy to allow only applications with specified labels to access the nginx Service.
    Solution: Modify the network policy to allow the source IP address to access the nginx Service.

3.  Run the following command to query the IP address of your on-premises machine:

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

    -   The request may come from different IP addresses. Therefore, specify a CIDR block with a mask length of 24.
    -   The IP addresses used by SLB for health checks are within the `100.64.0.0/10` CIDR block. Therefore, make sure that you add `100.64.0.0/10` to the rule.
5.  Run the following command to test access to the nginx Service:

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

    The output shows that the progress of the connection is 100%. This indicates that your on-premises machine can access the nginx Service as expected.


## Allow a pod to access a specific address

**Use the ACK console**

In this section, www.aliyun.com and registry.aliyuncs.com are used as examples. The network policy allows a pod to access only registry.aliyuncs.com.

1.  Visit [Public DNS](https://dns.google.com/) to query the IP address that is associated with registry.aliyuncs.com. In this example, the domain name is resolved to IP address 120.55.105.209.

2.  Create a network policy and add an egress rule to allow the busybox application to access only registry.aliyuncs.com.

    1.  In the upper-right corner of the **NetworkPolicies** page, click **Create**. In the **Create** panel, set the parameters.

        For more information about the parameters, see [Allow only applications with specific labels to access a Service](#section_yys_08i_fxv). The following content provides a sample configuration.

        -   Set **Name** to busybox-policy.
        -   Set pod selectors.

            |Parameter|Example|
            |---------|-------|
            |Type|Deployment|
            |Workload|busybox|
            |Label|app=busybox|

        -   Click **+ Add** to the right of **Destination**, and then perform the following steps:
            -   Click **+ Add** to the right of **Rule**. Select ipBlock and add the IP address associated with registry.aliyuncs.com to the rule. In this example, the IP address is 120.55.105.209/32.

                |Parameter|Example|
                |---------|-------|
                |Selector|ipBlock|
                |cidr|120.55.105.209/32|

        -   Click **+ Add** to the right of **Destination**. Select namespaceSelector and allow outbound traffic to pods in all namespaces through UDP port 53. This ensures that the application can perform DNS lookups.

            -   Click **+ Add** to the right of **Rule**. The following table describes the rule that selects all namespaces.

                |Parameter|Example|
                |---------|-------|
                |Selector|namespace|
                |Namespace|All|

            -   Click **+ Add** to the right of **Port**. Add UDP port 53 to ensure that the application can perform DNS lookups. The following table describes the port parameters.

                |Parameter|Example|
                |---------|-------|
                |Protocol|UDP|
                |Port|53|

            The following figure shows the configuration of the egress rule.

            ![dns](https://help-static-aliyun-doc.aliyuncs.com/assets/img/en-US/9960557261/p286769.png)

    2.  Click **Next** and then click **OK**.

    3.  Go to the terminal of the busybox pod and run the following commands to access www.aliyun.com and registry.aliyuncs.com in sequence:

        ```
        nc -vz -w 1 www.aliyunc.com 443
        ```

        ```
        nc -vz -w 1 registry.aliyuncs.com 443
        ```

        After the network policy is added, the busybox application can access only registry.aliyuncs.com.

        ![dns](https://help-static-aliyun-doc.aliyuncs.com/assets/img/en-US/9960557261/p286772.png)


**Use the CLI**

1.  Run the following command to query the IP addresses to which www.aliyun.com is resolved:

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

2.  Run the following command to create a file named busybox-policy:

    ```
    vim busybox-policy.yaml
    # The following content is an example of the YAML file. 
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

    **Note:** Egress rules are configured in the busybox-policy file to restrict outbound access from the application. You must allow the application to access UDP port 53. Otherwise, the domain name cannot be resolved.

3.  Run the following command to create a network policy based on the busybox-policy file:

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

    The output shows the can't connect to remote host message. This indicates that the application failed to access the domain name.

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

    The output shows that the progress of the connection is 100%. This indicates that the application can access the domain name as expected.


## Control pod access to the Internet in specific namespaces

**Note:** The operations in this section may affect services that are communicating with the Internet. We recommend that you create an empty namespace to perform the following operations.

**Use the ACK console**

1.  In the upper-right corner of the **NetworkPolicies** page, click **Create**. In the **Create** panel, set the parameters.

    For more information about the parameters, see [Allow only applications with specific labels to access a Service](#section_yys_08i_fxv). The following table provides a sample configuration.

    |Parameter|Example|
    |---------|-------|
    |Name|deny-public-net|
    |Pod Selectors|Set Type to **All**.|
    |Source|Add the following ingress rules:    -   Set **namespaceSelector** to **All**.
    -   Set **ipBlock** to **0.0.0.0/0**.
![Source](https://help-static-aliyun-doc.aliyuncs.com/assets/img/en-US/9960557261/p286827.png) |
    |Destination|Add egress rules that allow the pod to access only the internal network.    -   Set **namespaceSelector** to **All**. This allows the pod to access all other pods in the internal network.
    -   Create three rules and set **ipBlock** to the following CIDR blocks in sequence:

        -   10.0.0.0/8
        -   172.16.0.0/12
        -   192.168.0.0/16
**Note:** You cannot specify multiple CIDR blocks for the ipBlock parameter in one rule.

![Destination](https://help-static-aliyun-doc.aliyuncs.com/assets/img/en-US/9960557261/p286832.png) |

2.  Click **Next** and then click **OK**.

3.  Go to the terminal of the busybox pod created in [Create an NGINX application that allows access from other pods](#section_lso_lzv_tq4) and run the following command to test whether the pod can access the Internet:

    ```
    nc -vz -w 1 www.aliyunc.com 443
    ```

    ```
    nc -vz -w 1 192.168.0.120:10255
    ```

    The output shows that the pod can access the internal network but not the Internet.

    ![Test pod access to the Internet](https://help-static-aliyun-doc.aliyuncs.com/assets/img/en-US/9960557261/p286841.png)


**Use the CLI**

1.  Run the following command to create a namespace for testing.

    Create a namespace named test-np.

    ```
    kubectl create ns test-np
    ```

    Expected output:

    ```
    namespace/test-np created
    ```

2.  Run the following command to create a default network policy that allows pods in the test-np namespace to access only internal services:

    ```
    vim default-deny.yaml
    # The following content is an example of the YAML file. 
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

3.  Run the following command to allow pods with specific labels to access the Internet.

    In this example, the `public-network=true` label is used.

    ```
    vim allow-specify-label.yaml
    # The following content is an example of the YAML file. 
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
        - namespaceSelector:
            matchLabels:
              ns: kube-system
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

4.  Run the following command to verify that a pod without the specified label cannot access the Internet:

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

    The output shows 0 packets received. This indicates that the pod failed to access the Internet.

    **Note:** The pod failed to access the Internet because the deny-public-net network policy does not allow pods in the test-np namespace to access the Internet. Therefore, pods with default labels cannot access the Internet.

5.  Run the following command to verify that a pod with the public-network=true label can access the Internet:

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

    The output shows 0% packet loss. This indicates that the pod can access the Internet.

    **Note:** The pod can access the Internet because the allow-public-network-for-labels network policy allows pods with the public-network=true label to access the Internet. The busybox-internet pod has this label and therefore can access the Internet.


