# Collect logs by using Alibaba Cloud Log Service {#concept_v5q_tk1_ydb .concept}

This document uses the sample Tomcat application logs as an example. The log output path is /usr/local/tomcat/logs/catalina. \*.log.

## Step 1. Create a Log Service project {#section_tqw_wk1_ydb .section}

For more information, see [Manage a project](../../../../reseller.en-US/User Guide/Preparation/Manage a project.md#).

## Step 2. Create a Logstore {#section_uqw_wk1_ydb .section}

For more information, see [Manage a Logstore](../../../../reseller.en-US/User Guide/Preparation/Manage a Logstore.md#).

## Step 3. Create a machine group {#section_vqw_wk1_ydb .section}

**Note:** We recommend that the machine group ID be set to **user-defined ID**. **User-defined ID** is configured to yaml environment variable `ALIYUN_LOGTAIL_USER_DEFINED_ID`.

## Step 4. Create a Logtail configuration {#section_wqw_wk1_ydb .section}

/ecilogs is the mountPath path to the ilogtail container in yaml.

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16506/155719483810340_en-US.png)

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
        image: registry-vpc.${your_region_id}.aliyuncs.com/acs/logtail:0.16.16.0-1cf247c-aliyun
        env:
          - name: "ALIYUN_LOGTAIL_CONFIG"
            value: "/etc/ilogtail/conf/${your_region_id}/ilogtail_config.json"
          - name: "ALIYUN_LOGTAIL_USER_ID"
            value: "${your_aliyun_user_id}"
          - name: "ALIYUN_LOGTAIL_USER_DEFINED_ID"
            value: "${your_machine_group_name}"
        volumeMounts:
        - name: tomcat-log
          # The following root path will be added to the Logtail configuration.
          mountPath: /ecilogs
          readOnly: true
      - name: tomcat
        image: tomcat:7.0
        volumeMounts:
        - name: tomcat-log
          # Set the directory to which the application logs are output.
          mountPath: /usr/local/tomcat/logs
      volumes:
      - name: tomcat-log
        emptyDir: {}
```

**Note:** 

-   You need to replace `${your_aliyun_user_id}` with your primary account ID that can be obtained by running the echo $ALIBABA\_CLOUD\_ACCOUNT\_I command in [CloudShell](reseller.en-US/User Guide/Kubernetes cluster/Cluster management/Use kubectl on Cloud Shell to manage a Kubernetes cluster.md#).
-   You need to replace `${your_machine_group_name}` with the machine group ID that you set in the corresponding project in Log Service. For more information, see [Create an ID to identify a machine group](../../../../reseller.en-US/User Guide/Logtail collection/Machine Group/Create an ID to identify a machine group.md#).
-   You need to replace `${your_region_id}` with your region. For example, cn-hangzhou and ap-southeast-1.
-   We recommend that you use the image tag of the latest version. For more information, see [Tag list](https://parters-intl.console.aliyun.com/images/cn-hangzhou/acs/logtail/detail).

## Step 6. View the Tomcat logs in Log Service {#section_orw_wk1_ydb .section}

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16506/155719483810341_en-US.png)

