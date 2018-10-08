# Collect logs by using Alibaba Cloud Log Service {#concept_v5q_tk1_ydb .concept}

This document uses the sample Tomcat application logs as an example. The log output path is /usr/local/tomcat/logs/catalina. \*.log.

## Step 1. Create a Log Service project {#section_tqw_wk1_ydb .section}

For more information, see [Manage a project](../../../../intl.en-US/User Guide/Preparation/Manage a project.md#).

## Step 2. Create a Logstore {#section_uqw_wk1_ydb .section}

For more information, see [Manage a Logstore ](../../../../intl.en-US/User Guide/Preparation/Manage a Logstore .md#).

## Step 3. Create a machine group {#section_vqw_wk1_ydb .section}

For more information, see [Create a machine group](../../../../intl.en-US/User Guide/Logtail collection/Machine Group/Create a machine group.md#).

**Note:** We recommend that the machine group ID be set to **user-defined ID**. **User-defined ID** is configured to yaml environment variable `ALIYUN_LOGTAIL_USER_DEFINED_ID`.

## Step 4. Create a Logtail configuration {#section_wqw_wk1_ydb .section}

/ecilogs is the mountPath path to the ilogtail container in yaml.

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16506/153896386910340_en-US.png)

## Step 5. Create a Tomcat application {#section_yqw_wk1_ydb .section}

Configure the corresponding environment variable \(ENV\) based on the following template example and create the pod.

```
apiVersion: apps/v1beta2
kind: Deployment
metadata:
  name: tomcat-app
  labels:
    k8s-app: tomcat-app
spec:
  selector:
    matchLabels:
      k8s-app: tomcat-app
  template:
    metadata:
      labels:
        k8s-app: tomcat-app
    spec:
      containers:
      - name: ilogtail
        image: registry.cn-hangzhou.aliyuncs.com/acs/ilogtail:0.13.4-eb42407
        env:
          - name: "ALIYUN_REGION_ID"
            value: "${your_region_id}"
          - name: "ALIYUN_LOGTAIL_USER_ID"
            value: "${your_aliyun_user_id}"
          - name: "ALIYUN_LOGTAIL_USER_DEFINED_ID"
            value: "${your_machine_group_name}"
        volumeMounts:
        - name: tomcat-log
          # The root path here will be set to the logtail configuration
          mountPath: /ecilogs
          readOnly: true
      - name: tomcat
        image: tomcat:7.0
        volumeMounts:
        - name: tomcat-log
          # Set application log output path 
          mountPath: /usr/local/tomcat/logs
      volumes:
      - name: tomcat-log
        emptyDir: {}
```

Description of environment variables:

-   `ALIYUN_REGION_ID`: Region where the Kubernetes cluster resides. We recommend that the Log Service project resides in the same region as the Kubernetes cluster.
-   `ALIYUN_LOGTAIL_USER_ID`: Alibaba Cloud Account ID.
-   `ALIYUN_LOGTAIL_USER_DEFINED_ID`: User ID of the machine group defined by the user in the project.

## Step 6. View the Tomcat logs in Log Service {#section_orw_wk1_ydb .section}

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16506/153896387010341_en-US.png)

