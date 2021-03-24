# progressive-delivery-tool

This topic describes the features of the progressive-delivery-tool component and lists the latest changes to this component.

## Overview

Container Service for Kubernetes \(ACK\) provides the progressive-delivery-tool component to support canary releases of your applications. A canary release allows you run tests for an application before you publish the application to the production environment. You can manage the network traffic that is incrementally routed to the canary version. This allows you to progressively release the new version for all users and minimize the potential risks of the new version within a limited scope. If the canary version fails specified health checks, quick rollback is supported.

## Release notes

|Version|Image address|Release date|Description|
|-------|-------------|------------|-----------|
|v1.0.3.6-79c468b-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/appcenter-installer:v1.0.3.6-79c468b-aliyun|August 26, 2020|Releases the progressive-delivery-tool component that is used for canary deployments.|

