#See https://aka.ms/containerfastmode to understand how Visual Studio uses this Dockerfile to build your images for faster debugging.

FROM mcr.microsoft.com/dotnet/aspnet:3.1 AS base
WORKDIR /app
EXPOSE 80

RUN curl -L https://raw.githubusercontent.com/Microsoft/artifacts-credprovider/master/helpers/installcredprovider.sh | sh

FROM mcr.microsoft.com/dotnet/sdk:3.1 AS build
WORKDIR /app
COPY *.csproj .
COPY ./nuget.config .
ENV VSS_NUGET_EXTERNAL_FEED_ENDPOINTS="{\"endpointCredentials\": [{\"endpoint\":\"https://pkgs.dev.azure.com/MyOrg/_packaging/MyRepo/nuget/v3/index.json\", \"username\":\"docker\", \"password\":\"MyPassword\"}]}"
RUN dotnet restore

COPY . .
RUN dotnet build "DockerPrivateFeed.csproj" -c Release -o /app/build
FROM build AS publish
RUN dotnet publish "DockerPrivateFeed.csproj" -c Release -o /app/publish

FROM base AS final
WORKDIR /app
COPY --from=publish /app/publish .
ENTRYPOINT ["dotnet", "DockerPrivateFeed.dll"]
