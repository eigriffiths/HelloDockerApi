#See https://aka.ms/customizecontainer to learn how to customize your debug container and how Visual Studio uses this Dockerfile to build your images for faster debugging.
#
#FROM mcr.microsoft.com/dotnet/aspnet:6.0 AS base
#WORKDIR /app
#EXPOSE 80
#EXPOSE 443
#
#FROM mcr.microsoft.com/dotnet/sdk:6.0 AS build
#WORKDIR /src
#COPY ["HelloDockerApi.csproj", "HelloDockerApi/"]
#WORKDIR "/src/HelloDockerApi"
#RUN dotnet restore "HelloDockerApi.csproj"
#COPY . .
#
#RUN dotnet build "HelloDockerApi.csproj" -c Release -o /app/build
#
#FROM build AS publish
#RUN dotnet publish "HelloDockerApi.csproj" -c Release -o /app/publish /p:UseAppHost=false
#
#FROM base AS final
#WORKDIR /app
#COPY --from=publish /app/publish .
#ENTRYPOINT ["dotnet", "HelloDockerApi.dll"]


FROM mcr.microsoft.com/dotnet/sdk:6.0 AS build
WORKDIR /app

# Copy csproj and restore as distinct layers
COPY ["HelloDockerApi.csproj", "HelloDockerApi/"]
RUN dotnet restore "HelloDockerApi/HelloDockerApi.csproj"

# Copy everything else and build
FROM build AS publish
COPY . .
RUN dotnet publish "HelloDockerApi.csproj" -c Release -o /app/publish /p:UseAppHost=false

# Base Image for Node.js (for Vue 3)
FROM node:16 AS node-build
WORKDIR /app

# Copy Vue 3 frontend and install dependencies
COPY ["ClientApp/", "ClientApp/"]
RUN cd ClientApp && npm install

# Build Vue 3 Frontend with Vite
RUN cd ClientApp && npm run build

# Final Image for Running the Application
FROM mcr.microsoft.com/dotnet/aspnet:6.0 AS final
WORKDIR /app
COPY --from=publish /app/publish .

# Copy built Vue app to wwwroot
COPY --from=node-build /app/ClientApp/dist ./wwwroot

# Set the Entry Point
ENTRYPOINT ["dotnet", "HelloDockerApi.dll"]
