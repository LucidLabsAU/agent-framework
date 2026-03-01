# Build the application
FROM mcr.microsoft.com/dotnet/sdk:10.0-alpine AS build
WORKDIR /src

# Copy files from the current directory on the host to the working directory in the container
COPY . .

RUN dotnet restore
RUN dotnet build -c Release --no-restore
RUN dotnet publish -c Release --no-build -o /app -f net10.0

# Run the application
FROM mcr.microsoft.com/dotnet/aspnet:10.0-alpine AS final
WORKDIR /app

# Copy everything needed to run the app from the "build" stage.
COPY --from=build /app .

RUN addgroup --system appgroup && adduser --system --ingroup appgroup appuser

EXPOSE 8088

HEALTHCHECK --interval=30s --timeout=5s --start-period=10s --retries=3 \
    CMD wget --no-verbose --tries=1 --spider http://localhost:8088/ || exit 1

USER appuser

ENTRYPOINT ["dotnet", "AgentWithHostedMCP.dll"]
