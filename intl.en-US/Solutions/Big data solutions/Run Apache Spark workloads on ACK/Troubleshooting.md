---
keyword: [View the Spark job ID, Spark History Server, Troubleshooting, ACK]
---

# Troubleshooting

During the test, invalid parameters may lead to exceptions in Apache Spark jobs. This topic describes how to use the Spark UI and ACK Spark History Server to view application status and troubleshoot problems.

[Analyze test results](/intl.en-US/Solutions/Big data solutions/Run Apache Spark workloads on ACK/Analyze test results.md)

## Access Spark UI

You can to view the status of each SQL job in Spark UI in real time. The following content describes the procedure:

1.  Run the **kubectl get services** command.

    ![service SQL](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/6935780061/p161789.jpeg)

    In this command, **tpcds-query-runner-with-alluxio-ui-svc** is the Spark UI service.

2.  Run the following command to access the Spark UI from your on-premises machine:

    ```
    kubectl port-forward service/tpcds-query-runner-with-alluxio-ui-svc 4040:4040
    ```

    The following figure shows the output of the command:

    ![localhost](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/6935780061/p161790.png)

3.  In the address bar of a browser, enter localhost:4040 to view jobs in Spark UI.

    ![spark ui](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/7935780061/p161792.jpeg)


## Access ACK Spark History Server

After a job is completed, you can perform the following operations to view historical data:

1.  Run the following command to query the ID of the sparkapplication job:

    ```
    kubectl get sparkapplication tpcds-query-runner-with-alluxio -o yaml
    ```

    The following figure shows **sparkApplicationId**.

    ![ID](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/7935780061/p161794.jpeg)

2.  Run the following command to query the endpoint of ACK Spark History Server:

    ```
    kubectl get service ack-spark-history-server 
    ```

    ![eternal ip](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/7935780061/p161801.jpeg)

3.  In the address bar of a browser, enter the IP address \(39.XX.XX.XXX\) that appears in the **EXTERNAL-IP** column in the preceding figure and the port number.

    Then you can view the historical data of all Spark jobs. By using the **sparkApplicationId** returned in [Step 1](#step_dg0_bsm_2n1), you can find the job that you want to view and perform troubleshooting.


