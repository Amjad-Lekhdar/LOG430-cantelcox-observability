# Backlog observability

## Fait

- Stack Prometheus, Grafana et Blackbox Exporter provisionnee par Docker Compose.
- Discovery Prometheus par fichiers pour les checks `/health` et Node Exporter.
- Check `/health` configure pour `api-gateway`, `identity-service`, `order-service` et `catalog-service`.
- Job Prometheus `microservices-metrics` ajoute pour les futurs endpoints `/metrics`.
- Dashboard Grafana provisionne pour disponibilite `/health`, latence, RPS, erreurs et saturation infra.
- Node Exporter verifie le 2026-06-27:
  - `identity-service` `100.83.57.43:9100`: OK.
  - `order-service` `100.108.225.1:9100`: OK.

## Non fait / a completer

- `api-gateway` `http://192.168.1.40:8000/health`: cible ajoutee, mais non joignable depuis cette machine le 2026-06-27.
- `identity-service` `http://100.83.57.43:8020/health`: non joignable depuis cette machine le 2026-06-27.
- `order-service` `http://100.108.225.1:8030/health`: non joignable depuis cette machine le 2026-06-27.
- `catalog-service` `http://100.95.65.46:8040/health`: OK le 2026-06-27.
- `catalog-service` Node Exporter `100.95.65.46:9100`: non joignable le 2026-06-27; a installer/demarrer avant ajout durable dans `prometheus/targets/nodes.yml`.
- `customers-service`, `billing-service`, `audit-service`: endpoints non presents dans ce depot et non ajoutes faute d'IP/port verifiables.
- `catalog-load-balancer` / HAProxy: endpoint non present dans ce depot et non ajoute faute d'IP/port verifiable.
- Endpoints applicatifs `/metrics`: aucun endpoint verifie ne repond actuellement; renseigner `prometheus/targets/app-metrics.yml` quand les services exposent Prometheus.
- Captures Grafana pour le rapport final: a prendre apres demarrage de la stack et stabilisation des cibles.
