#See https://aka.ms/customizecontainer to learn how to customize your debug container and how Visual Studio uses this Dockerfile to build your images for faster debugging.

FROM mcr.microsoft.com/dotnet/aspnet:5.0 AS base
WORKDIR /app
EXPOSE 80
EXPOSE 443

FROM mcr.microsoft.com/dotnet/sdk:5.0 AS build
ARG BUILD_CONFIGURATION=Release
ARG PASSWORD_ENV_SEEDED
ENV PASSWORD_ENV_SEEDED=dGVzdGU=
RUN dotnet dev-certs https -ep /src/aspnetapp.pfx -p ${PASSWORD_ENV_SEEDED}
WORKDIR /src
# Copy csproj first to speed up restore
COPY Sample.Profile.sln .
COPY Sample.Profile/Sample.Profile.csproj Sample.Profile/

RUN dotnet restore
COPY . .
WORKDIR "/src/Sample.Profile"
# Publish
RUN dotnet publish Sample.Profile/Sample.Profile.csproj -c Release -o /app/publish

FROM build AS publish
ARG BUILD_CONFIGURATION=Release

RUN dotnet publish "cshapr/Sample.Profile/Sample.Profile.csproj" -c $BUILD_CONFIGURATION -o /app/publish /p:UseAppHost=false

FROM base AS final
WORKDIR /app
COPY --from=build /src/aspnetapp.pfx .
COPY --from=publish /app/publish .
ENTRYPOINT ["dotnet", "Sample.Profile.dll"]
