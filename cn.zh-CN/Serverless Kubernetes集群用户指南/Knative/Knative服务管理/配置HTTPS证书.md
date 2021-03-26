---
keyword: [HTTPS证书, 证书管理]
---

# 配置HTTPS证书

ASK Knative使用SLB作为Gateway。除了HTTP以外，Gateway还提供HTTPS功能。Knative默认给Gateway生成一个自签名的HTTPS证书，没有域名限制，可用于测试。在使用Knative部署线上服务时，您需要创建HTTPS证书，并将Knative Gateway Service的证书ID修改为创建的证书ID。本文介绍如何查看、创建和使用HTTPS证书。

Gateway的详细介绍请参见[Knative Gateway](/cn.zh-CN/Serverless Kubernetes集群用户指南/Knative/Knative服务管理/Knative Gateway.md)。

## 查看默认证书

1.  登录[传统型负载均衡CLB控制台](https://slb.console.aliyun.com/slb)。

2.  在左侧导航栏，选择**传统型负载均衡 CLB（原SLB）** \> **证书管理**。

3.  在**证书管理**页面找到knative-default-gateway-cert，knative-default-gateway-cert就是Knative的默认证书。默认证书由Knative自动生成，可用于测试。


## 创建证书

您可以通过使用阿里云签发的证书或上传非阿里云签发的证书的方式创建证书，详细介绍请参见[选择阿里云签发证书](/cn.zh-CN/传统型负载均衡CLB/CLB用户指南/证书管理/创建证书/选择阿里云签发证书.md)和[上传非阿里云签发证书](/cn.zh-CN/传统型负载均衡CLB/CLB用户指南/证书管理/创建证书/上传非阿里云签发证书.md)。

## 使用创建的证书

1.  登录[传统型负载均衡CLB控制台](https://slb.console.aliyun.com/slb)。

2.  在左侧导航栏，选择**传统型负载均衡 CLB（原SLB）** \> **证书管理**。

3.  在**证书管理**页面鼠标悬停至目标证书ID，单击目标证书**证书名称/ID**列的![复制](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/5431815061/p171208.png)，复制证书ID。

4.  将Knative Gateway Service的`service.beta.kubernetes.io/alibaba-cloud-loadbalancer-cert-id`的值修改为步骤[3](#step_riv_0nw_nb8)中复制的证书ID。通过这个Annotation指定证书ID后，您就可以使用创建的证书提供服务。

    ```
    apiVersion: v1
    kind: Service
    metadata:
      annotations:
        service.beta.kubernetes.io/alibaba-cloud-loadbalancer-protocol-port: "https:443"
        service.beta.kubernetes.io/alibaba-cloud-loadbalancer-cert-id: "${YOUR_CERT_ID}"
      name: nginx
    spec:
      ports:
      - port: 443
        protocol: TCP
        targetPort: 80
      selector:
        run: nginx
      type: LoadBalancer
    ```


## 管理多域名证书

阿里云SLB支持配置多个证书，每一个证书可以服务一组域名，您可以配置多组证书和域名。

**说明：** 更多域名管理操作请参见[添加扩展域名](/cn.zh-CN/传统型负载均衡CLB/CLB用户指南/监听/扩展域名/添加扩展域名.md)。

1.  登录[传统型负载均衡CLB控制台](https://slb.console.aliyun.com/slb)。

2.  在左侧导航栏，选择**实例** \> **实例管理**。

3.  在实例管理页面，单击负载均衡实例的ID。

4.  在**监听**页签找到已创建的HTTPS监听，选择操作列下的 **![更多](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/8772129951/p136776.png)** \> **扩展域名管理**。

    **说明：** 在**监听**页签监听的**前端协议/端口**列显示HTTPS，则表示该监听是HTTPS监听。

5.  在扩展域名管理页面，单击**添加扩展域名**，输入扩展域名，选择服务器证书。

    域名只能由字母、数字、连字符（-）和点（.）组成，首位必须是字母或数字。合法域名检测，请参见[阿里云域名检测工具](https://zijian.aliyun.com)。

6.  单击**确定**。


