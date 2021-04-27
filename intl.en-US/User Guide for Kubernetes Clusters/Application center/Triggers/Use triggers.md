---
keyword: [use triggers, update an ACK orchestration template, redeploy applications from Git repositories or Helm repositories]
---

# Use triggers

You can use triggers in a third-party environment to deploy applications that are created in Application Center to clusters of Container Service for Kubernetes \(ACK\) or redeploy applications that have already been deployed in ACK clusters by Application Center. This topic describes how to use triggers.

-   [Deploy an application in Application Center](/intl.en-US/User Guide for Kubernetes Clusters/Application center/Application management/Deploy an application in Application Center.md)
-   [Use kubectl to connect to an ACK cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Access clusters/Use kubectl to connect to an ACK cluster.md)

You can create applications in Application Center from the following data sources: Git repositories, Helm repositories, and ACK orchestration templates.

-   If an application is created from an ACK orchestration template, you can use triggers to update the image information \(such as the tag of the image\) in the template, pull the updated template, and use it to redeploy the application.
-   If an application is created from a Git or Helm repository, you can only pull the latest template and use the template to redeploy the application. You cannot use triggers to update the template.

In this example, an application named demo is created. It runs on a Deployment named demo. The URL of the trigger that is created for the application is in the following format: `https://cs.console.aliyun.com/hook/trigger?token=xxxxxxxxx`.

Use the following template to create the demo Deployment:

```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: demo
  labels:
    app: demo
spec:
  minReadySeconds: 5
  revisionHistoryLimit: 5
  progressDeadlineSeconds: 60
  strategy:
    rollingUpdate:
      maxUnavailable: 1
    type: RollingUpdate
  selector:
    matchLabels:
      app: demo
  template:
    metadata:
      annotations:
        prometheus.io/scrape: "true"
        prometheus.io/port: "9797"
      labels:
        app: demo
    spec:
      containers:
      - name: demo
        image: registry.cn-hangzhou.aliyuncs.com/acs/rollouts-demo:blue
        imagePullPolicy: IfNotPresent
        ports:
        - name: http
          containerPort: 8080
          protocol: TCP
        readinessProbe:
          tcpSocket:
            port: 8080
          initialDelaySeconds: 5
          timeoutSeconds: 5
        resources:
          limits:
            cpu: 2000m
            memory: 512Mi
          requests:
            cpu: 100m
            memory: 64Mi
```

## Update an ACK orchestration template and use it to redeploy the application that is created from the template

If an application is created from an ACK orchestration template, you can run the following command to set a trigger to update the image of the demo Deployment and redeploy the application in the cluster.

```
curl -X POST -H 'content-type: application/json' https://cs.console.aliyun.com/hook/trigger?token=xxxxxxxxx -d '{"resource":{"deployment":{"metadata":{"name":"demo"}, "spec": {"template": {"spec": {"containers": [{"name":"demo","image":"registry.cn-hangzhou.aliyuncs.com/acs/rollouts-demo:red"}]}}}}}}'
```

**Note:**

The body of the preceding command contains the JSON patch that is used to update the Deployment.

The patch includes the following information about the Deployment: the name of the Deployment, the names of containers, and the address of the image registry.

```
{
    "resource":{
        "deployment":{
            "metadata":{
                "name":"demo"
            },
            "spec": {
                "template": {
                    "spec": {
                        "containers": [
                            {
                                "name":"demo",
                                "image":"registry.cn-hangzhou.aliyuncs.com/acs/rollouts-demo:red"
                            }]
                    }
                }
            }
        }
    }
}
```

In this example, the trigger completes the following tasks:

-   Updates the ACK orchestration template and automatically generates a new template version.
-   Checks whether the data source of the application is updated. If the data source is updated, the latest ACK orchestration template is pulled and then used to redeploy the Deployment.
-   Redeploys the application in the cluster by using the latest ACK orchestration template.

## Redeploy an application that is created from a Git or Helm repository

If an application is created from a Git or Helm repository, you can run the following command to set a trigger to redeploy the application in the cluster. However, you cannot pass parameters to the trigger in the body of the command.

```
curl -X POST https://cs.console.aliyun.com/hook/trigger?token=xxxxxxxxx
```

In this example, the trigger completes the following tasks:

-   Checks whether the data source of the application is updated. If the data source is updated, the latest template is pulled from the Git or Helm repository to redeploy the Deployment.
-   Redeploys the application in the cluster by using the latest ACK orchestration template.

