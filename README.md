# dotnet-dice-api-otel-protocol

Exemplo de como configurar um projeto com Trace do OpenTelemetry usando OpenTelemetryProtocol (OTLP) com jarger rodando no docker.

Execute o projeto dotnet no visual studio e no terminal execute o seguinte comando:

```curl
curl http://localhost:8080/rolldice
```

```yaml
version: '3.8'

services:
  jaeger:
    image: jaegertracing/all-in-one
    container_name: jaeger 
    ports:
      - "16686:16686"
      - "4317:4317"
      - "4318:4318"
    environment:
      - COLLECTOR_OTLP_ENABLED=true
```

```csharp
var builder = WebApplication.CreateBuilder(args);

const string serviceName = "roll-dice";

builder.Services.AddOpenTelemetry()
      .ConfigureResource(resource => resource.AddService(serviceName))
      .WithTracing(tracing => tracing
          .AddAspNetCoreInstrumentation()
          .AddConsoleExporter()
          .AddOtlpExporter())
      .WithMetrics(metrics => metrics
          .AddAspNetCoreInstrumentation()
          .AddConsoleExporter());
```