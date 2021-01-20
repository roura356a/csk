# Ingress-Nginx

This topic lists the latest changes to ingress-nginx.

## April 2020

|Version|Image address|Release date|Description|Impact|
|-------|-------------|------------|-----------|------|
|v0.30.0.1-5f89cb606-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/aliyun-ingress-controller:v0.30.0.1-5f89cb606-aliyun|April 2, 2020|-   FastCGI Backend is supported.
-   The Dynamic SSL Cert Update mode is enabled by default.
-   The feature that is used to mirror requests to backend servers is supported.
-   NGINX is upgraded to V1.17.8 and OpenResty is upgraded to V1.15.8. The operating system of base images is updated to Alpine Linux.
-   Ingress validating admission webhooks are supported.
-   The following vulnerabilities are fixed: CVE-2018-16843, CVE-2018-16844, CVE-2019-9511, CVE-2019-9513, and CVE-2019-9516.
-   Major updates:
    -   The lua-resty-waf, session-cookie-hash, and force-namespace-isolation configurations are deprecated.
    -   The data type of x-forwarded-prefix is changed from BOOLEAN to STRING.
    -   The the\_real\_ip variable in log-format will be deprecated in the next version and replaced with remote\_addr.
-   The NGINX Ingress controller is upgraded to Ingress-NGINX 0.30.0. For more information about the updates, see [Changelog](https://github.com/kubernetes/ingress-nginx/blob/master/Changelog.md).

|The upgrade may cause transient connection errors. We recommend that you upgrade Ingress-NGINX during off-peak hours.|

## October 2019

|Version|Image address|Release date|Description|Impact|
|-------|-------------|------------|-----------|------|
|v0.22.0.5-552e0db-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/aliyun-ingress-controller:v0.22.0.5-552e0db-aliyun|October 24, 2019|If you enable dynamic update for NGINX upstream servers, this provides support for wildcard domain names, whitelists, and rewrite rules.|The upgrade may cause transient connection errors. We recommend that you upgrade Ingress-NGINX during off-peak hours.|

## July 2019

|Version|Image address|Release date|Description|Impact|
|-------|-------------|------------|-----------|------|
|v0.22.0.4-5a14d4b-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/aliyun-ingress-controller:v0.22.0.4-5a14d4b-aliyun|July 18, 2019|Phased release rules are optimized and the Perl regular expressions are supported.|The upgrade may cause transient connection errors. We recommend that you upgrade Ingress-NGINX during off-peak hours.|

## April 2019

|Version|Image address|Release date|Description|Impact|
|-------|-------------|------------|-----------|------|
|v0.22.0.3-da10b7f-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/aliyun-ingress-controller:v0.22.0.3-da10b7f-aliyun|April 25, 2019|-   The NGINX Ingress controller is upgraded to Ingress-NGINX 0.22.0. For more information about the updates, see [ingress-nginx](https://github.com/kubernetes/ingress-nginx/releases).
-   If you enable dynamic update for NGINX upstream servers, this provides support for blue-green releases and canary releases.
-   Dynamic update is enabled for NGINX upstream servers by default.
-   Major updates: Capture groups are used for rewrite-target annotations. For more information, see [rewrite-target](https://kubernetes.github.io/ingress-nginx/examples/rewrite/#rewrite-target). For more information about how to upgrade Ingress-NGINX, visit [https://github.com/kubernetes/ingress-nginx/pull/3174\#issuecomment-455665710](https://github.com/kubernetes/ingress-nginx/pull/3174#issuecomment-455665710).

|The upgrade may cause transient connection errors. We recommend that you upgrade Ingress-NGINX during off-peak hours.|

## January 2019

|Version|Image address|Release date|Description|Impact|
|-------|-------------|------------|-----------|------|
|v0.20.0.2-cc39f1b-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/aliyun-ingress-controller:v0.20.0.2-cc39f1b-aliyun|January 17, 2019|-   The default number of NGINX worker processes is limited. This avoids the issue when unnecessary NGINX processes occupy host resources.
-   Different service port numbers are supported for different Ingress-NGINX versions during blue-green releases and canary releases.
-   The NGINX configuration verification failure is fixed when no pod is active in the backend servers of a new Ingress-NGINX version during a canary release.
-   The following issue is fixed: The Ingress address endpoint is not updated. This issue was caused by failed connections to the Kubernetes API server.

|The upgrade may cause transient connection errors. We recommend that you upgrade Ingress-NGINX during off-peak hours.|

## November 2018

|Version|Image address|Release date|Description|Impact|
|-------|-------------|------------|-----------|------|
|v0.20.0.1-4597ce2-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/aliyun-ingress-controller:v0.20.0.1-4597ce2-aliyun|November 29, 2018|-   The NGINX Ingress controller is upgraded to Ingress-NGINX 0.20.0. For more information about the updates, see [Kubernetes community](https://github.com/kubernetes/ingress-nginx/releases).
-   NGINX is upgraded to V1.15.6 and HTTP/2-related security vulnerabilities are fixed.
-   The path field supports regular expressions.
-   The default-http-backend service is removed and custom default backend services are supported.
-   Blacklists based on IP addresses, user agents, and referer header fields are supported.
-   The default permissions are optimized and the privileged permissions are removed.
-   Apache JServ Protocol \(AJP\) is supported.

|The upgrade may cause transient connection errors. We recommend that you upgrade Ingress-NGINX during off-peak hours.|

