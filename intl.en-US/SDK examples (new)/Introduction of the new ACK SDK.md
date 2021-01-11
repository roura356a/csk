# Introduction of the new ACK SDK

The new Container Service for Kubernetes \(ACK\) SDK improves the user experience and enhances the capability of verifying demos. The new SDK does not involve feature changes. This topic describes the differences between the new ACK SDK and earlier ACK SDK.

|Item|Earlier SDK|New SDK|
|----|-----------|-------|
|Solution|SDKs for different programming languages are written by using different templates. The templates for some programming languages are not mature. Therefore, developers have different experience with SDKs for different programming languages.|SDKs for different programming languages are written in [Darabonba](https://github.com/aliyun/darabonba/blob/master/README.md). This intermediate language supports syntax verification.|
|Demo|Demos for different programming languages differ from each other. The correctness of the demos cannot be ensured.|Demos for different programming languages are written in an intermediate language based on the same logic. The demos can be verified to ensure correctness.|
|Code hints|Code hints are provided for only API operations. No hints are not provided for annotations. Developers must refer to documentation for details about parameters, returned values, and methods.|Hints are provided for API operations, fields, annotations, and returned values in the integrated development environment \(IDE\).|

