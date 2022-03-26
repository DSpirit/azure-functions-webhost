# Azure Functions Webhost on Windows
- Server: Windows Server 2022 (LTSC)
- Framework: .NET Core 3.1
- Language: C#

If you want to use Azure Functions with Docker on a Windows-based platform, this repository provides a webhost template to host your function apps.
As the [official repository](https://github.com/Azure/azure-functions-docker/tree/dev/host/3.0/nanoserver) stopped getting updated after Windows Server 1909, I decided to create this example with Windows Server 2022.

## Get your Azure Functions Webhost Version Number
Browse the releases for find your desired version:
https://github.com/Azure/azure-functions-host/releases

Here, we use release [v3.5.2](https://github.com/Azure/azure-functions-host/releases/tag/v3.5.2)

## Build an image
Build your function webhost image as follows:

`docker build -t functions.webhost --build-arg WINDOWS_VERSION=ltsc2022 --build-arg HOST_VERSION=3.5.2 .`

## Run the image
`docker run -it functions.webhost`

## Customize
In this example, the secrets could also be maintained filed-based i.e. you can specify your functions secret in the [host.json](src/Secrets/host.json) in order to execute your functions with the code query parameter as follows:

`POST http://localhost:7071/api/function1?code=TXkgcHJldHR5IGxpdHRsZSBzZWNyZXQ=`

Additionally, you can also provide some custom certificates and add them to your host.
Both features can be activated by adding these lines to your [Dockerfile](src/Dockerfile):

``` Docker
 # --- Add Certificates if necessary ---
 ADD Certificates\\dummy.cer C:\\certs\\dummy.cer
 USER ContainerAdministrator
 RUN certoc.exe -addstore root C:\\certs\\dummy.cer
 USER ContainerUser
 # --- File based Secret Handling if required ---
 ENV AzureWebJobsSecretStorageType=files
 ENV FUNCTIONS_SECRETS_PATH=C:\Secrets
 ADD Secrets\\host.json C:\\Secrets\\host.json
```
## Links
- https://github.com/Azure/azure-functions-docker
- https://github.com/Azure/azure-functions-host
- https://hub.docker.com/_/microsoft-dotnet-aspnet
