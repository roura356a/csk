# 如何手动安装alicloud-application-controller {#concept_dz2_tw3_r2b .concept}

在阿里云容器服务中，1.10.4及以上版本默认安装alicloud-application-controller，来提供一种基于 CRD 的分批发布的能力。

**说明：** Kubernetes最新集群已经默认安装alicloud-application-controller，旧版本集群可以通过手动安装的方式部署alicloud-application-controller。

您可通过`kubectl create -f alicloud-application-controller.yml`命令部署alicloud-application-controller，在alicloud-application-controller.yml中输入如下的编排模板。

```
apiVersion: extensions/v1beta1
kind: Deployment
metadata:
  name: alicloud-application-controller
  labels:
    owner: aliyun
    app: alicloud-application-controller
  namespace: kube-system
spec:
  replicas: 1
  selector:
    matchLabels:
      owner: aliyun
      app: alicloud-application-controller
  template:
    metadata:
      labels:
        owner: aliyun
        app: alicloud-application-controller
      annotations:
        scheduler.alpha.kubernetes.io/critical-pod: ''
    spec:
      tolerations:
      - effect: NoSchedule
        operator: Exists
        key: node-role.kubernetes.io/master
      - effect: NoSchedule
        operator: Exists
        key: node.cloudprovider.kubernetes.io/uninitialized
      containers:
        - name: alicloud-application-controller
          image: registry.cn-hangzhou.aliyuncs.com/acs/aliyun-app-lifecycle-manager:0.1-93095ba
          imagePullPolicy: IfNotPresent
      serviceAccount: admin
```

