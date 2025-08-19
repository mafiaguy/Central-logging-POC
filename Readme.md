# Centralized Logging and Distributed Tracing with OpenSearch

This project sets up a **centralized logging** and **distributed tracing** stack using:
- **OpenSearch** (search & analytics engine)
- **OpenSearch Dashboards** (visualization UI)
- **Data Prepper** (ingestion & trace analytics)
- **Three demo microservices** (`service-a`, `service-b`, `service-c`)

It allows you to:
- Collect logs from services
- View structured logs in OpenSearch Dashboards
- Trace requests across multiple services (distributed tracing)

---

## 🚀 Getting Started

### 1. Prerequisites
- Docker & Docker Compose installed
- `curl` (to generate requests & test traces)

### 2. Bootstrap the Environment
Run the bootstrap script to set up index templates and start services:

```bash
bash scripts/bootstrap.sh
```
This will:
- Create required OpenSearch templates
- Start OpenSearch, Dashboards, Data Prepper, and demo services

## 🧩 Services

- OpenSearch → runs on https://localhost:9200

- OpenSearch Dashboards → runs on https://localhost:5601

- Data Prepper → ingests logs/traces (pipelines.yaml)

- Demo services:

    service-a

    service-b

    service-c

## 🔐 Authentication

Default credentials:
```bash
username: admin
password: G7v!xQp2@Ln9
```
Use them with curl or in the Dashboards login screen.

## 📖 Viewing Logs

- Go to OpenSearch Dashboards → Observability > Logs

- You should see logs flowing from your demo services.

- Logs are stored in the service-logs index (custom pipeline filters out infra logs).

## 🔎 Viewing Traces

Generate a request:
```bash
curl -u 'admin:G7v!xQp2@Ln9' http://localhost:8000/work
```
Example output:
```
{
  "ok": true,
  "service": "service-a",
  "took_ms": 12,
  "upstream": {
    "ok": true,
    "service": "service-b",
    "took_ms": 5,
    "upstream": {
      "ok": true,
      "service": "service-c",
      "took_ms": 0,
      "upstream": null
    }
  }
}
```
In Dashboards → Observability > Traces
You should see spans and distributed traces across all three services.

In Dashboards → Observability > Applications
You can view service maps.

## ⚙️ Configuration
Pipelines

Located in: ./data-prepper/pipelines.yaml

- otel-trace-raw-pipeline → receives OpenTelemetry traces

- service-map-pipeline → builds service maps

- trace-analytics-pipeline → stores spans

- service-logs-pipeline → ingests logs from services (filters out infra logs)

OpenSearch Dashboards Config
Located in: ./opensearch_dashboards.yml
Key settings:
```
server.host: "0.0.0.0"
opensearch.hosts: ["https://opensearch:9200"]
opensearch.username: "admin"
opensearch.password: "G7v!xQp2@Ln9"
opensearch.ssl.verificationMode: none
```

## 📊 Observability in Dashboards

Logs → Filter by service-a, service-b, service-c

Traces → Full request trace across all services

Applications → Service dependency map

Dashboards → Custom visualizations & metrics

## 🛠️ Troubleshooting
Too many logs?
The service-logs-pipeline in pipelines.yaml drops infra logs and only keeps service logs.
Adjust filters as needed.

No traces?
Ensure you hit http://localhost:8000/work at least once.
Then refresh Observability > Traces.

SSL issues with curl?
Use plain HTTP for testing:
```bash
curl -u 'admin:G7v!xQp2@Ln9' http://localhost:8000/work
```


## Extending

- Add more services → configure them with OpenTelemetry SDK/agent
- Add metrics ingestion (Prometheus integration available)
- Build custom dashboards for monitoring

## 🧹 Cleanup

Stop all containers:
```bash
docker compose down -v
```

This will remove volumes, containers, and networks.