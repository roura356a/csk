---
keyword: [Knative Eventing, event-driven]
---

# Overview

Knative Eventing is designed to address common demands for cloud-native development. It allows you to bind event sources with event consumers to handle events. This topic describes event sources, event handling, and event consumption of Knative Eventing.

## Features

Knative Eventing meets the common needs in cloud-native development. In addition, Knative Eventing provides an architecture for serverless event-driven mode. The architecture contains event sources, event ingesting and subscription, and event filtering. The following figure shows the event-driven architecture.

![s-6](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/6432334161/p210865.png)

-   Event sources
    -   Open source Knative provides various event sources such as Kafka and GitHub.
    -   Open source Knative also allows you to use cloud services as event sources. The cloud services include Message Service \(MNS\) and RocketMQ.
-   Event handling
    -   Knative Eventing routes events from brokers to event sinks or consumers. You can create one or more triggers to filter or subscribe to specific events.
    -   Events can be consumed by serverless applications that are managed by Knative.
-   Event consumption
    -   Automatically releases applications when images in Container Registry are updated.
    -   Automatically creates images upon code submission.
    -   Supports cron jobs and AI-assisted processing of audios and videos.

## How to start

For more information about how to use Knative Eventing to handle events, see [Deploy Knative Eventing](/intl.en-US/User Guide for Serverless Kubernetes Clusters/Knative management/Knative Eventing/Deploy Knative Eventing.md).

**Related topics**  


[Deploy Knative Eventing](/intl.en-US/User Guide for Serverless Kubernetes Clusters/Knative management/Knative Eventing/Deploy Knative Eventing.md)

