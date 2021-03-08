# Enable distributed tracing in ASM

Alibaba Cloud Service Mesh \(ASM\) integrates Alibaba Cloud Tracing Analysis. Tracing Analysis provides a wide range of tools to help developers identify performance bottlenecks of distributed applications. This helps developers improve the efficiency of developing and troubleshooting applications that use the microservices model. The provided tools can be used to map traces, offer trace topologies, analyze application dependencies, and calculate the number of requests. This topic describes how to use Tracing Analysis in ASM.

-   The [Tracing Analysis](https://tracing-analysis.console.aliyun.com) service is activated.
-   An ASM instance is created and Tracing Analysis is enabled for the instance. For more information, see [Create an ASM instance]().

Distributed tracing can be used to profile and monitor applications, especially those built using a microservices model. Although Istio proxies can automatically send spans, they need some hints to tie together the entire trace. Applications need to propagate appropriate HTTP headers so that the spans sent by Istio proxies can be correctly correlated into a single trace. To do this, applications need to collect the following headers and propagate them from inbound requests to all outbound requests:

-   `x-request-id`
-   `x-b3-traceid`
-   `x-b3-spanid`
-   `x-b3-parentspanid`
-   `x-b3-sampled`
-   `x-b3-flags`
-   `x-ot-span-context`

## Deployment example

[Deploy applications in an ASM instance](). After that, if you check the deployed applications, you can find that all applications use the required HTTP headers. For example, the following sample Python application productpage extracts the required headers from an HTTP request by using [OpenTracing](https://opentracing.io/) libraries:

```
def getForwardHeaders(request):
    headers = {}

    # x-b3-*** headers can be populated using the opentracing span
    span = get_current_span()
    carrier = {}
    tracer.inject(
        span_context=span.context,
        format=Format.HTTP_HEADERS,
        carrier=carrier)

    headers.update(carrier)

    # ...

    incoming_headers = ['x-request-id']

    # ...

    for ihdr in incoming_headers:
        val = request.headers.get(ihdr)
        if val is not None:
            headers[ihdr] = val

    return headers
```

The following sample Java application reviews also extracts the HTTP headers:

```
@GET
@Path("/reviews/{productId}")
public Response bookReviewsById(@PathParam("productId") int productId,
                            @HeaderParam("end-user") String user,
                            @HeaderParam("x-request-id") String xreq,
                            @HeaderParam("x-b3-traceid") String xtraceid,
                            @HeaderParam("x-b3-spanid") String xspanid,
                            @HeaderParam("x-b3-parentspanid") String xparentspanid,
                            @HeaderParam("x-b3-sampled") String xsampled,
                            @HeaderParam("x-b3-flags") String xflags,
                            @HeaderParam("x-ot-span-context") String xotspan) {

  if (ratings_enabled) {
    JsonObject ratingsResponse = getRatings(Integer.toString(productId), user, xreq, xtraceid, xspanid, xparentspanid, xsampled, xflags, xotspan);
```

## Access example

## View applications

On the Applications page of the Tracing Analysis console, you can check key metrics of all monitored applications, including the request count and error count in the current day, and the health status. You can also set tags for applications so that you can filter applications by tag.

1.  Log on to the [Tracing Analysis console](https://tracing-analysis.console.aliyun.com/#/overview).

2.  In the left-side navigation pane, click **Applications**. At the top of the Applications page, select a region as required.


## View application details

On the details page of an application in the Tracing Analysis console, you can view the key metrics, call topology, and traces of each server.

1.  Log on to the [Tracing Analysis console](https://tracing-analysis.console.aliyun.com/#/overview).

2.  In the Tracing Analysis console, click **Applications** in the left-side navigation pane. At the top of the Applications page, select a region as required. On the page that appears, click the name of the application that you want to check.

3.  On the details page of the application, click **Application Details** in the left-side navigation pane. Then, click **All** or select a server, represented by an IP address, from the server list on the left.

    On the page that appears, you can click the **Overview** tab to view the call topology and key metrics. You can also click the **Traces** tab to view the traces of the selected application server. A maximum of 100 traces are listed in descending order of amount of time consumed.


## View the waterfall chart of call traces

The waterfall chart of a call trace shows the information of the trace, including the log generation time, status, IP address or server name, service name, and timeline.

1.  On the details page of the application that you want to check, click the **Traces** tab. In the trace list, click the ID of the target trace.

2.  On the page that appears, view the waterfall chart of the trace.

    **Note:**

    -   The **IP Address** column may display IP addresses or server names. This depends on the display settings configured on the **Application Settings** page. For more information, see [Manage applications and tags](/intl.en-US/Operations in the Console/Application management/Manage applications and tags.md).
    -   You can move the pointer over a service name to view the information of the service, including the duration, start time, tags, and log events.

