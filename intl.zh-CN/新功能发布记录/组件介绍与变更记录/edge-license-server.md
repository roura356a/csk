---
keyword: [edge-license-server, 商业化软件, k8s]
---

# edge-license-server

edge-license-server组件是ACK集群中部署ACK商业化软件的必要组件，您必须首先安装该组件才能正常使用ACK商业化软件。本文介绍edge-license-server相关内容的最新动态。

在使用edge-license-server组件所提供的License验证管理服务前，请务必在安装此组件前完成服务角色授权，[点击此处完成授权](https://ram.console.aliyun.com/#/role/authorize?request=%7B%22Requests%22%3A%7B%22request1%22%3A%7B%22RoleName%22%3A%22AliyunCSManagedLicenseRole%22%2C%22TemplateId%22%3A%22AliyunCSManagedLicenseRole%22%7D%7D%2C%22ReturnUrl%22%3A%22https%3A%2F%2Fcs.console.aliyun.com%2F%22%2C%22Service%22%3A%22CS%22%7D)。

## 2021年02月

|版本号|镜像地址|变更时间|变更内容|
|---|----|----|----|
|v0.2.0-8aa5e14-aliyun|registry.aliyuncs.com/acs/edge-license-server:v0.2.0-8aa5e14-aliyun|2021年02月24日|在ACK集群中提供License验证服务。|

