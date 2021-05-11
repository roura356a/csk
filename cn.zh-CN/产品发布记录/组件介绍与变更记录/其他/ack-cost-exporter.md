---
keyword: [ack-cost-exporter, 集群成本可视化]
---

# ack-cost-exporter

本文介绍ack-cost-exporter组件相关内容的最新动态。

## 组件介绍

成本分析功能通过以下方式实现：

-   将ack-cost-exporter组件上报集群的云资源费用、实时价格及不同付费策略等预测数据作为成本分析的计费数据。
-   ack-arms-prometheus组件上报集群的计量数据。
-   ack-arms-prometheus组件数据链路根据计费数据和计量数据计算出Kubernetes集群的成本分析数据。

ack-cost-exporter包含alibaba-cloud-price-exporter与alibaba-cloud-billing-exporter两个组件：

-   alibaba-cloud-price-exporter
    -   周期性的获取集群中的实例类型、实例价格并转换为Prometheus的指标。
    -   支持公有云、混合云、多云的场景。
    -   支持包年包月、按量付费、竞价实例等多种付费类型。
-   alibaba-cloud-billing-exporter

    定时获取集群的费用账单并转换为Prometheus的指标。


## 使用说明

关于ack-cost-exporter组件的使用详情，请参见[成本分析](/cn.zh-CN/Kubernetes集群用户指南/成本分析.md)。

## 变更记录

**2021年05月**

|版本号|镜像地址|变更时间|变更内容|变更影响|
|---|----|----|----|----|
|v0.1.0|-   registry.cn-zhangjiakou.aliyuncs.com/acs/alibaba-cloud-price-exporter:v0.1.0-64dae5a-aliyun
-   registry.cn-zhangjiakou.aliyuncs.com/acs/alibaba-cloud-billing-exporter:v0.1.0-4194980-aliyun

|2021年05月11日|-   支持Pod维度的成本数据上报。
-   支持对计算资源的不同销售策略成本进行预测。

|此次升级不会对业务造成影响。|

