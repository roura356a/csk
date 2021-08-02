---
keyword: [CoreDNS, Analyser, DNS resolution error, DNSTAP]
---

# Use ACK CoreDNS DNSTAP Analyser to diagnose CoreDNS resolution errors

Container Service for Kubernetes \(ACK\) provides the ACK CoreDNS DNSTAP Analyser component for you to identify and classify DNS messages that are exchanged between a client and CoreDNS, or between CoreDNS and an upstream DNS server. You can use this component to quickly identify the causes of DNS resolution failures. This topic describes how to use ACK CoreDNS DNSTAP Analyser to diagnose CoreDNS resolution errors.

-   [Create a professional managed Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Professional Kubernetes clusters/Create a professional managed Kubernetes cluster.md)
-   [Connect to ACK clusters by using kubectl](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Access clusters/Connect to ACK clusters by using kubectl.md)

dnstap is a log format for DNS messages. You can use dnstap to troubleshoot resolution issues on DNS servers. For more information, see [dnstap](https://dnstap.info/).

## Step 1: Install ACK CoreDNS DNSTAP Analyser

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane of the ACK console, choose **Marketplace** \> **App Catalog**.

3.  On the App Catalog page, enter ack-coredns-dnstap-analyser into the search box and click the search icon. In the search result, find and click **ack-coredns-dnstap-analyser**.

4.  On the App Catalog - ack-coredns-dnstap-analyser page, select the cluster where you want to install the component in the **Deploy** panel. Click the **Description** tab to read the parameter descriptions. Then, click the **Parameters** tab to set the parameters based on your requirements.

5.  On the right side of the page, click **Create**.


## Step 2: Configure the DNSTAP plug-in in CoreDNS

CoreDNS is pre-installed with the DNSTAP plug-in that sends DNSTAP messages. The DNS messages that are sent and received by the DNSTAP plug-in can be forwarded to ACK CoreDNS DNSTAP Analyser. To use ACK CoreDNS DNSTAP Analyser to diagnose CoreDNS resolution errors, you must configure the DNSTAP plug-in in CoreDNS.

1.  Run the following command to query the cluster IP address of ACK CoreDNS DNSTAP Analyser in the kube-system namespace. This IP address is used by the DNSTAP plug-in. In this example, the IP address is 172.21.0.10.

    ```
    kubectl -n kube-system get svc dnstap-analyser
    ```

2.  Run the following command to modify the CoreDNS configuration file:

    ```
    kubectl -n kube-system edit cm coredns -o yaml
    ```

3.  Modify the default settings of CoreDNS based on the comments in the following code block.

    You must modify the configuration file to enable the DNSTAP plug-in and set its destination address to the cluster IP address of ACK CoreDNS DNSTAP Analyser. This way, the DNS messages that are sent and received by the DNSTAP plug-in are forwarded to ACK CoreDNS DNSTAP Analyser.

    ```
    json      Corefile: |
            .:53 {
                // Omission
                ready
    
                // Add the following line and replace 172.21.0.10 with the actual cluster IP address of CoreDNS DNSTAP Analyser. 
                dnstap tcp://172.21.0.10:6000 full
    
                kubernetes cluster.local in-addr.arpa ip6.arpa {
                  // Omission
                }
                // Omission
                reload
                loadbalance
            }
    ```

4.  Run the following command to query all pods that run CoreDNS:

    ```
    kubectl -n kube-system get pod | grep coredns
    ```

    Expected output:

    ```
    coredns-7d56l         1/1     Running   0          30m
    coredns-s7m2t         1/1     Running   0          30m
    ```

5.  Run the following command to query the CoreDNS log:

    ```
    kubectl -n kube-system logs -f --tail=500 coredns-7d56l
    ```

    **Note:** Replace coredns-7d56l with the name of a pod that you obtained in Step 3.

    Wait one minute. If the following output is returned and no exceptions occur, it indicates that the configuration is successfully modified.

    ```
    [INFO] Reloading complete
    ```


After you complete the preceding steps, the DNSTAP plug-in is enabled in CoreDNS. This way, CoreDNS automatically forwards the DNS messages that are sent and received by the DNSTAP plug-in to ACK CoreDNS DNSTAP Analyser for diagnosis.

## Step 3: Query the log of ACK CoreDNS DNSTAP Analyser

1.  Run the following command to query all pods that run ACK CoreDNS DNSTAP Analyser:

    ```
    kubectl -n kube-system get pod | grep dnstap-analyser
    ```

    Expected output:

    ```
    dnstap-analyser-bbdf879-gpfkm         1/1     Running   0          30m
    ```

2.  Run the following command to query the log of ACK CoreDNS DNSTAP Analyser:

    ```
    kubectl -n kube-system logs -f dnstap-analyser-bbdf879-gpfkm
    ```

    **Note:** Replace dnstap-analyser-bbdf879-gpfkm with the name of the pod that you obtained in Step 1.


## Step 4: Analyze the log of ACK CoreDNS DNSTAP Analyser

In the log of ACK CoreDNS DNSTAP Analyser, each line includes the aggregated information about a DNS session. A DNS session contains all requests and responses that are exchanged between clients, CoreDNS, and upstream DNS servers. A DNS session includes the following fields:

-   Status: Different status values indicate different scenarios.

    |Status|Description|
    |------|-----------|
    |Succeeded|The DNS query completed. The response carries the NXDOMAIN or NOERROR status code.|
    |Failed|The DNS query failed. The response carries an error status code, or the connection to the upstream DNS server timed out.|
    |SampleLoss|CoreDNS or ACK CoreDNS DNSTAP Analyser is overloaded, which results in the loss of DNSTAP messages. In this case, you must adjust the number of pods that run CoreDNS or ACK CoreDNS DNSTAP Analyser to reduce the load on individual containers. This improves the accuracy of diagnostic results.|

-   BitMap: indicates the type of the DNSTAP message that is captured from the session. You can identify possible causes of DNS resolution errors based on message types.

    |BitMap|Description|
    |------|-----------|
    |1|CoreDNS received a DNS query from the client but did not return a response to the client.|
    |3|The DNS query completed within the cluster. The client sent the DNS request. The query hit the local DNS cache of CoreDNS or the Kubernetes cluster. The result was returned to the client.|
    |5|Failed to connect to the upstream DNS server. The client sent the DNS request. CoreDNS forwarded the request to the upstream DNS server. The upstream DNS server did not return a result.|
    |15|The DNS query completed with external DNS lookups. The client sent the DNS request. CoreDNS forwarded the request to the upstream DNS server. The upstream DNS server resolved the domain name and returned the result to CoreDNS. CoreDNS then returned the result to the client.|

-   Messages: records the JSON output of the original DNS request and response messages. The message content can be analyzed based on [dns-parameters-6](https://www.iana.org/assignments/dns-parameters/dns-parameters.xhtml#dns-parameters-6).

## Step 5 \(optional\): View error information in DNS messages in Log Service dashboards

If you configured dashboards in Log Service and set the recommended `env` parameter for log collection when you installed ACK CoreDNS DNSTAP Analyser, you can go to the Kubernetes CoreDNS DNSTAP dashboard of your cluster in Log Service to view error information in DNS messages. For more information about the env parameter, see the **Description** tab introduced in [Step 4](#step_jrp_up5_e1e). For more information, see [Monitor and analyze the log of CoreDNS](/intl.en-US/User Guide for Kubernetes Clusters/Observability/Log management/Monitor and analyze the log of CoreDNS.md).

**Related topics**  


[Overview](/intl.en-US/User Guide for Kubernetes Clusters/Network/Service discovery DNS/Overview.md)

[Introduction and configuration of the DNS service in ACK clusters](/intl.en-US/User Guide for Kubernetes Clusters/Network/Service discovery DNS/Introduction and configuration of the DNS service in ACK clusters.md)

