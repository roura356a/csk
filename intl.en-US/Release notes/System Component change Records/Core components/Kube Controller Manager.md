---
keyword: Kube Controller Manager
---

# Kube Controller Manager

The Kubernetes controller manager manages the resources in a Kubernetes cluster. This topic introduces the kube-controller-manager component and its usage notes, and lists the latest changes to the component.

## Introduction

The Kubernetes controller manager is a daemon that embeds the core control loops that are shipped with Kubernetes. The Kubernetes controller manager monitors the state of the cluster through the Kubernetes API server and makes sure that the cluster runs in the desired state. The Kubernetes controller manager consists of multiple controllers that manage differnet resources. Kubernetes provides the following built-in controllers: replication controller, endpoints controller, namespace controller, and service accounts controller.

## Usage notes

The kube-controller-manager component is automatically installed. You can use it without extra configurations.

## Release notes

The kube-controller-manager component is upgraded along with the Kubernetes version. For more information, see [Overview of Kubernetes versions supported by ACK](/intl.en-US/Release notes/Kubernetes release notes/Overview of Kubernetes versions supported by ACK.md).

