FROM mcr.microsoft.com/dotnet/core/aspnet:3.1 AS base
WORKDIR /app
EXPOSE 80
EXPOSE 443

FROM mcr.microsoft.com/dotnet/core/sdk:3.1 AS build
ARG FEED_ACCESSTOKEN

RUN curl -L https://raw.githubusercontent.com/Microsoft/artifacts-credprovider/master/helpers/installcredprovider.sh  | bash

ENV NUGET_CREDENTIALPROVIDER_SESSIONTOKENCACHE_ENABLED true
ENV DOTNET_SYSTEM_NET_HTTP_USESOCKETSHTTPHANDLER=0
ENV VSS_NUGET_EXTERNAL_FEED_ENDPOINTS "{\"endpointCredentials\": [{\"endpoint\":\"https://pkgs.dev.azure.com/Training-3rdWave/19e4531d-5a9c-4805-8bef-402e22637abb/_packaging/3rdnuget/nuget/v3/index.json\", \"username\":\"manuel\", \"password\":\"prgb6f4tkym24w3r6udkjpeo3hg3fg6evl5oqhgagtsqqkyypvxq\"}]}"

WORKDIR /src
COPY ["./.", "./"]
RUN dotnet restore --verbosity diag --configfile "TestNugetDocker/nuget.config" "TestNugetDocker.sln"
COPY . .
WORKDIR "/host"
RUN dotnet build "host.netcore.admin.csproj" -c Release -o /app

FROM build AS publish
RUN dotnet publish "host.netcore.admin.csproj" -c Release -o /app

FROM base AS final
WORKDIR /app
COPY --from=publish /app .
ENTRYPOINT ["dotnet", "host.netcore.admin.dll"]