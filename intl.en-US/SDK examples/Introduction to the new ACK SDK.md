# Introduction to the new ACK SDK

The new Container Service for Kubernetes \(ACK\) SDK improves the user experience and enhances the capability of verifying sample code. The new SDK does not involve feature changes. This topic describes the differences between the new ACK SDK and the earlier ACK SDK.

|Comparison item|Earlier SDK|New SDK|
|---------------|-----------|-------|
|Design principle|SDKs for different programming languages are generated based on different templates. The template-based solutions of some programming languages are not mature. Therefore, developers have different experience with different SDKs.|SDKs for different programming languages are written in [Darabonba](https://github.com/aliyun/darabonba/blob/master/README.md). This intermediate language supports syntax verification.|
|Sample code|The sample code for different programming languages differs from each other. The correctness of the sample code cannot be ensured.|The sample code for different programming languages is written in an intermediate language based on the same logic. The sample code can be verified to ensure correctness.|
|Code hints|Code hints are provided for only API operations. No hints are provided for annotations. Developers must refer to documentation for details about parameters, returned values, and methods.|Hints are provided for API operations, fields, annotations, and returned values in the integrated development environment \(IDE\).|

