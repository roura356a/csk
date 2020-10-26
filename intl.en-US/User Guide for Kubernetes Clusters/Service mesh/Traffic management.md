# Traffic management

This topic describes how to use Istio to manage traffic. The traffic management feature enables the configuration of load balancing algorithms, session persistence, connection pool management, circuit breaking, and fault injection.

## Procedure

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane, choose **Service Mesh** \> **Virtual Services**.

    ![Virtual Services](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/1602633061/p147885.png)

    **Note:** In the **Gateways** column, the **Bind** button is available for virtual services that are not bound to gateways.

3.  Find the virtual service that you want to manage and click **Manage** in the Actions column. On the details page of the service, you can manage traffic based on your needs.


## Throttling

On the details page of the virtual service, click **Traffic Control**.

![Virtual service details](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/1602633061/p147887.png)

-   You can select **LB Algorithm** or **Session Affinity** for load balancing.

    ![Load balancing algorithm](../images/p43337.jpg "Load balancing algorithm")

    ![Session persistence](../images/p43338.jpg "Session persistence")

    |Parameter| |Description|
    |---------|--|-----------|
    |loadBalancer| |    -   ROUND\_ROBIN: round-robin scheduling. By default, Istio uses a round-robin algorithm for load balancing. This method evenly distributes traffic across the endpoints in the load balancing pool.
    -   LEAST\_CONN: Istio selects two random healthy hosts and uses the host which has fewer active requests. This enables weighted least request load balancing.
    -   RANDOM: Istio forwards requests at random to instances in the load balancing pool. Istio selects a host from all healthy hosts at random and evenly distributes traffic across the endpoints in the load balancing pool. If you do not configure health checks, RANDOM is more efficient than ROUND\_ROBIN, but requests are not forwarded in order.
    -   PASSTHROUGH: Istio forwards requests to the IP address specified by the client. Exercise caution when you select this option. |
    |consistentHash| |    -   Obtains hashes based on HTTP headers.
    -   Obtains hashes based on HTTP cookies.
    -   Obtains hashes based on source IP addresses. |

-   Configure parameters for a connection pool.

    ![Configure parameters for a connection pool](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/5655359951/p43339.jpg)

    |Parameter| |Description|
    |---------|--|-----------|
    |maxConnections| |The maximum number of connections that Envoy can establish for all hosts in an upstream cluster. This parameter applies only to the connections that use the HTTP/1.1 protocol. **Note:** maxConnections and connectTimeout apply to connections that use the TCP or HTTP protocol. |
    |connectTimeout| |The timeout period for a TCP connection. The value must be greater than 1 ms. **Note:** maxConnections and connectTimeout apply to connections that use the TCP or HTTP protocol. |
    |maxRequestsPerConnection| |The maximum number of requests that are destined for a backend server per connection. If you set the value to 1, the keepalive feature is disabled. **Note:** maxRequestsPerConnection and maxRetries apply only to connections that use the HTTP/1.1, HTTP/2, or gRPC protocol. |
    |maxRetries| |The maximum number of retries of an HTTP request that is sent to a destination host during a specified period of time. Default value: 3. **Note:** maxRequestsPerConnection and maxRetries apply only to connections that use the HTTP/1.1, HTTP/2, or gRPC protocol. |

-   You can select the Circuit Breaker Configuration tab to configure a circuit breaker.

    ![Configure a circuit breaker](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/5655359951/p43340.jpg)


|Parameter| |Description|
|---------|--|-----------|
|consecutiveErrors| |The number of consecutive errors that occur to a host at a specified interval. If the value that you specify is exceeded, the host where the consecutive errors occur is removed from the connection pool. Default value: 5. **Note:** If you send an HTTP request to connect to an upstream host, a `5xx` status code is identified as an error. If you send a TCP request to access an upstream host, the following types of events are identified as errors: TCP connection timeouts, connection errors, and connection failures. |
|maxEjectionPercent| |The maximum ratio of removable hosts to all the hosts in a load balancing pool of upstream services. Default value: 10.|
|baseEjectionTime| |The minimum ejection period during which an unhealthy host is ejected from the load balancing pool. A host remains ejected for a period equal to the product of the minimum ejection period and the number of times the host has been ejected. This method allows the system to automatically increase the ejection period for unhealthy upstream hosts. Default value: 30s.|
|interval| |The time interval between ejection sweep analysis. Default value: 10s. Minimum value: 1 ms.|

**Note:** To apply the preceding settings to all versions of the virtual service, click **Apply to All Versions**.

## Create a fault injection policy

On the details page of the virtual service, click **Fault Injection Policy**.

![Create a fault injection policy](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/1602633061/p147889.png)

You can inject two types of faults: **delay faults** and **abort faults**. The fault injection feature supports the HTTP protocol.

-   **Delay**: creates a fault injection policy to delay traffic flow. This method simulates faults before requests are forwarded, such as network faults and faults caused by overloaded upstream services.

    ![Parameters for a delay fault](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/5655359951/p43341.jpg)

    |Parameter| |Description|
    |---------|--|-----------|
    |percent| |The ratio of the requests that you want to delay to all the requests that are forwarded. Valid values: 0 to 100.|
    |fixedDelay| |Required. The delay before the specified ratio of requests are forwarded. You can set the delay in hours, minutes, seconds, or milliseconds. The value must be greater than or equal to 1 ms.|

-   **Abort**: creates a fault injection policy that aborts a request and returns an error code to the downstream service. This method simulates an error that occurs to the upstream service.

    ![Parameters for an abort fault](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/5655359951/p43342.jpg)

    |Parameter| |Description|
    |---------|--|-----------|
    |percent| |The ratio of the requests that you want to abort to all the requests that are forwarded. Valid values: 0 to 100.|
    |httpStatus| |Required. The HTTP status code to be returned to a client service.|


