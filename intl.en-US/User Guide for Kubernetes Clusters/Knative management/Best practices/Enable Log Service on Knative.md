# Enable Log Service on Knative

Log Service is an all-in-one logging service developed by Alibaba Cloud Log Service allows you to collect, consume, ship, query, and analyze log data without development work. After Log Service is enabled on Knative, you can manage and maintain serverless applications with higher efficiency.

-   Log Service is activated. For more information, see [t17400.md\#](/intl.en-US/User Guide for Kubernetes Clusters/Observability/Log management/Use Log Service to collect container logs.md).
-   A Knative Service is deployed. For more information, see [Create a Knative Service](/intl.en-US/User Guide for Kubernetes Clusters/Knative management/Manage Knative services/Create a Knative Service.md).

## Procedure

1.  Collect log data of the Hello World application.

    For more information, see [t13075.md\#](/intl.en-US/Data Collection/Logtail collection/Container log collection/Use the console to collect Kubernetes stdout and stderr logs in DaemonSet mode.md).

    1.  Log on to the [Log Service console](http://sls.console.aliyun.com/).

    2.  In the **Projects** section, click **Create Project** to create a Log Service project named helloworld. After the project is created, create a Logstore under the project. For more information, see [Manage a Logstore](/intl.en-US/Data Collection/Preparation/Manage a Logstore.md).

    3.  Navigate to the details page of the project. In the upper-right corner of the page, click **Import Data**.

    4.  In the Import Data dialog box, find and click **Docker Standard Output** to navigate to the Docker Standard Output page.

        ![Specify the collection mode](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/4912515061/p49477.png)

    5.  In Step 1, select the created Logstore and click **Next**.

    6.  **Create a machine group**.

        If you have already installed the Logtail agent, click **Use Existing Server Groups**.

        You can also click Kubernetes Cluster \(Recommended\) to install the Logtail agent or click Standard Docker Container to deploy a Logtail container. For more information, see [Use Log Service to collect container logs](/intl.en-US/User Guide for Kubernetes Clusters/Observability/Log management/Use Log Service to collect container logs.md) and [Collect logs from standard Docker containers](/intl.en-US/Data Collection/Logtail collection/Container log collection/Collect logs from standard Docker containers.md).

    7.  Click **Complete Installation**.

    8.  Configure **machine group settings**.

    9.  Specify **data source**.

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
                "Regex": "(\\d+-\\d+-\\d+\\s+\\d+:\\d+:\\d+)\\s+(\\w+)\\s+(. *)",
                "SourceKey": "content"
              },
              "type": "processor_regex"
            }
          ]
        }
        ```

    10. Click **Next**.

    11. In Step 5, enable **Full Text Index** and set key/value index attributes.

    12. Click **Next** to proceed to Step 6 and complete the configurations.

2.  Run the following command to access the Hello World application:

    Log data is generated.

    ```
    curl -H "Host: helloworld-go.default.example.com" http://112.124.XX.XX
    ```

    ```
    Hello Knative!
    ```

3.  Go to the details page of the helloworld project. On the details page, find the created Logstore, find the ![Logstore icon](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/0722574161/p94111.png) icon on the right side of the Logstore, and choose **![Logstore icon](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/0722574161/p94111.png)** \> **Search & Analysis**. On the page that appears, you can view the collected logs.

4.  Configure search conditions.

    For more information, see [Real-time log analysis](/intl.en-US/Index and query/Real-time log analysis.md).

    You can modify **Column Settings** to specify the columns that are displayed. In the following example, three columns are displayed: level, msg, and time.

    ![column](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/0722574161/p149161.png)


## Summary

The preceding example demonstrates how to use Log Service to collect log data of a serverless application in Knative. Log Service provides a comprehensive solution for log collection and analytics on Knative and an easy method to manage and maintain serverless applications that run in a production environment.

