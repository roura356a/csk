---
keyword: [指定ECS规格, 创建ECI Pod]
---

# 通过指定ECS规格创建ECI Pod

在某些业务场景下，存在着特殊的规格需求，例如：GPU、增强的网络能力、高主频、本地盘等。ECI支持通过指定ECS规格进行创建。本文介绍如何通过指定ECS规格创建ECI Pod。

## 规格说明

ECI指定规格完全参考ECS规格定义。ECI单价与对应规格的ECS价格保持一致，按秒计费。详情请参见[ECS价格计算器](https://www.alibabacloud.com/pricing-calculator#/add/980738/vm_intl/vm_intl)。

您可以通过[ECS 实例规格可购买地域总览](https://ecs-buy.aliyun.com/instanceTypes#/instanceTypeByRegion)，查询每个地域和可用区具体支持的ECS规格信息。目前支持的实例规格族如下所示：

-   通用型（1:4）实例规格族g6、g5、sn2ne（网络增强）
-   计算型（1:2）实例规格族c6、c5、sn1ne（网络增强）
-   内存型（1:8）实例规格族r6、r5、se1ne（网络增强）
-   密集计算型（1:1）实例规格族ic5
-   高主频计算型（1:2）实例规格族hfc6、hfc5
-   高主频通用型（1:4）实例规格族hfg6、hfg5
-   GPU计算型实例规格族gn6i、gn6v、gn5i、gn5（不支持本地存储）
-   突发性能实例规格族t6、t5

## 使用示例

通过在Pod定义中设置annotations: k8s.aliyun.com/eci-use-specs，可以配置多个规格，以逗号分割。

```
apiVersion: apps/v1beta2
kind: Deployment
metadata:
  name: nginx
  labels:
    app: nginx
spec:
  replicas: 2
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
      annotations:
        k8s.aliyun.com/eci-use-specs : "ecs.c5.large"  #根据需要替换ECS规格
    spec:
      containers:
      - name: nginx
        image: nginx:1.7.9
        ports:
        - containerPort: 80
```

