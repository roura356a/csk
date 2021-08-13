---
keyword: [Intel SGX SDK, build Intel SGX 2.0 applications]
---

# Use TEE SDK to develop and build Intel SGX 2.0 applications

This topic describes how to use trusted execution environment \(TEE\) SDK to develop, build, and deploy Intel Software Guard Extensions \(SGX\) 2.0 applications. In this topic, an application named `helloworld` is used as an example. This application generates messages in an enclave on a regular basis and sends the messages to an untrusted buffer. The messages are then sent to the terminal.

-   [Create a managed Kubernetes cluster for confidential computing](/intl.en-US/User Guide for Kubernetes Clusters/TEE-based confidential computing/Create a managed Kubernetes cluster for confidential computing.md).
-   The following environments are available for developing and compiling applications:
    -   Alibaba Cloud Linux 2
    -   Intel SGX Driver
    -   TEE SDK: a development kit provided by Alibaba Cloud to develop applications for confidential computing. This kit includes development models and programming interfaces that are consistent with those of Intel Linux SGX SDK. TEE SDK is compatible with Intel SGX SDK and Intel SGX Platform Software \(PSW\).
    -   Architectural Enclave Service Manager \(AESM\)

## How Intel SGX works

![](../images/p102676.png "How Intel SGX works")

This section describes how Intel SGX works.

-   An Intel SGX 2.0 application consists of two components:
    -   Untrusted component

        The untrusted component is an unencrypted part of the memory. If you store the code and data of an application in this part, the `main()` function \(the entry function\) of the application must also be placed in the untrusted component. In the preceding figure, the `main()` and `bar()` functions are placed in the untrusted component.

    -   Trusted component \(enclave\)

        The trusted component or enclave is an encrypted part of the memory. This component is created by the CPU, and the data and code in this component can be accessed only by the CPU. In the preceding figure, the `helloworld()` and `foo()` functions are placed in the enclave.

-   To call a function in the enclave from the untrusted component, the application must perform an enclave call \(ECALL\).
-   To call a function in the untrusted component from the enclave, the application must perform an outside call \(OCALL\).
-   ECALLs and OCALLs are declared in Enclave Definition Language \(EDL\) files.

## Example

