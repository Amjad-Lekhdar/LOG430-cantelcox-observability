# Runbook observabilite

## URLs

- Grafana: <http://localhost:3000>
- Prometheus: <http://localhost:9090>
- Blackbox Exporter: <http://localhost:9115>
- Prometheus targets: <http://localhost:9090/targets>
- Prometheus service discovery: <http://localhost:9090/service-discovery>

## Commandes de diagnostic

```bash
docker compose ps
docker compose logs -f prometheus
docker compose logs -f grafana
docker compose logs -f blackbox-exporter
docker compose restart prometheus
```

```bash
curl -fsS http://localhost:9090/-/ready
curl -fsS "http://localhost:9115/probe?module=http_2xx&target=http://100.95.65.46:8040/health"
curl -fsS http://100.83.57.43:9100/metrics
curl -fsS http://100.108.225.1:9100/metrics
```

## Requetes PromQL utiles

Disponibilite `/health`:

```promql
probe_success{job="blackbox-http-health"}
```

Latence P95/P99 des checks `/health`:

```promql
quantile_over_time(0.95, probe_duration_seconds{job="blackbox-http-health"}[5m])
quantile_over_time(0.99, probe_duration_seconds{job="blackbox-http-health"}[5m])
```

RPS applicatif quand `/metrics` est expose:

```promql
sum by (service) (rate(http_server_requests_seconds_count{job="microservices-metrics"}[5m]))
sum by (service) (rate(http_requests_total{job="microservices-metrics"}[5m]))
```

Erreurs applicatives 4xx/5xx quand `/metrics` est expose:

```promql
sum by (service, status) (rate(http_server_requests_seconds_count{job="microservices-metrics", status=~"4..|5.."}[5m]))
sum by (service, code) (rate(http_requests_total{job="microservices-metrics", code=~"4..|5.."}[5m]))
```

Saturation CPU/RAM/reseau:

```promql
100 - (avg by (machine) (rate(node_cpu_seconds_total{job="node-exporter", mode="idle"}[5m])) * 100)
100 * (1 - (node_memory_MemAvailable_bytes{job="node-exporter"} / node_memory_MemTotal_bytes{job="node-exporter"}))
sum by (machine) (rate(node_network_receive_bytes_total{job="node-exporter", device!~"lo|docker.*|br-.*|veth.*"}[5m]) * 8)
sum by (machine) (rate(node_network_transmit_bytes_total{job="node-exporter", device!~"lo|docker.*|br-.*|veth.*"}[5m]) * 8)
```
