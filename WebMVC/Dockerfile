FROM microsoft/dotnet:2.2.0-aspnetcore-runtime AS base
WORKDIR /app
EXPOSE 80

FROM microsoft/dotnet:2.2.100-sdk AS build
WORKDIR /src
COPY . .
WORKDIR /src
RUN dotnet restore -nowarn:msb3202,nu1503

FROM build AS publish
RUN dotnet publish --no-restore -c Release -o /app

FROM base AS final
WORKDIR /app
RUN mkdir -p /opt/appdynamics/dotnet
ADD appd/libappdprofiler.so /opt/appdynamics/dotnet/
ADD appd/AppDynamics.Agent.netstandard.dll /opt/appdynamics/dotnet/
COPY --from=publish /app .
# Mandatory settings required to attach the agent to the .NET application
ENV CORECLR_PROFILER={57e1aa68-2229-41aa-9931-a6e93bbc64d8} \
    CORECLR_ENABLE_PROFILING=1 \
    CORECLR_PROFILER_PATH=/opt/appdynamics/dotnet/libappdprofiler.so
ENTRYPOINT ["dotnet", "WebMVC.dll"]