In this example, an Intel SGX 2.0 application named `helloworld` is deployed. For more information about the source code, visit [GitHub](https://github.com/AliyunContainerService/sgx-device-plugin/tree/master/samples/hello_world). The source code includes the code for application compilation, image building, and application deployment.

**Directory hierarchy of the sample code**

```
sgx-device-plugin/samples/hello_world/
                                    ├── Dockerfile
                                    ├── Makefile
                                    ├── README.md
                                    └── src
                                         ├── App
                                         │   ├── App.cpp
                                         │   └── App.h
                                         ├── Enclave
                                         │   ├── Enclave.config.xml
                                         │   ├── Enclave.cpp
                                         │   ├── Enclave.edl
                                         │   ├── Enclave.h
                                         │   ├── Enclave.lds
                                         │   └── Enclave_private.pem
                                         └── Makefile
```

The following table describes the `src` directory and related files.

-   The App directory contains untrusted code, such as the main\(\) function \(the entry function\) and code of OCALL functions.
-   The Enclave directory contains trusted code, such as the code of ECALL functions.

    |File|Description|
    |----|-----------|
    |Enclave.edl|The EDL file|
    |Enclave.lds|The enclave linker script|
    |Enclave\_private.pem|The private key that is used to sign the enclave.so file|
    |Enclave.config.xml|The enclave configuration file that specifies parameters, such as the stack size and whether to enable debugging|
    |Enclave.h and Enclave.cpp|The code that implements the trusted component|


1.  In a CLI, connect to your managed Kubernetes cluster for confidential computing and compile hello\_world.

    Run the following command in the project directory:

    ```
    export SGX_SDK=/opt/alibaba/teesdk/intel/sgxsdk make
    ```

    A binary file named hello\_world is generated in the root directory of the project.

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

    Run the `./hello_world` command to start the hello\_world application:

    ```
    ./hello_world
    ```

    Expected output:

    ```
    Wed May  6 06:53:33 2020
    Hello world From SGX Enclave!
    Wed May  6 06:53:34 2020
    Hello world From SGX Enclave!
    ...
    ```

    The following content describes how to compile the application and shows the directory hierarchy of the compiled code:

    -   Use a makefile to compile the application.
        1.  Use the **sgx\_edger8r** tool and the `sgx_ecall` ECALL function to generate untrusted code \(`Enclave_u.c` and `Enclave_u.h`\) in the App directory.
        2.  Compile untrusted binary files in the App directory.
        3.  Use the **sgx\_edger8r** tool to generate trusted code \(`Enclave_t.c` and `Enclave_t.h`\) in the Enclave directory.
        4.  Compile the enclave.so file. It is a trusted dynamic-link library.
        5.  Use the **sgx\_sign** tool to sign the trusted dynamic-link library. The name of the trusted dynamic-link library changes to enclave.signed.so.
        6.  The application is compiled.
    -   The directory hierarchy of the compiled code.

        ```
        sgx-device-plugin/samples/hello_world/src/
                                                 ├── hello_world      #[generated]
                                                 ├── App
                                                 │   ├── App.cpp
                                                 │   ├── App.h
                                                 │   ├── App.o        #[generated]
                                                 │   ├── Enclave_u.c  #[generated] 
                                                 │   ├── Enclave_u.h  #[generated] 
                                                 │   └── Enclave_u.o  #[generated]
                                                 ├── Enclave
                                                 │   ├── Enclave.config.xml
                                                 │   ├── Enclave.cpp
                                                 │   ├── Enclave.edl
                                                 │   ├── Enclave.h
                                                 │   ├── Enclave.lds
                                                 │   ├── Enclave.o     #[generated]
                                                 │   ├── Enclave_private.pem
                                                 │   ├── Enclave_t.c   #[generated]
                                                 │   ├── Enclave_t.h   #[generated]
                                                 │   └── Enclave_t.o   #[generated]
                                                 ├── enclave.signed.so #[generated]
                                                 ├── enclave.so        #[generated]
                                                 └── Makefile
        ```

2.  Build an image and deploy the `helloworld` application.

    Build an image from the compiled application based on **alibabatee/centos\_sgx:7**. The image must contain the dynamic-link library that is required by the compiled SGX application.

    The Dockerfile contains the following content:

    ```
    FROM alibabatee/centos_sgx:7
    
    COPY src/hello_world src/enclave.signed.so /app/
    
    WORKDIR /app
    
    ENTRYPOINT ["/app/hello_world"]
    ```

    1.  Run the following commands to compile and build the image:

        ```
        cd sgx-device-plugin/samples/hello_world
        TARGET_IMAGE=registry-vpc.cn-shanghai.aliyuncs.com/${namespace}/${image_name}:${image_tag} make image
        docker push registry-vpc.cn-shanghai.aliyuncs.com/${namespace}/${image_name}:${image_tag}
        ```

    2.  Run the following command to deploy the `helloworld` application:

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


|File path|Description|Sample code|
|---------|-----------|-----------|
|Encalve/Enclave.edl|The EDL file that declares a public ECALL function. At least one of the following functions must be declared in the EDL file of an SGX application:-   An ECALL function of the `public` type.
-   `trusted {...}`declares an ECALL function.
-   `untrusted {...}`declares an OCALL function.

In this example, the application does not need to invoke an OCALL function. Therefore, only one ECALL function \(`ecall_hello_from_enclave`\) is declared. This ECALL function is used to create a buffer in the enclave and deploy the `helloworld` application. Then, the information in the buffer is copied to an untrusted buffer in the untrusted component. The application invokes `printf` in the untrusted component to print the information.

|```
enclave {
    trusted {
        public void ecall_hello_from_enclave([out, size=len] char* buf, size_t len);
    };
};
``` |
|Enclave/Enclave.lds|N/A|```
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
|Enclave/Enclave.config.xml|N/A|```
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
|Enclave/Enclave.h|In most cases, this header file is empty.|```
#ifndef _ENCLAVE_H_
#define _ENCLAVE_H_
#endif
``` |
|Enclave/Enclave.cpp|N/A|```
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
|Enclave/Enclave\_private.pem|The private key that is used to sign the enclave.so file|```
openssl genrsa -out Enclave/Enclave_private.pem -3 3072
``` |
|App/App.h|N/A|```
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
|App/App.cpp|N/A|```
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

## References

-   [TEE-based confidential computing](/intl.en-US/User Guide for Kubernetes Clusters/TEE-based confidential computing/TEE-based confidential computing.md)
-   [Create a managed Kubernetes cluster for confidential computing](/intl.en-US/User Guide for Kubernetes Clusters/TEE-based confidential computing/Create a managed Kubernetes cluster for confidential computing.md)
-   [Intel® Software Guard Extensions Developer Guide](https://software.intel.com/content/www/us/en/develop/documentation/sgx-developer-guide/top.html)

