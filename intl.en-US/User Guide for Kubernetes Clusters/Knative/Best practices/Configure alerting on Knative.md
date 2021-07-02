---
keyword: [alerting, Log Service]
---

# Configure alerting on Knative

This topic describes how to use Log Service to collect log data from Knative and configure alerts based on the collected log data.

-   A Knative Service is deployed. For more information, see [Use Knative to deploy serverless applications](/intl.en-US/User Guide for Kubernetes Clusters/Knative/Manage Knative services/Use Knative to deploy serverless applications.md).

## Procedure

1.  Configure search conditions.

    For more information, see [Log analysis](/intl.en-US/Index and query/Log analysis.md).

    1.  Log on to the [Log Service console](http://sls.console.aliyun.com/) and click the name of the Log Service project that you want to manage. On the **Logstores** tab, find the Logstore created in [Step 1](/intl.en-US/User Guide for Kubernetes Clusters/Knative/Best practices/Collect application logs.md).

    2.  Click the name of the created Logstore.

    3.  Enter the following command into the search box below the Logstore name and click **Search & Analyze** on the right side of the page.

        To configure an alert based on the number of errors that have occurred, enter the following SQL statement:

        ![Search & Analyze](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/1195748161/p49472.png)

        ```
        * | select 'ERROR' , count(1) as total group by 'ERROR'
        ```

2.  Configure alert settings.

    For more information, see [Create an alert rule](/intl.en-US/Alerting/Alerting (Old)/Create an alert rule.md).

    1.  Click **Save as Alert** in the upper-right corner of the page.

        **Note:** In the Trigger Condition field, enter a conditional expression that determines whether to trigger an alert. For more information, see [Syntax of trigger conditions in alert rules](/intl.en-US/Alerting/Alerting (Old)/Relevant syntax and fields for reference/Syntax of trigger conditions in alert rules.md). In this example, set **Search Period** to 1 minute, **Check Frequency** to 1 minute, and **Trigger Condition** to total \> 3. This means that a search is performed every other minute. If three or more errors are found from the log data within 1 minute, an alert is triggered.

    2.  In the Alert Monitoring Rule pane, specify the configurations of the alert rule.

        |Parameter|Description|
        |---------|-----------|
        |Rule Name|The name of the alert rule. The name must be 1 to 64 characters in length.|
        |Check Frequency|The time interval at which the server checks log data. **Note:** The server collects and checks only the first 100 data entries that are generated during the time specified by the search period. |
        |Dashboard|Select or create a dashboard.|
        |Chart Name|The name of the chart. The name must be 1 to 64 characters in length.|
        |Query|Enter an SQL statement.|
        |Search Period|The **Search Period** parameter specifies the time range of log data that the server reads when a search is performed. You can select a relative time or a time frame. For example, if you set **Search Period** to 15 minutes \(relative\) and start the query at 14:30:06, Log Service reads the log data that was written from 14:15:06 to 14:30:06. If you set **Search Period** to 15 minutes \(time frame\) and start the query at 14:30:06, Log Service reads the log data that was written from 14:15:00 to 14:30:00.|
        |Trigger Condition|The conditional expression that determines whether to trigger an alert. If the condition is met, Log Service sends notifications based on the settings of **Check Frequency** and **Notification Interval**. For example, you can enter `pv%100 > 0 && uv > 0` into the Trigger Condition field.

**Note:** In the conditional expression, you can use `$[serial number]` to differentiate charts. For example, you can use `$0` to identify the chart whose serial number is 0.

For more information, see [How can I view the serial number of a chart?](/intl.en-US/Alerting/Alerting (Old)/Relevant syntax and fields for reference/Syntax of trigger conditions in alert rules.md) |
        |Trigger Threshold|When the cumulative number of times that the trigger condition is met reaches the threshold, notifications are sent based on the **notification interval**. The count does not increase if the trigger condition is not met. The default value of **Trigger Threshold** is 1. This means that each time the **trigger condition** is met, Log Service checks the **notification interval** to decide whether to send a notification.

You can also set Log Service to send only one notification after the trigger condition is met a specified number of times. For example, if you set the value to 100, Log Service checks the **notification interval** to decide whether to send a notification only when the trigger condition is met 100 times. If both the **trigger threshold** and **notification interval** are reached, Log Service sends a notification. After Log Service sends the notification, it resets the count to 0. If Log Service fails to check log data due to exceptions such as network failures, the overall count does not change.|
        |Notification Interval|The time interval at which Log Service sends notifications.

If the cumulative number of times that the trigger condition is met reaches the **trigger threshold** and the length of time since the last notification reaches the specified notification interval, Log Service sends a notification to the specified contacts. If you set this parameter to 5 minutes, you receive at most one notification every 5 minutes. The default value is None.

**Note:** You can use the Trigger Threshold and Notification Interval parameters to control the number of notifications that you can receive during a specified time period. |
        |Notification Policy|Select one or more notification methods. You can choose text messages, phone calls, email, DingTalk notifications, webhooks, or notifications from Alibaba Cloud Message Center.|

    3.  After you configure the notification policy, click **OK**.

        |Notification method|Description|
        |-------------------|-----------|
        |**DingTalk**|Sends notifications through DingTalk. When an alert is triggered, a DingTalk chatbot sends a notification to a DingTalk group. To use this notification method, you must specify **Request URL** and **Content**.

Enter the content of the DingTalk notification into the **Content** field. The content must be 1 to 500 characters in length. Template variables are supported.

For more information about how to configure the DingTalk chatbot and obtain the **request URL**, see [Create an alert rule](/intl.en-US/Alerting/Alerting (Old)/Create an alert rule.md).

**Note:** Each DingTalk chatbot can send up to 20 notifications per minute. |
        |**WebHook**|Sends notifications to a custom webhook URL in a specific method. To use this notification method, you must specify **Request URL**, **Request Method**, and **Content**.

Valid values of **Request Method**: GET, PUT, POST, DELETE, and OPTIONS. Enter the notification content into the **Content** field. The content must be 1 to 500 characters in length. Template variables are supported. |
        |**Alibaba Cloud Message Center**|Sends notifications to specific contacts by using the notification method specified in Alibaba Cloud Message Center. To use this notification method, you must specify the **notification content**. Enter the notification content into the **Content** field. The content must be 1 to 500 characters in length. Template variables are supported.

In addition, you must specify contacts and the notification method in **Alibaba Cloud Message Center**. |

3.  Run the following command to access the Hello World application.

    An alert is triggered:

    ```
    curl -H "Host: helloworld-go.default.example.com" http://112.124.XX.XX
    ```

    ```
    Hello Knative!
    ```

4.  You are notified by email if setting up email notifications.


## Conclusion

After you configure alerts in Log Service, you can detect anomalies of applications and then send notifications to operations and maintenance \(O&M\) engineers and developers in real time. This ensures the continuity of your business.

