# CanTelcoX Observability

Docker Compose observability stack for the CanTelcoX lab services. It runs Prometheus, Grafana, and Blackbox Exporter with file-based scrape target configuration.

## Stack

- Prometheus `v2.53.4` on <http://localhost:9090>
- Grafana `11.1.4` on <http://localhost:3000>
- Blackbox Exporter `v0.25.0` on <http://localhost:9115>
- Provisioned Grafana datasource and dashboard folder
- Prometheus file discovery for service health checks, application metrics, and node metrics

## Prerequisites

- Docker
- Docker Compose
- Reachability from this host to the configured lab service IPs

## Setup

Create a local environment file from the example:

```bash
cp .env.example .env
```

Update `.env` if you want different Grafana admin credentials:

```env
GRAFANA_ADMIN_USER=admin
GRAFANA_ADMIN_PASSWORD=change-me
```

Start the stack:

```bash
docker compose up -d
```

Open Grafana at <http://localhost:3000> and sign in with the credentials from `.env`.

## Configuration

Prometheus is configured in [prometheus/prometheus.yml](prometheus/prometheus.yml).

Service health checks are probed through Blackbox Exporter from [prometheus/targets/microservices.yml](prometheus/targets/microservices.yml):

```yaml
- labels:
    environment: lab
    service: identity-service
  targets:
    - http://100.83.57.43:8020/health
```

Node Exporter targets are listed in [prometheus/targets/nodes.yml](prometheus/targets/nodes.yml):

```yaml
- labels:
    environment: lab
    machine: identity-service
  targets:
    - 100.83.57.43:9100
```

Application `/metrics` targets are listed in [prometheus/targets/app-metrics.yml](prometheus/targets/app-metrics.yml) once services expose Prometheus metrics.

Prometheus refreshes all target files every 30 seconds.

## Grafana Provisioning

Grafana automatically provisions:

- Prometheus datasource from [grafana/provisioning/datasources/prometheus.yml](grafana/provisioning/datasources/prometheus.yml)
- Dashboards from [grafana/dashboards](grafana/dashboards)
- Dashboard provider from [grafana/provisioning/dashboards/dashboards.yml](grafana/provisioning/dashboards/dashboards.yml)

## Documentation

- Backlog observability: [docs/backlog.md](docs/backlog.md)
- Runbook: [docs/runbook.md](docs/runbook.md)
- Architecture sections 8.8 and 10.5: [docs/architecture.md](docs/architecture.md)

## Useful Commands

```bash
docker compose ps
docker compose logs -f prometheus
docker compose logs -f grafana
docker compose restart prometheus
docker compose down
```

Remove persisted Prometheus and Grafana data:

```bash
docker compose down -v
```

## Troubleshooting

- If Grafana cannot show metrics, verify Prometheus is healthy at <http://localhost:9090/targets>.
- If health probes fail, confirm the URLs in `prometheus/targets/microservices.yml` are reachable from the Docker host.
- If node metrics are missing, confirm Node Exporter is running on each target host at port `9100`.
