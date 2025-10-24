# BotticelliBundle
BotticelliBundle is a Docker image designed to facilitate the quick deployment of a basic Botticelli bot. 
This repository includes a sample bot configuration, showcasing how to set up and customize your own bot 
using the powerful Botticelli framework. With a Docker setup, you can easily run and test your bot in a 
containerized environment.

## How to
In order to deploy your bot, you should clone this repository, modify a Dockerfile and appsettings.json loike this:

``` Dockerfile
# Use .NET SDK 8 image for building
FROM mcr.microsoft.com/dotnet/sdk:8.0-alpine AS build

# Set the working directory
WORKDIR /app

# Clone the repository and switch to the specified branch
RUN git clone --single-branch --branch release/0.8 https://github.com/devgopher/botticelli.git .
RUN git clone <your git repo> ./bot

# Change to the project directory
WORKDIR /app/bot

# Restore dependencies
RUN dotnet restore

# Build the project
RUN dotnet build -c Release -o out

# Use the ASP.NET Runtime image for running
FROM mcr.microsoft.com/dotnet/aspnet:8.0-alpine
WORKDIR /app
COPY --from=build /app/bot/out .

# Copy custom appsettings.json to replace the original
COPY appsettings.json .

# Set the entry point for the application
ENTRYPOINT ["dotnet", "<YourBot>.dll"]
```

``` json
{
  "Logging": {
    "LogLevel": {
      "Default": "Information",
      "Microsoft.AspNetCore": "Warning"
    }
  },
  "DataAccess": {
    "ConnectionString": "database.db;Password=<bot database password>"
  },
  "Server": {
    "ServerUri": "http://<your Botticelli admin server>:5042/v1/"
  },
  "AnalyticsClient": {
    "TargetUrl": "http://<your Botticelli analytics server(if exists)>:5251/v1/"
  },
  "TelegramBot": {
    "timeout": 60,
    "useThrottling": false,
    "useTestEnvironment": false,
    "name": "TestBot"
  },
  "Broadcasting": {
    "BroadcastingDbConnectionString": "YourConnectionStringHere",
    "BotId": "<Your BotId (if you need broadcasting)>",
    "HowOld": "00:10:00",
    "ServerUri": "http://<<Your BotId (if you need broadcasting)> (if you need broadcasting)>:5042/v1"
  },
  "AllowedHosts": "*"
}
```
