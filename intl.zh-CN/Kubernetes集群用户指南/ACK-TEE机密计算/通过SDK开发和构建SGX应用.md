---
keyword: [Intel SGX SDK, 构建SGX应用]
---

# 通过SDK开发和构建SGX应用

本文介绍如何基于Intel SGX SDK开发和构建，并部署一个名为helloworld的SGX应用，这个应用在可信区周期性地生产消息并传递给不可信代码（缓冲区），然后输出到终端。

-   [创建加密计算托管集群](/intl.zh-CN/Kubernetes集群用户指南/ACK-TEE机密计算/创建加密计算托管集群.md)。
-   开发编译环境依赖：
    -   AliyunLinux 2或CentOS/RHEL7+
    -   Intel SGX Driver
    -   Intel SGX SDK
    -   Intel SGX PSW
    -   AESM service

## 基本原理

![](../images/p102676.png "SGX程序原理图")

SGX程序基本原理说明如下：

-   SGX应用由两部分组成：
    -   untrusted不可信区

        如果代码和数据运行在普通非加密内存区域，程序`main`入口必须在非可信区。SGX程序原理图中的`main()`和`bar()`函数均在非可信区。

    -   trusted可信区

        如果代码和数据运行在硬件加密内存区域，此区域由CPU创建且只有CPU有权限访问。SGX程序原理图中的`helloworld()`和`foo()`函数运行在可信区。

-   非可信区只能通过ECALL函数调用可信区内的函数。
-   可信区只能通过OCALL函数调用非可信区的函数。
-   ECALL函数和OCALL函数通过EDL文件声明。

## 示例

