FROM mcr.microsoft.com/dotnet/sdk:10.0.302 AS build
WORKDIR /src

COPY src/SkillCatalog/global.json ./SkillCatalog/global.json
COPY src/SkillCatalog/api/SkillCatalog.Api/SkillCatalog.Api.csproj ./SkillCatalog/api/SkillCatalog.Api/
RUN dotnet restore SkillCatalog/api/SkillCatalog.Api/SkillCatalog.Api.csproj

COPY src/SkillCatalog ./SkillCatalog
COPY plugins ./plugins
WORKDIR /src/SkillCatalog
RUN dotnet publish api/SkillCatalog.Api/SkillCatalog.Api.csproj --configuration Release --output /app/publish --no-restore

FROM mcr.microsoft.com/dotnet/sdk:10.0.302 AS migrator
WORKDIR /src/SkillCatalog
COPY --from=build /src /src
RUN dotnet restore api/SkillCatalog.Api/SkillCatalog.Api.csproj
RUN dotnet tool install --tool-path /tools dotnet-ef --version 10.0.0
ENV PATH="/tools:${PATH}"
ENTRYPOINT ["dotnet", "ef", "database", "update", "--project", "api/SkillCatalog.Api", "--startup-project", "api/SkillCatalog.Api"]

FROM mcr.microsoft.com/dotnet/aspnet:10.0 AS runtime
WORKDIR /app
COPY --from=build /app/publish .
COPY --from=build /src/plugins /repo/plugins
ENV ASPNETCORE_URLS=http://+:8080
EXPOSE 8080
ENTRYPOINT ["dotnet", "SkillCatalog.Api.dll"]
