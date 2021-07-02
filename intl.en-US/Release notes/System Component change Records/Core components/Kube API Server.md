---
keyword: [Kubernetes API server, API server]
---

# Kube API Server

The Kubernetes API server is the access gateway to a Kubernetes cluster. This topic introduces the kube-apiserver component and its usage notes, and lists the latest changes to the component.

## Introduction

The Kubernetes API server validates and configures data for the API objects, which include pods, Services, and ReplicationControllers. The Kubernetes API server serves REST operations and provides a frontend to the shared state of the cluster. All other components interact through this frontend.

## Usage notes

The kube-apiserver component is automatically installed. You can use it without extra configurations.

## Release notes

The kube-apiserver component is upgraded along with the Kubernetes version. For more information, see [Overview of Kubernetes versions supported by ACK](/intl.en-US/Release notes/Kubernetes release notes/Overview of Kubernetes versions supported by ACK.md).

