---
keyword: [containerd, release note]
---

# Release notes for containerd

containerd is an industry-standard container runtime. containerd manages the entire lifecycle of containers on its host. containerd provides a simple and stable runtime for your containers. This topic describes the release notes for containerd.

For more information about the comparison between containerd and other runtimes, see [Comparison of Docker, containerd, and Sandboxed-Container](/intl.en-US/User Guide for Kubernetes Clusters/Sandboxed-Container management/Comparison of Docker, containerd, and Sandboxed-Container.md).

## August 2021

|Version|Release date|Description|Impact|
|-------|------------|-----------|------|
|1.4.8|2021-08-03|-   The following issue is fixed: Sandbox creation times out due to system overloading, which further causes an IP leak.
-   The [CVE-2021-32760](https://nvd.nist.gov/vuln/detail/CVE-2021-32760) vulnerability is fixed.

|No impact on workloads|

## June 2021

|Version|Release date|Description|Impact|
|-------|------------|-----------|------|
|1.4.6|2021-06-03|The [CVE-2021-30465](https://nvd.nist.gov/vuln/detail/CVE-2021-30465) vulnerability is fixed.|No impact on workloads|

## March 2021

|Version|Release date|Description|Impact|
|-------|------------|-----------|------|
|1.4.4|2021-03-16|containerd can be selected as the container runtime when you create a Kubernetes cluster. **Note:** The containerd runtime is in public preview.

|No impact on workloads|

**Related topics**  


[Release notes for Docker](/intl.en-US/Release notes/Release notes for Runtime/Release notes for Docker.md)

[Release notes of Sandboxed-Container](/intl.en-US/Release notes/Release notes for Runtime/Release notes of Sandboxed-Container.md)

