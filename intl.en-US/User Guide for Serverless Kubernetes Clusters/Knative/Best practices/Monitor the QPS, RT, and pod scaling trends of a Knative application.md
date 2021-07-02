---
keyword: [Knative, monitoring service, QPS, RT, and pod scaling trends]
---

# Monitor the QPS, RT, and pod scaling trends of a Knative application

You can monitor the queries per second \(QPS\), response time \(RT\), and pod scaling trends of a Knative application. This topic describes how to collect these metrics and view the collected statistics.

Log Service and Knative are enabled for a serverless Kubernetes \(ASK\) cluster. For more information, see [Enable Knative](/intl.en-US/User Guide for Serverless Kubernetes Clusters/Knative/Component management/Enable Knative.md).

## Create and configure a Logstore

1.  Log on to the [Log Service console](https://sls.console.aliyun.com).

2.  In the Projects section, click the Log Service project that is created for the ASK cluster.

3.  On the **Logstores** tab of the **Log Storage** page, click the **+** icon.

4.  In the Create Logstore panel, set the parameters and click **OK**. For more information about the parameters that are required to **create a Logstore**, see [Manage a Logstore](/intl.en-US/Preparation/Manage a Logstore.md).

5.  In the **Created.** dialog box, click **Data Import Wizard**.

6.  In the Data Import section, click **Docker Standard Output**.

    ![86](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/8342234161/p205975.png)

7.  In the **Create Machine Group** step, click **Use Existing Machine Group**.

8.  In the **Machine Group Settings** step, select an existing machine group and click **Next**.

9.  In the **Specify Data Source** step, set log collection rules by using the following template and click **Next**.

    ```
    {
        "inputs": [
            {
                "detail": {
                    "IncludeEnv": {
                        "QUEUE_SERVING_PORT": "8012"
                    },
                    "ExcludeLabel": {}
                },
                "type": "service_docker_stdout"
            }
        ]
    }
    ```

10. In the **Configure Query and Analysis** step, keep the default settings and click **Next**.

11. In the **End** step, click **Search** in the **Log Query** section.

    1.  Click the **Search & Analysis** icon to view the collected log data.

        ![92-1](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/9342234161/p206185.png)

    2.  Click **Index Attributes** and select **Attributes** from the drop-down list.

        ![92-2](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/9342234161/p206189.png)

    3.  In the **Search & Analysis** panel, click **Automatic Index Generation**. In the **Automatically Generate Index Attributes** dialog box, click **Append** and add the following parameters to the **content** field.

        |Parameter|Type|Alias|
        |---------|----|-----|
        |httpRequest.latency|text|latency|
        |httpRequest.status|long|status|
        |httpRequest.revisionName|text|revision|

        ![K13](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/8566025261/p264340.png)


## Create a QPS chart

1.  Log on to the [Log Service console](https://sls.console.aliyun.com).

2.  In the Projects section, click the Log Service project that is created for the ASK cluster.

3.  On the **Logstores** tab of the **Log Storage** page, click the Logstore that you want to manage.

4.  Enter the following SQL statement into the **Search & Analyze** field.

    ![97-ch](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/9342234161/p207338.png)

    ```
    httpRequest | select date_trunc('minute' ,  __time__) as stamp, count(*) as num, revision from log group by stamp,revision order by stamp
    ```

5.  Click the **Flow Chart** icon. Set the parameters on the **Properties** tab and click **Add to New Dashboard**.

    ![91-ch](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/9342234161/p206816.png)


## Create an RT chart

1.  Log on to the [Log Service console](https://sls.console.aliyun.com).

2.  In the Projects section, click the Log Service project that is created for the ASK cluster.

3.  On the **Logstores** tab of the **Log Storage** page, click the Logstore that you want to manage.

4.  Enter the following SQL statement into the **Search & Analyze** field.

    ```
    httpRequest|select  stamp as timestamp, trt as rt, revision from (select  date_trunc('minute' ,  __time__)  as stamp, round (avg(cast(replace(latency,'s') as double)), 6) as trt, revision   from log   group by   stamp, revision
           order by stamp 
           limit 100000)
    ```

5.  Click the **Flow Chart** icon. Set the parameters on the **Properties** tab and click **Add to New Dashboard**.

    ![93-ch](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/9342234161/p206866.png)


## Create a pod scaling trend chart

1.  Log on to the [Log Service console](https://sls.console.aliyun.com).

2.  In the Projects section, click the Log Service project that is created for the ASK cluster.

3.  On the **Logstores** tab of the **Log Storage** page, click the Logstore whose name starts with audit.

4.  Click **Index Attributes** and select **Attributes** from the drop-down list.

5.  In the **Search & Analysis** panel, add the following parameters to the **responseObject** field in the **Field Search** section.

    |Parameter|Type|Alias|
    |---------|----|-----|
    |metadata.labels.serving.knative.dev/revision|text|revision|
    |metadata.labels.serving.knative.dev/service|text|service|
    |status.readyReplicas|long|readyReplicas|
    |status.replicas|long|replicas|

6.  Enter the following SQL statement into the **Search & Analyze** field.

    ```
    objectRef.resource: deployments and responseStatus.code: 200 and ( verb: update or verb: get)| select case when "revision" is null then '无' else "revision" end as "revision", "t" as timestamp, total,concat('total:', cast(total AS varchar), ', ready: ', cast(ready AS varchar), ', notReady',cast((total-ready) AS varchar)) as detail from (select date_trunc('minute' ,  __time__) as t, max(replicas) as total, max(readyReplicas) as ready, revision from log where service='event-display-common' group by t, revision order by t)
    ```

7.  Click the **Flow Chart** icon. Set the parameters on the **Properties** tab and click **Add to New Dashboard**.

    ![94-ch](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/9342234161/p206950.png)


## Monitor the application status

To monitor the application status, you can add the preceding charts to one dashboard by using the following methods:

-   Select the same dashboard for each chart.
-   Create a global dashboard and add the preceding charts to the dashboard. For more information, see [Create a dashboard](/intl.en-US/Visualization/Create a dashboard.md) and [Add an analysis chart to a dashboard](/intl.en-US/Visualization/Add an analysis chart to a dashboard.md).

    ![96](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/9342234161/p206990.png)


