---
keyword: [Jenkins CI, automatic application update, image version]
---

# Use the Jenkins CI pipeline to enable automatic application update

You can use the triggers provided by Application Center to update the image version specified in the source template of an application and redeploy the application. You can use triggers to update only orchestration templates. You can use Jenkins to build a continuous integration \(CI\) pipeline. After the CI pipeline is built, you can build images and update the versions of the images used by orchestration templates. Then, you can use triggers to update applications based on the orchestration templates. This topic describes how to use the Jenkins CI pipeline to enable automatic application update.

-   An application is deployed and a trigger is created for the application. For more information, see [Create an application from an orchestration template](/intl.en-US/User Guide for Kubernetes Clusters/Application Center/Application management/Create an application from an orchestration template.md) and [Use a trigger to enable automatic application update](/intl.en-US/User Guide for Kubernetes Clusters/Application Center/Triggers/Use a trigger to enable automatic application update.md).
-   The Jenkins CI pipeline is built. For more information, see [Set up Jenkins to run pipeline jobs](/intl.en-US/Best Practices/DevOps/Set up Jenkins to run pipeline jobs.md).

In this topic, the image version used by the application specifies that the red color appears on the application page. You can use the Jenkins CI pipeline to change the image version to one that specifies the green color. Then, use the trigger to update the application. After the application is updated, you can access the application by using your browser to check whether the color is changed. The result indicates whether the application is successfully updated by the trigger. If the color that appears on the application page is changed to green, it indicates that the image version of the application is changed. This also indicates that the application update is successfully performed by using the Jenkins CI pipeline.

## Procedure

1.  Create a build job to create and push a `registry.cn-hangzhou.aliyuncs.com/acs/rollouts-demo:green` image. For more information, see [Set up Jenkins to run pipeline jobs](/intl.en-US/Best Practices/DevOps/Set up Jenkins to run pipeline jobs.md).

2.  Run the following script to trigger Application Center to use the `registry.cn-hangzhou.aliyuncs.com/acs/rollouts-demo:green` image to redeploy the application.

    ```
    ## 4. trigger appcenter
    DEPLOYMENT_NAME=demo
    CONTAINER_NAME=demo
    IMAGE=registry.cn-hangzhou.aliyuncs.com/acs/rollouts-demo:green
    cat <<EOF > patch.json
    {
        "resource":{
            "deployment":{
                "metadata":{
                    "name":"$DEPLOYMENT_NAME"
                },
                "spec": {
                    "template": {
                        "spec": {
                            "containers": [
                                {
                                    "name":"$CONTAINER_NAME",
                                    "image":"$IMAGE"
                                }]
                        }
                    }
                }
            }
        }
    }
    EOF
    
    curl -X POST -H 'content-type: application/json' \
        https://cs.console.aliyun.com/hook/trigger?token=xxxxxxxx \
        -d "$(cat patch.json)"
    {"code":"200","message":"","requestId":"151c92fd-d97b-4eff-a6da-4669114fa4f0"}
    ```


## Verify the result

1.  Gets the endpoint of the application.

    1.  Log on to the [ACK console](https://cs.console.aliyun.com).

    2.  In the left-side navigation pane of the ACK console, click **Clusters**.

    3.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

    4.  In the left-side navigation pane of the details page, choose **Network** \> **Ingresses**.

    5.  View the endpoint of the demo on the routing page.

2.  Enter the following in the hosts file.

    ```
    <endpoint> app.demo.example.com 
    ```

3.  Enter app.demo.example.com in the address bar of your browser and press Enter to access the application.

    If the page appears as shown in the following figure, it indicates that the color is changed and the application is updated.

    ![Verify the result](https://help-static-aliyun-doc.aliyuncs.com/assets/img/en-US/2683779161/p263738.png)


**Related topics**  


[Trigger overview](/intl.en-US/User Guide for Kubernetes Clusters/Application Center/Triggers/Trigger overview.md)

[Use a trigger to enable automatic application update](/intl.en-US/User Guide for Kubernetes Clusters/Application Center/Triggers/Use a trigger to enable automatic application update.md)

