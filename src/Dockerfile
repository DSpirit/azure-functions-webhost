# escape=`
ARG WINDOWS_VERSION
ARG HOST_VERSION

# Installer image
FROM mcr.microsoft.com/windows/servercore:$WINDOWS_VERSION AS installer-env
ARG WINDOWS_VERSION 
ARG HOST_VERSION
ENV HOST_VERSION ${HOST_VERSION}

RUN powershell -Command `
    Invoke-WebRequest -OutFile webhost.zip https://github.com/Azure/azure-functions-host/releases/download/v$Env:HOST_VERSION/Functions.$Env:HOST_VERSION.zip; `
    Expand-Archive webhost.zip -DestinationPath C:\webhost; `
    Move-Item C:\webhost\$Env:HOST_VERSION\64bit\ -Destination C:\; `
    Rename-Item C:\64bit -NewName C:\runtime; `
    Remove-Item C:\webhost -Recurse
    
FROM mcr.microsoft.com/dotnet/aspnet:3.1-nanoserver-$WINDOWS_VERSION

COPY --from=installer-env ["C:\\runtime", "C:\\runtime"]
COPY --from=installer-env /Windows/system32/certoc.exe .

# --- Add Certificates if necessary ---
# ADD Certificates\\dummy.cer C:\\certs\\dummy.cer
# USER ContainerAdministrator
# RUN certoc.exe -addstore root C:\\certs\\dummy.cer
# USER ContainerUser

# --- File based Secret Handling if required ---
# ENV AzureWebJobsSecretStorageType=files
# ENV FUNCTIONS_SECRETS_PATH=C:\Secrets
# ADD Secrets\\host.json C:\\Secrets\\host.json

ENV AzureWebJobsScriptRoot=C:\approot
ENV AzureFunctionsJobHost__Logging__Console__IsEnabled=true
ENV FUNCTIONS_WORKER_RUNTIME=dotnet
ENV WEBSITE_HOSTNAME=localhost:80

USER ContainerAdministrator
ENTRYPOINT ["C:\\Program Files\\dotnet\\dotnet.exe", "C:\\runtime\\Microsoft.Azure.WebJobs.Script.WebHost.dll" ]
