# Query an orchestration template

You can run the `arc-cs template get <template_id> --template-type <template_type>` command to query the details of a specified orchestration template.

For more information about this command, see [DescribeTemplateAttribute](/intl.en-US/API Reference/Applications/DescribeTemplateAttribute.md).

## CLI example

Sample request:

```
arc-cs template get 6e0e568b-90f7-48f0-a3c5-444baf2c**** \
--template-type kubernetes
```

Sample output:

```
[
  {
    "id": "6e0e568b-90f7-48f0-a3c5-444baf2c****",
    "acl": "private",
    "name": "ds-162867555****",
    "template": "apiVersion: apps/v1\r\nkind: Deployment\r\nmetadata:\r\n  name: demo\r\n  labels:\r\n    app: demo\r\nspec:\r\n  minReadySeconds: 5\r\n  revisionHistoryLimit: 5\r\n  progressDeadlineSeconds: 60\r\n  strategy:\r\n    rollingUpdate:\r\n      maxUnavailable: 1\r\n    type: RollingUpdate\r\n  selector:\r\n    matchLabels:\r\n      app: demo\r\n  template:\r\n    metadata:\r\n      annotations:\r\n        prometheus.io/scrape: \"true\"\r\n        prometheus.io/port: \"9797\"\r\n      labels:\r\n        app: demo\r\n    spec:\r\n      containers:\r\n      - name: demo\r\n        image: registry.cn-hangzhou.aliyuncs.com/acs/rollouts-demo:red\r\n        imagePullPolicy: IfNotPresent\r\n        ports:\r\n        - name: http\r\n          containerPort: 8080\r\n          protocol: TCP\r\n        readinessProbe:\r\n          tcpSocket:\r\n            port: 8080\r\n          initialDelaySeconds: 5\r\n          timeoutSeconds: 5\r\n        resources:\r\n          limits:\r\n            cpu: 2000m\r\n            memory: 512Mi\r\n          requests:\r\n            cpu: 100m\r\n            memory: 64Mi\n---\napiVersion: v1\r\nkind: Service\r\nmetadata:\r\n  name: demo-svc\r\nspec:\r\n  selector:\r\n    app: demo\r\n  ports:\r\n    - protocol: TCP\r\n      port: 80\r\n      targetPort: 8080\n---\napiVersion: networking.k8s.io/v1\r\nkind: Ingress\r\nmetadata:\r\n  name: demo\r\n  labels:\r\n    app: demo\r\nspec:\r\n  rules:\r\n    - host: app.demo.example.com\r\n      http:\r\n        paths:\r\n          - backend:\r\n              serviceName: demo-svc\r\n              servicePort: 80",
    "templateType": "kubernetes",
    "description": "da",
    "tags": "kubernetes",
    "templateWithHistId": "6e0e568b-90f7-48f0-a3c5-444baf2c****",
    "created": "2021-08-11T17:52:38+08:00",
    "updated": "2021-08-11T17:52:38+08:00"
  }
]
```

