FROM mcr.microsoft.com/dotnet/aspnet:8.0 AS base
WORKDIR /app
EXPOSE 8080
EXPOSE 8081

FROM mcr.microsoft.com/dotnet/sdk:8.0 AS build
ARG BUILD_CONFIGURATION=Release
WORKDIR /src
COPY ["GoReplayInDotnet/GoReplayInDotnet.csproj", "GoReplayInDotnet/"]
RUN dotnet restore "GoReplayInDotnet/GoReplayInDotnet.csproj"
COPY . .
WORKDIR "/src/GoReplayInDotnet"
RUN dotnet build "GoReplayInDotnet.csproj" -c $BUILD_CONFIGURATION -o /app/build

# Install Gor

ENV GOR_VERSION 1.3.3

RUN wget https://github.com/buger/goreplay/releases/download/${GOR_VERSION}/gor_${GOR_VERSION}_x64.tar.gz -O gor.tar.gz

RUN tar -xzvf gor.tar.gz

RUN mv gor /usr/local/bin/

# End Of Install Gor

FROM build AS publish
ARG BUILD_CONFIGURATION=Release
RUN dotnet publish "GoReplayInDotnet.csproj" -c $BUILD_CONFIGURATION -o /app/publish /p:UseAppHost=false

FROM base AS final
WORKDIR /app
COPY --from=publish /app/publish .
ENTRYPOINT [ "gor --input-raw :8000 --output-stdout | dotnet GoReplayInDotnet.dll"]
