# Use .NET SDK 8 image for building
FROM mcr.microsoft.com/dotnet/sdk:8.0-alpine AS build

# Set the working directory
WORKDIR /app

# Clone the repository and switch to the specified branch
RUN git clone --single-branch --branch dev/0.9 https://github.com/devgopher/botticelli.git .

# Change to the project directory
WORKDIR /app/Samples/Messaging.Sample.Telegram

# Restore dependencies
RUN dotnet restore

# Build the project
RUN dotnet build -c Release -o out

# Use the ASP.NET Runtime image for running
FROM mcr.microsoft.com/dotnet/aspnet:8.0-alpine
WORKDIR /app
COPY --from=build /app/Samples/Messaging.Sample.Telegram/out .

# Copy custom appsettings.json to replace the original
COPY appsettings.json .

# Set the entry point for the application
ENTRYPOINT ["dotnet", "Messaging.Sample.Telegram.dll"]