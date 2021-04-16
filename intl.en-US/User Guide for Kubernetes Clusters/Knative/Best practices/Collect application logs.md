# Collect application logs

Log Service is an all-in-one service to manage log data. Log Service allows you to collect, consume, ship, query, and analyze log data without development work. After Log Service is enabled on Knative, you can manage and maintain serverless applications with higher efficiency.

-   Log Service is activated. For more information, see [t17400.md\#](/intl.en-US/User Guide for Kubernetes Clusters/Observability/Log management/Use Log Service to collect container logs.md).
-   A Knative Service is deployed. For more information, see [t474495.md\#]().

## Procedure

1.  Collect log data of the Helloworld application.

    For more information, see [t13075.md\#](/intl.en-US/Data Collection/Logtail collection/Container log collection/Use the console to collect Kubernetes stdout and stderr logs in DaemonSet mode.md).

    1.  Log on to the [Log Service console](http://sls.console.aliyun.com/).

    2.  In the **Projects** section, click **Create Project** to create a Log Service project named helloworld. For more information, see [Manage a Logstore](/intl.en-US/Data Collection/Preparation/Manage a Logstore.md).

    3.  Click the created helloworld project.

    4.  Navigate to the details page of the project. In the upper-right corner of the page, click **Import Data**.

    5.  In the Import Data dialog box, find and click **Docker Standard Output** to navigate to the Docker Standard Output page.

        ![Specify the collection mode](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/4912515061/p49477.png)

    6.  In the Specify Logstore step, select the created Logstore and click **Next**.

    7.  Configure **Create Machine Group**.

        If the Logtail agent is installed, click **Use Existing Machine Groups**.

        You can also click Kubernetes Cluster \(Recommended\) to install the Logtail agent or click Standard Docker Container to deploy a Logtail container. For more information, see [Use Log Service to collect container logs](/intl.en-US/User Guide for Kubernetes Clusters/Observability/Log management/Use Log Service to collect container logs.md) and [Collect logs from standard Docker containers](/intl.en-US/Data Collection/Logtail collection/Container log collection/Collect logs from standard Docker containers.md).

    8.  Click **Complete Installation**.

    9.  Configure **Machine Group Settings**.

    10. Configure **Specify Data Source**.

        In the plug-in configuration, specify the following environment variable for log collection: `"K_SERVICE": "helloworld-go"`. Use processors to process log data, for example, `"Keys": [ "time","level", "msg" ]`. The following code block is an example:

        ```
        {
          "inputs": [
            {
              "detail": {
                "IncludeEnv": {
                  "K_SERVICE": "helloworld-go"
                },
                "IncludeLabel": {},
                "ExcludeLabel": {}
              },
              "type": "service_docker_stdout"
            }
          ],
          "processors": [
            {
              "detail": {
                "KeepSource": false,
                "NoMatchError": true,
                "Keys": [
                  "time",
                  "level",
                  "msg"
                ],
                "NoKeyError": true,
                "Regex": "(\\d+-\\d+-\\d+\\s+\\d+:\\d+:\\d+)\\s+(\\w+)\\s+(.*)",
                "SourceKey": "content"
              },
              "type": "processor_regex"
            }
          ]
        }
        ```

    11. After the data source is configured, click **Next**.

    12. In the Configure Query and Analysis step, enable **Full Text Index** and set key/value index attributes.

    13. Click **Next** to proceed to the End page.

2.  Run the following command to access the Helloworld application.

    The following log data is generated:

    ```
    curl -H "Host: helloworld-go.default.example.com" http://112.124.XX.XX
    ```

    Expected output:

    ```
    Hello Knative!
    ```

3.  Go to the details page of the helloworld project. On the details page, find the created Logstore, move the pointer over the ![Logstore icon](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/0722574161/p94111.png) icon on the right side of the Logstore, and choose **![Logstore icon](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/0722574161/p94111.png)** \> **Search & Analysis**. On the page that appears, you can view the log data in the Logstore.

4.  Configure search conditions.

    For more information, see [Real-time log analysis](/intl.en-US/Index and query/Real-time log analysis.md).

    You can modify **Column Settings** to specify the columns that are displayed. In the following example, three columns are displayed: level, msg, and time.

    ![Column](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/0722574161/p149161.png)


## Conclusion

The preceding example demonstrates how to use Log Service to collect the log data of a serverless Knative application. You can use Log Service to collect and analyze log data of Knative applications. This helps manage and maintain serverless Knative applications in a production environment.

