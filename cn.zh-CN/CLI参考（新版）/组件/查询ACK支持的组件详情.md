# 查询ACK支持的组件详情

通过执行命令`arc-cs addon get --region <region>`查询容器服务ACK支持的组件详情。

关于该命令的详细参数说明，请参见[查询集群组件详情](/cn.zh-CN/API参考/组件/查询集群组件详情.md)。

## CLI示例

请求示例：

```
arc-cs addon get  --region cn-beijing
```

返回结果示例：

```
{
  "componentGroups": [
    {
      "groupName": "core",
      "items": [
        {
          "name": "cloud-controller-manager"
        }
      ]
    },
    {
      "groupName": "application",
      "items": [
        {
          "name": "alicloud-application-controller"
        },
        {
          "name": "appcenter"
        },
        {
          "name": "progressive-delivery-tool"
        },
        {
          "name": "tiller"
        }
      ]
    },
    {
      "groupName": "monitor",
      "items": [
        {
          "name": "metrics-server"
        },
        {
          "name": "logtail-ds"
        },
        {
          "name": "ags-metrics-collector"
        },
        {
          "name": "ack-node-problem-detector"
        },
        {
          "name": "arms-prometheus"
        },
        {
          "name": "alicloud-monitor-controller"
        }
      ]
    },
    {
      "groupName": "storage",
      "items": [
        {
          "name": "flexvolume"
        },
        {
          "name": "alicloud-disk-controller"
        },
        {
          "name": "csi-plugin"
        },
        {
          "name": "csi-provisioner"
        }
      ]
    },
    {
      "groupName": "network",
      "items": [
        {
          "name": "kube-flannel-ds"
        },
        {
          "name": "terway"
        },
        {
          "name": "terway-eni"
        },
        {
          "name": "terway-eniip"
        },
        {
          "name": "nginx-ingress-controller"
        },
        {
          "name": "coredns"
        },
        {
          "name": "managed-kube-proxy"
        }
      ]
    },
    {
      "groupName": "security",
      "items": [
        {
          "name": "aliyun-acr-credential-helper"
        },
        {
          "name": "kritis-validation-hook"
        },
        {
          "name": "security-inspector"
        },
        {
          "name": "gatekeeper"
        }
      ]
    },
    {
      "groupName": "other",
      "items": [
        {
          "name": "cgroups-controller"
        },
        {
          "name": "cgpu"
        },
        {
          "name": "arena"
        }
      ]
    }
  ],
  "standardComponents": {
    "ack-arena": {
      "name": "ack-arena",
      "version": "0.5.0",
      "disabled": false,
      "required": "false",
      "config": "",
      "value": "",
      "description": "\napiVersion: v2\n\nname: ack-arena\n\ndescription: Running Deep Learning Containers on ACK\n\n# This is the chart version. This version number should be incremented each time you make changes\n\n# to the chart and its templates, including the app version.\n\nversion: 0.5.0\n\n# This is the version number of the application being deployed. This version number should be\n\n# incremented each time you make changes to the application.\n\nappVersion: 0.5.0\n\nhome: https://github.com/kubeflow/arena\n\nintroduction:\n\n- 使用Arena运行深度学习负载\n\n- Running Deep Learning Containers on ACK\n\ndoc:\n\n- https://github.com/kubeflow/arena/blob/master/README_cn.md\n\n- https://github.com/kubeflow/arena/blob/master/README.md\n\nchangelog:\n\n- https://github.com/kubeflow/arena/releases\n\n- https://github.com/kubeflow/arena/releases\n\nkeywords:\n\n- category:data\n\n- arena\n\n- namespace:kube-system\n\n- releaseName:ack-arena\n",
      "properties": null
    },
    "ack-kubernetes-cronhpa-controller": {
      "name": "ack-kubernetes-cronhpa-controller",
      "version": "v1.4.0-fc4f6060-aliyun",
      "disabled": false,
      "required": "false",
      "config": "",
      "value": "",
      "description": "name: ack-kubernetes-cronhpa-controller\nintroduction:\n  zh: 使用 ack-kubernetes-cronhpa-controller 实现应用负载定时伸缩\n  en: ask-kubernetes-cronhpa-controller is a kubernetes cron horizontal pod autoscaler controller using crontab like scheme.\ndoc:\n  zh: https://www.alibabacloud.com/help/zh/doc-detail/151557.htm\n  en: https://www.alibabacloud.com/help/en/doc-detail/151557.htm\nchangelog:\n  zh: https://www.alibabacloud.com/help/zh/doc-detail/151557.htm\n  en: https://www.alibabacloud.com/help/en/doc-detail/151557.htm\nkeywords:\n- cronhpa\n- hpa\n- namespace:kube-system\n- releaseName:arms-prom\n- category:ops\n",
      "properties": null
    },
    "ack-node-problem-detector": {
      "name": "ack-node-problem-detector",
      "version": "1.2.1",
      "disabled": false,
      "required": "false",
      "config": "",
      "value": "",
      "description": "name: ack-node-problem-detector\nintroduction:\n  zh: 节点故障检测以及上报至事件中心的插件\ndoc:\n  zh: https://help.aliyun.com/document_detail/178479.htm\nchangelog:\n  zh: https://help.aliyun.com/document_detail/178479.htm\ndescription: this chart  aims to make various node problems visible to the upstream layers in cluster management stack,and  told people where is the problem on time.\nappVersion: \"0.8.0\"\nversion: 1.2.1\nkubeVersion: \">=1.8.0-0\"\nkeywords:\n  - ack-node-problem-detector\n  - eventer\n  - namespace:kube-system\n  - releaseName:ack-node-problem-detector\n  - category:ops\nhome: https://github.com/AliyunContainerService/node-problem-detector\nmaintainers:\n  - name: carson\n    email: wb-gq555900@alibaba-inc.com\n  - name: 莫源\n    email: zhongwei.lzw@alibaba-inc.com",
      "properties": null
    },
    "aesm": {
      "name": "aesm",
      "version": "2.7.1-4a8c95b-aliyun",
      "disabled": false,
      "required": "false",
      "config": "",
      "value": "",
      "description": "name: Intel SGX AESM\nhome: \nicon: \nintroduction: #必填\n  zh: Intel SGX AESM，提供了SGX1 Enclave启动支持，EPID配置和证明，以及一些平台相关的服务。\n  en: Intel® SGX AESM, which provides Legacy Launch Support, EPID Provisioning and Attestation, and Platform Services.\ndoc:  \n  zh: https://help.aliyun.com/document_detail/177707.html\n  en: https://help.aliyun.com/document_detail/177707.html\nchangelog:    #必填\n  zh: https://help.aliyun.com/document_detail/177707.html\n  en: https://help.aliyun.com/document_detail/177707.html\nkeywords:\n- Intel\n- SGX\n- namespace:kube-system\n- releaseName:aesm\n- category:ACK-TEE",
      "properties": null
    },
    "ags-binary-install": {
      "name": "ags-binary-install",
      "version": "v1.0.0.1-00000001-aliyun",
      "disabled": false,
      "required": "false",
      "config": "",
      "value": "",
      "description": "ags-binary-install",
      "properties": null
    },
    "ags-metrics-collector": {
      "name": "ags-metrics-collector",
      "version": "v1.0.0.2-cc3b2d6-aliyun",
      "disabled": false,
      "required": "false",
      "config": "",
      "value": "",
      "description": "\nintroduction:\n  zh: 基因服务监控组件\n  en: Gene service monitoring component\ndoc:\n  zh: https://help.aliyun.com/document_detail/156348.html\nchangelog:\n  zh: https://help.aliyun.com/document_detail/156348.html\n",
      "properties": null
    },
    "alicloud-application-controller": {
      "name": "alicloud-application-controller",
      "version": "v0.1.0.1-f832bed-aliyun",
      "disabled": false,
      "required": "true",
      "config": "",
      "value": "",
      "description": "alicloud-application-controller",
      "properties": null
    },
    "alicloud-disk-controller": {
      "name": "alicloud-disk-controller",
      "version": "v1.14.8.51-842f0a81-aliyun",
      "disabled": false,
      "required": "false",
      "config": "",
      "value": "",
      "description": "alicloud-disk-controller",
      "properties": null
    },
    "alicloud-monitor-controller": {
      "name": "alicloud-monitor-controller",
      "version": "v1.4.0-49ff2362-aliyun",
      "disabled": false,
      "required": "true",
      "config": "",
      "value": "",
      "description": "name: alicloud-monitor-controller\nintroduction:\n  zh: 监控应用容器的生命周期和状态变化\n  en: Monitoring application containers' lifecycle and state changes\ndoc:\n  zh: https://help.aliyun.com/document_detail/176514.html\n  en:\nchangelog:\n  zh: https://help.aliyun.com/document_detail/176514.html\n  en:\nkeywords:\n- namespace: kube-system\n- releaseName: alicloud-monitor-controller",
      "properties": null
    },
    "aliyun-acr-credential-helper": {
      "name": "aliyun-acr-credential-helper",
      "version": "v20.12.28.0-062ac99-aliyun",
      "disabled": false,
      "required": "true",
      "config": "",
      "value": "",
      "description": "name: aliyun-acr-credential-helper\nintroduction: \n  zh: 使用 aliyun-acr-credential-helper 进行镜像拉取\n  en: Using aliyun-acr-credential-helper to implement private image pulling without credential setting\ndoc:         \n  zh: https://help.aliyun.com/document_detail/159750.html?spm=5176.10695662.1996646101.searchclickresult.dede56d4WTiNdU\n  en: https://www.alibabacloud.com/help/doc-detail/144049.htm?spm=a2c63.p38356.b99.38.be135889E2TbLg\nchangelog:   \n  zh: https://help.aliyun.com/document_detail/177224.htm·\n  en: \nkeywords:\n- private image\n- namespace:kube-system\n- releaseName: aliyun-acr-credential-helper",
      "properties": null
    },
    "aliyun-log-controller": {
      "name": "aliyun-log-controller",
      "version": "0.16.19.0-5be7ad3-aliyun",
      "disabled": false,
      "required": "false",
      "config": "",
      "value": "",
      "description": "aliyun-log-controller",
      "properties": null
    },
    "appcenter": {
      "name": "appcenter",
      "version": "v1.0.1.4-2c25021-aliyun",
      "disabled": false,
      "required": "false",
      "config": "",
      "value": "",
      "description": "\nintroduction:\n  zh: 使用 appcenter 管理多集群应用\n  en: Using appcenter to manage applications in multiple clusters\ndoc:\n  zh: https://help.aliyun.com/document_detail/169921.html\nchangelog:\n  zh: https://help.aliyun.com/document_detail/176183.html\n",
      "properties": null
    },
    "arms-prometheus": {
      "name": "arms-prometheus",
      "version": "0.1.5",
      "disabled": false,
      "required": "false",
      "config": "",
      "value": "",
      "description": "appVersion: 1.0.3\ndescription: ARMS Prometheus Operator\nhome: https://www.aliyun.com/product/arms\nicon: logo.png\nintroduction:\n- 使用 ARMS Prometheus 实现容器服务集群监控\n- Using ARMS Prometheus to implement ACK cluster monitoring\ndoc:\n- https://help.aliyun.com/document_detail/161304.html\n- https://www.alibabacloud.com/help/doc-detail/161304.html\nchangelog:\n- https://help.aliyun.com/document_detail/161820.html\n- https://www.alibabacloud.com/help/doc-detail/161820.html\nkeywords:\n- arms\n- prometheus\n- namespace:arms-prom\n- releaseName:arms-prom\n- category:ops\nname: ack-arms-prometheus\nversion: 0.1.5\n",
      "properties": null
    },
    "cloud-controller-manager": {
      "name": "cloud-controller-manager",
      "version": "v1.9.3.339-g9830b58-aliyun",
      "disabled": false,
      "required": "true",
      "config": "",
      "value": "",
      "description": "\nname: Cloud Controller Manager\nintroduction:\n  zh: 使用Cloud Controller Manager为k8s应用创建负载均衡，管理节点路由条目\n  en: Using Cloud Controller Manager to create load balancers for k8s applications and manage node route entries\ndoc:\n  zh: https://help.aliyun.com/document_detail/181518.html\n  en: https://www.alibabacloud.com/help/doc-detail/86531.htm\nchangelog:\n  zh: https://help.aliyun.com/document_detail/94925.html\n  en: https://www.alibabacloud.com/help/doc-detail/94925.htm\n",
      "properties": null
    },
    "coredns": {
      "name": "coredns",
      "version": "v1.6.2",
      "disabled": false,
      "required": "true",
      "config": "",
      "value": "",
      "description": "remove resource limit",
      "properties": null
    },
    "csi-plugin": {
      "name": "csi-plugin",
      "version": "v1.18.8.45-1c5d2cd1-aliyun",
      "disabled": false,
      "required": "false",
      "config": "",
      "value": "",
      "description": "csi-plugin",
      "properties": null
    },
    "csi-provisioner": {
      "name": "csi-provisioner",
      "version": "v1.18.8.45-1c5d2cd1-aliyun",
      "disabled": false,
      "required": "false",
      "config": "",
      "value": "",
      "description": "csi-provisioner",
      "properties": null
    },
    "dedicated-kube-proxy": {
      "name": "dedicated-kube-proxy",
      "version": "v1.18.8-aliyun.1",
      "disabled": false,
      "required": "true",
      "config": "",
      "value": "",
      "description": "1.18.8 dedicated-kube-proxy",
      "properties": null
    },
    "flexvolume": {
      "name": "flexvolume",
      "version": "v1.14.8.109-649dc5a-aliyun",
      "disabled": false,
      "required": "false",
      "config": "",
      "value": "",
      "description": "flexvolume",
      "properties": null
    },
    "gatekeeper": {
      "name": "gatekeeper",
      "version": "v3.1.0.11-24bab09-aliyun",
      "disabled": false,
      "required": "false",
      "config": "",
      "value": "",
      "description": "\nintroduction:\n  zh: 使用 gatekeeper 管理和应用 OPA 策略\n  en: Using gatekeeper to manage and apply OPA policy\ndoc:\n  zh: https://help.aliyun.com/document_detail/180803.html\n  en: https://www.alibabacloud.com/help/doc-detail/180803.html\nchangelog:\n  zh: https://help.aliyun.com/document_detail/180804.html\n  en: https://www.alibabacloud.com/help/doc-detail/180804.html\n",
      "properties": null
    },
    "kritis-validation-hook": {
      "name": "kritis-validation-hook",
      "version": "v0.2.7.2-g5fa671a-aliyun",
      "disabled": false,
      "required": "false",
      "config": "",
      "value": "",
      "description": "\nintroduction:\n  zh: 使用 kritis-validation-hook 实现验证镜像签名\n  en: Using kritis-validation-hook to verifying image signature\ndoc:\n  zh: https://help.aliyun.com/document_detail/157328.html\n  en: https://www.alibabacloud.com/help/doc-detail/157328.html\nchangelog:\n  zh: https://help.aliyun.com/document_detail/157327.html\n  en: https://www.alibabacloud.com/help/doc-detail/157327.html\n",
      "properties": null
    },
    "kube-flannel-ds": {
      "name": "kube-flannel-ds",
      "version": "v0.11.0.2-g6e46593e-aliyun",
      "disabled": false,
      "required": "false",
      "config": "",
      "value": "",
      "description": "flannel",
      "properties": null
    },
    "logtail-ds": {
      "name": "logtail-ds",
      "version": "v0.16.52.2-f161bec-aliyun",
      "disabled": false,
      "required": "false",
      "config": "",
      "value": "",
      "description": "aliyun-log-controller",
      "properties": null
    },
    "metrics-server": {
      "name": "metrics-server",
      "version": "v0.3.8.3-d658416-aliyun",
      "disabled": false,
      "required": "true",
      "config": "",
      "value": "",
      "description": "metrics-server",
      "properties": null
    },
    "migrate-controller": {
      "name": "migrate-controller",
      "version": "v1.0.1.2-a-e616322-aliyun",
      "disabled": false,
      "required": "false",
      "config": "",
      "value": "",
      "description": "\nName: ACK Migrate Controller\nintroduction:\n  zh: \"使用 migrate controller(velero) 备份恢复集群应用\"\n  en: \"Using migrate controller to backup and restore applications in kubernetes clusters\"\nchangelog: \n- https://www.aliyun.com/\n",
      "properties": null
    },
    "nginx-ingress-controller": {
      "name": "nginx-ingress-controller",
      "version": "v0.30.0.2-9597b3685-aliyun",
      "disabled": false,
      "required": "true",
      "config": "",
      "value": "",
      "description": "name: \"Nginx Ingress Controller\"\nhome: https://kubernetes.github.io/ingress-nginx/\nintroduction:\n  zh: \"根据ingress资源动态更新nginx配置文件\"\n  en: \"update nginx configuration file according to k8s ingress resouce\"\ndoc:\n  zh: https://help.aliyun.com/document_detail/86533.html\n  en: https://www.alibabacloud.com/help/doc-detail/86535.html\nchangelog:\n  zh: https://help.aliyun.com/document_detail/160658.html\n  en: https://www.alibabacloud.com/help/zh/doc-detail/160658.htm\nkeywords:\n- ingress\n- nginx",
      "properties": null
    },
    "progressive-delivery-tool": {
      "name": "progressive-delivery-tool",
      "version": "v1.0.3.7-ea68c41-aliyun",
      "disabled": false,
      "required": "false",
      "config": "",
      "value": "",
      "description": "\nintroduction:\n  zh: 使用 progressive-delivery-tool 管理应用灰度发布\n  en: Using progressive-delivery-tool to manage applications‘s canary delivery strategy\ndoc:\n  zh: https://help.aliyun.com/document_detail/181383.html\nchangelog:\n  zh: https://help.aliyun.com/document_detail/181393.html\n",
      "properties": null
    },
    "resource-controller": {
      "name": "resource-controller",
      "version": "v1.1.0-e7388cb",
      "disabled": false,
      "required": "false",
      "config": "",
      "value": "",
      "description": "\nintroduction:\n  zh: CPU拓扑感知调度\n  en: CPU topology aware scheduling\ndoc:\n  zh: https://help.aliyun.com/document_detail/178168.htm\nchangelog:\n  zh: https://help.aliyun.com/document_detail/178168.htm\n",
      "properties": null
    },
    "sandboxed-container-controller": {
      "name": "sandboxed-container-controller",
      "version": "v1.1.1-55d545f-aliyun",
      "disabled": false,
      "required": "false",
      "config": "",
      "value": "",
      "description": "name: sandboxed-container-controller\nhome: \nicon: \nintroduction: #必填\n  zh: sandboxed-container-controller,可以帮助在安全沙箱中实现NAS/云盘直挂\n  en: sandboxed-container-controller can help to achive nas and cloud disk pass through in ack sandboxed container.\ndoc:  \n  zh: https://help.aliyun.com/document_detail/160311.html\n  en: https://help.aliyun.com/document_detail/160311.html\nchangelog:    #必填\n  zh: https://help.aliyun.com/document_detail/160311.html\n  en: https://help.aliyun.com/document_detail/160311.html\nkeywords:\n- Sandboxed-Container\n- namespace:kube-system\n- releaseName:sandboxed-container-controller\n- category:Sandboxed-Container",
      "properties": null
    },
    "sandboxed-container-helper": {
      "name": "sandboxed-container-helper",
      "version": "v1.0.0-7a70086-aliyun",
      "disabled": false,
      "required": "false",
      "config": "",
      "value": "",
      "description": "name: sandboxed-container-helper\nhome: \nicon: \nintroduction: #必填\n  zh: sandboxed-container-helper是针对ACK全沙箱容器的诊断运维工具\n  en: sandboxed-container-helper is a operation tool dedicated for ack sandboxed container\ndoc:  \n  zh: https://help.aliyun.com/document_detail/165057.html\n  en: https://help.aliyun.com/document_detail/165057.html\nchangelog:    #必填\n  zh: https://help.aliyun.com/document_detail/165057.html\n  en: https://help.aliyun.com/document_detail/165057.html\nkeywords:\n- Sandboxed-Container\n- namespace:kube-system\n- releaseName:sandboxed-container-helper\n- category:Sandboxed-Container",
      "properties": null
    },
    "security-inspector": {
      "name": "security-inspector",
      "version": "v0.3.0.2-gcb49252-aliyun",
      "disabled": false,
      "required": "false",
      "config": "",
      "value": "",
      "description": "\nintroduction:\n  zh: 使用 security-inspector 实现集群安全扫描\n  en: Using security-inspector to scan security risks\ndoc:\n  zh: https://help.aliyun.com/document_detail/173306.html\n  en: https://www.alibabacloud.com/help/doc-detail/173306.html\nchangelog:\n  zh: https://help.aliyun.com/document_detail/173307.html\n  en: https://www.alibabacloud.com/help/doc-detail/173307.html\n",
      "properties": null
    },
    "sgx-device-plugin": {
      "name": "sgx-device-plugin",
      "version": "v1.0.0-5f5b5ef-aliyun",
      "disabled": false,
      "required": "false",
      "config": "",
      "value": "",
      "description": "name: sgx-device-plugin\nhome: \nicon: \nintroduction: #必填\n  zh: sgx-device-plugin,可以让您更容易的在容器中使用Intel SGX.\n  en: sgx-device-plugin, making it easier to run SGX applications inside a container.\ndoc:  \n  zh: https://help.aliyun.com/document_detail/177706.html\n  en: https://help.aliyun.com/document_detail/177706.html\nchangelog:    #必填\n  zh: https://help.aliyun.com/document_detail/177706.html\n  en: https://help.aliyun.com/document_detail/177706.html\nkeywords:\n- Intel\n- SGX\n- device-plugin\n- namespace:kube-system\n- releaseName:sgx-device-plugin\n- category:ACK-TEE",
      "properties": null
    },
    "terway": {
      "name": "terway",
      "version": "v1.0.10.301-g0115576-aliyun",
      "disabled": false,
      "required": "false",
      "config": "",
      "value": "",
      "description": "name: terway\nhome: https://help.aliyun.com/document_detail/97467.html\nicon: logo.png\nintroduction:\n  zh: Terway网络插件\n  en: Terway network plugin\ndoc:\n  zh: https://help.aliyun.com/document_detail/97467.html\n  en: https://www.alibabacloud.com/help/doc-detail/97467.html\nchangelog:\n  zh: https://help.aliyun.com/document_detail/113090.html\n  en: https://www.alibabacloud.com/help/doc-detail/113090.html\nkeywords:\n- Terway",
      "properties": null
    },
    "terway-eni": {
      "name": "terway-eni",
      "version": "v1.0.10.301-g0115576-aliyun",
      "disabled": false,
      "required": "false",
      "config": "",
      "value": "",
      "description": "name: terway-eni\nhome: https://help.aliyun.com/document_detail/97467.html\nicon: logo.png\nintroduction:\n  zh: Terway网络插件\n  en: Terway network plugin\ndoc:\n  zh: https://help.aliyun.com/document_detail/97467.html\n  en: https://www.alibabacloud.com/help/doc-detail/97467.html\nchangelog:\n  zh: https://help.aliyun.com/document_detail/113090.html\n  en: https://www.alibabacloud.com/help/doc-detail/113090.html\nkeywords:\n- Terway",
      "properties": null
    },
    "terway-eniip": {
      "name": "terway-eniip",
      "version": "v1.0.10.301-g0115576-aliyun",
      "disabled": false,
      "required": "false",
      "config": "",
      "value": "",
      "description": "name: terway-eniip\nhome: https://help.aliyun.com/document_detail/97467.html\nicon: logo.png\nintroduction:\n  zh: Terway网络插件\n  en: Terway network plugin\ndoc:\n  zh: https://help.aliyun.com/document_detail/97467.html\n  en: https://www.alibabacloud.com/help/doc-detail/97467.html\nchangelog:\n  zh: https://help.aliyun.com/document_detail/113090.html\n  en: https://www.alibabacloud.com/help/doc-detail/113090.html\nkeywords:\n- Terway",
      "properties": null
    },
    "workflow-controller": {
      "name": "workflow-controller",
      "version": "v2.3.4.0-bd49391-aliyun",
      "disabled": false,
      "required": "false",
      "config": "",
      "value": "",
      "description": "workflow-controller",
      "properties": null
    }
  }
}
```