本示例中SGX应用helloworld所涉及的应用编译、镜像构建以及部署等示例源码位于[github](https://github.com/AliyunContainerService/sgx-device-plugin/tree/master/samples/hello_world)。

**示例代码目录结构**

```
sgx-device-plugin/samples/hello_world/
                                    ├── Dockerfile
                                    ├── Makefile
                                    ├── README.md
                                    └── src
                                         ├── App
                                         │   ├── App.cpp
                                         │   └── App.h
                                         ├── Enclave
                                         │   ├── Enclave.config.xml
                                         │   ├── Enclave.cpp
                                         │   ├── Enclave.edl
                                         │   ├── Enclave.h
                                         │   ├── Enclave.lds
                                         │   └── Enclave_private.pem
                                         ├── Include
                                         └── Makefile
```

**说明：** `src`代码目录和文件说明如下：

-   App目录存放不可信区域代码，包括main入口、OCALL函数内具体逻辑代码等。
-   Enclave目录存放可信区域代码，包括ECALL函数内具体逻辑代码。

    |文件|说明|
    |--|--|
    |Enclave.edl|EDL\(Enclave Description Language\)文件。|
    |Enclave.lds|Enclave linker script。|
    |Enclave\_private.pem|enclave.so的签名私钥。|
    |Enclave.config.xml|Enclave配置文件，如堆栈大小、是否开启Debug等。|
    |Enclave.h和Enclave.cpp|应用安全区代码实现。|

-   Include目录是不可信代码和可信代码共享的头文件。

1.  在命令行工具连接您创建的加密计算托管集群并编译hello\_world。

    在项目目录下执行命令make。

    ```
    make
    ```

    在项目根目录下会生成一个名为hello\_world的Binary文件。

    ```
    GEN  =>  App/Enclave_u.h
    CC   <=  App/Enclave_u.c
    CXX  <=  App/App.cpp
    LINK =>  app
    GEN  =>  Enclave/Enclave_t.h
    CC   <=  Enclave/Enclave_t.c
    CXX  <=  Enclave/Enclave.cpp
    LINK =>  enclave.so
    <EnclaveConfiguration>
        <ProdID>0</ProdID>
        <ISVSVN>0</ISVSVN>
        <StackMaxSize>0x40000</StackMaxSize>
        <HeapMaxSize>0x100000</HeapMaxSize>
        <TCSNum>10</TCSNum>
        <TCSPolicy>1</TCSPolicy>
        <!-- Recommend changing 'DisableDebug' to 1 to make the enclave undebuggable for enclave release -->
        <DisableDebug>0</DisableDebug>
        <MiscSelect>0</MiscSelect>
        <MiscMask>0xFFFFFFFF</MiscMask>
    </EnclaveConfiguration>
    tcs_num 10, tcs_max_num 10, tcs_min_pool 1
    The required memory is 3960832B.
    The required memory is 0x3c7000, 3868 KB.
    Succeed.
    SIGN =>  enclave.signed.so
    The project has been built in debug hardware mode.
    ```

    执行命令`./hello_world`运行hello\_world应用。

    ```
    ./hello_world
    ```

    返回结果如下：

    ```
    Wed May  6 06:53:33 2020
    Hello world From SGX Enclave!
    Wed May  6 06:53:34 2020
    Hello world From SGX Enclave!
    ...
    ```

    编译流程和编译后的代码目录结构说明如下：

    -   编译基本流程（Makefile）。
        1.  通过**sgx\_edger8r**工具在App/目录下生成不可信代码\(`Enclave_u.c`和`Enclave_u.h`\)，这部分生成的代码主要会调用`ECALL（sgx_ecall）`。
        2.  编译不可信部分Binary：app。
        3.  通过**sgx\_edger8r**工具在Enclave/目录下生成可信代码（`Enclave_t.c`和`Enclave_t.h`）。
        4.  编译可信动态链接库（enclave.so）。
        5.  通过**sgx\_sign**工具签名可信动态链接库（enclave.signed.so）。
        6.  结束。
    -   编译后的代码目录结构。

        ```
        sgx-device-plugin/samples/hello_world/src/
                                                 ├── hello_world      #[generated]
                                                 ├── App
                                                 │   ├── App.cpp
                                                 │   ├── App.h
                                                 │   ├── App.o        #[generated]
                                                 │   ├── Enclave_u.c  #[generated] 
                                                 │   ├── Enclave_u.h  #[generated] 
                                                 │   └── Enclave_u.o  #[generated]
                                                 ├── Enclave
                                                 │   ├── Enclave.config.xml
                                                 │   ├── Enclave.cpp
                                                 │   ├── Enclave.edl
                                                 │   ├── Enclave.h
                                                 │   ├── Enclave.lds
                                                 │   ├── Enclave.o     #[generated]
                                                 │   ├── Enclave_private.pem
                                                 │   ├── Enclave_t.c   #[generated]
                                                 │   ├── Enclave_t.h   #[generated]
                                                 │   └── Enclave_t.o   #[generated]
                                                 ├── enclave.signed.so #[generated]
                                                 ├── enclave.so        #[generated]
                                                 ├── Include
                                                 └── Makefile
        ```

2.  构建镜像并部署helloworld应用。

    编译出的应用程序必须基于**alibabatee/centos\_sgx:7**构建镜像，镜像里面已安装了SGX程序所依赖的动态链接库。

    Dockerfile文件内容如下。

    ```
    FROM alibabatee/centos_sgx:7
    
    COPY src/hello_world src/enclave.signed.so /app/
    
    WORKDIR /app
    
    ENTRYPOINT ["/app/hello_world"]
    ```

    1.  执行以下命令编译并构建镜像。

        ```
        cd sgx-device-plugin/samples/hello_world
        TARGET_IMAGE=registry-vpc.cn-shanghai.aliyuncs.com/${namespace}/${image_name}:${image_tag} make image
        docker push registry-vpc.cn-shanghai.aliyuncs.com/${namespace}/${image_name}:${image_tag}
        ```

    2.  执行以下命令部署helloworld应用。

        ```
        cat <<EOF | kubectl create -f -
        apiVersion: apps/v1
        kind: Deployment
        metadata:
          name: helloworld
          namespace: default
        spec:
          replicas: 2
          selector:
            matchLabels:
              app: helloworld
          template:
            metadata:
              labels:
                app: helloworld
            spec:
              containers:
              - command:
                - /app/hello_world
                image: {{TARGET_IMAGE}}
                imagePullPolicy: Always
                name: helloworld
                resources:
                  limits:
                    cpu: 250m
                    memory: 512Mi
                    alibabacloud.com/sgx_epc_MiB: 2
                volumeMounts:
                - mountPath: /var/run/aesmd/aesm.socket
                  name: aesmsocket
              volumes:
              - hostPath:
                  path: /var/run/aesmd/aesm.socket
                  type: Socket
                name: aesmsocket
        EOF
        ```


|文件路径|说明|示例代码|
|----|--|----|
|Encalve/Enclave.edl|EDL中声明了一个公共ECALL函数，每个SGX应用的EDL必须至少声明一个：-   `public`类型的ECALL函数。
-   `trusted {...}`内声明ECALL函数。
-   `untrusted {...}`内申明OCALL函数。

由于本例中安全区不需要向非安全区调用（OCALL），所以只声明了一个ECALL函数`ecall_hello_from_enclave`，这个ECALL函数目的是在安全区创建一个Buffer并填充helloworld，然后这个Buffer的内容拷贝到非安全的Buffer中，非安全区调用`printf`打印这个非安全Buffer内容。

|```
enclave {
    trusted {
        public void ecall_hello_from_enclave([out, size=len] char* buf, size_t len);
    };
};
``` |
|Enclave/Enclave.lds|-|```
enclave.so
{
    global:
        g_global_data_sim;
        g_global_data;
        enclave_entry;
        g_peak_heap_used;
    local:
        *;
};
``` |
|Enclave/Enclave.config.xml|-|```
<EnclaveConfiguration>
  <ProdID>0</ProdID>
  <ISVSVN>0</ISVSVN>
  <StackMaxSize>0x40000</StackMaxSize>
  <HeapMaxSize>0x100000</HeapMaxSize>
  <TCSNum>10</TCSNum>
  <TCSPolicy>1</TCSPolicy>
  <!-- Recommend changing 'DisableDebug' to 1 to make the enclave undebuggable for enclave release -->
  <DisableDebug>0</DisableDebug>
  <MiscSelect>0</MiscSelect>
  <MiscMask>0xFFFFFFFF</MiscMask>
</EnclaveConfiguration>
``` |
|Enclave/Enclave.h|这个头文件内容基本为空。|```
#ifndef _ENCLAVE_H_
#define _ENCLAVE_H_
#endif
``` |
|Enclave/Enclave.cpp|-|```
#include "Enclave.h"
#include "Enclave_t.h" /* print_string */
#include <string.h>

void ecall_hello_from_enclave(char *buf, size_t len)
{
    const char *hello = "Hello world";

    size_t size = len;
    if(strlen(hello) < len)
    {
        size = strlen(hello) + 1;
    }

    memcpy(buf, hello, size - 1);
    buf[size-1] = '\0';
}
``` |
|Enclave/Enclave\_private.pem|生成签名密钥。|```
$ openssl genrsa -out Enclave/Enclave_private.pem -3 3072
``` |
|App/App.h|-|```
#ifndef _APP_H_
#define _APP_H_

#include <assert.h>
#include <stdio.h>
#include <stdlib.h>
#include <stdarg.h>

#include "sgx_error.h"       /* sgx_status_t */
#include "sgx_eid.h"     /* sgx_enclave_id_t */

#ifndef TRUE
# define TRUE 1
#endif

#ifndef FALSE
# define FALSE 0
#endif

# define TOKEN_FILENAME   "enclave.token"
# define ENCLAVE_FILENAME "enclave.signed.so"

extern sgx_enclave_id_t global_eid;    /* global enclave id */

#if defined(__cplusplus)
extern "C" {
#endif


#if defined(__cplusplus)
}
#endif

#endif /* !_APP_H_ */
``` |
|App/App.cpp|-|```
#include <stdio.h>
#include <string.h>
#include <assert.h>

#include <time.h>
#include <ctime>

# include <unistd.h>
# include <pwd.h>
# define MAX_PATH FILENAME_MAX

#include "sgx_urts.h"
#include "App.h"
#include "Enclave_u.h"

/* Global EID shared by multiple threads */
sgx_enclave_id_t global_eid = 0;

int initialize_enclave(void)
{
    sgx_status_t ret = SGX_ERROR_UNEXPECTED;

    char enclavefile[256];
    getcwd(enclavefile, sizeof(enclavefile));
    strcat(enclavefile, "/enclave.signed.so");

    /* Call sgx_create_enclave to initialize an enclave instance */
    /* Debug Support: set 2nd parameter to 1 */
    ret = sgx_create_enclave(enclavefile, SGX_DEBUG_FLAG, NULL, NULL, &global_eid, NULL);
    if (ret != SGX_SUCCESS) {
        printf("Failed to create enclave, ret code: %d, enclave file: %s\n", ret, enclavefile);
        return -1;
    }

    return 0;
}

tm* get_time() {
    time_t rawtime;
    struct tm * timeinfo;
    time ( &rawtime );
    timeinfo = localtime ( &rawtime );
    return timeinfo;
}

/* Application entry */
int SGX_CDECL main(int argc, char *argv[])
{
    (void)(argc);
    (void)(argv);

    const size_t max_buf_len = 100;
    char buffer[max_buf_len] = {0};


    /* Initialize the enclave */
    if(initialize_enclave() < 0){
        printf("Enter a character before exit ...\n");
        getchar();
        return -1;
    }

    /* Enclave calls */
    while(1) {
        ecall_hello_from_enclave(global_eid, buffer, max_buf_len);

        printf("%s%s\n", asctime(get_time()), buffer);
        fflush(stdout);

        sleep(1);
    }

    /* Destroy the enclave */
    sgx_destroy_enclave(global_eid);

    printf("Info: SampleEnclave successfully returned.\n");

    printf("Enter a character before exit ...\n");
    getchar();
    return 0;
}
``` |

## 参考链接

-   [ACK-TEE机密计算介绍](/intl.zh-CN/Kubernetes集群用户指南/ACK-TEE机密计算/ACK-TEE机密计算介绍.md)
-   [创建加密计算托管集群](/intl.zh-CN/Kubernetes集群用户指南/ACK-TEE机密计算/创建加密计算托管集群.md)
-   [Intel® Software Guard Extensions Developer Guide](https://software.intel.com/content/www/us/en/develop/documentation/sgx-developer-guide/top.html)

