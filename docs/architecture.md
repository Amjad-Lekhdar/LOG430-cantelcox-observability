# Dossier d'architecture

## 8.8 Observabilite

L'observabilite infra est deja en place dans ce depot. La stack Docker Compose demarre Prometheus, Grafana et Blackbox Exporter. Prometheus charge les cibles via `prometheus/targets/microservices.yml` pour les checks `/health` et `prometheus/targets/nodes.yml` pour Node Exporter.

Les checks `/health` couvrent `api-gateway`, `identity-service`, `order-service` et `catalog-service`. Le 2026-06-27, `catalog-service` repondait depuis cette machine; `api-gateway`, `identity-service` et `order-service` etaient configures mais non joignables depuis l'environnement de verification.

Node Exporter est actif pour `identity-service` et `order-service`. Le noeud `catalog-service` n'est pas ajoute dans `nodes.yml` tant que `100.95.65.46:9100` ne repond pas.

L'observabilite applicative reste a completer avec des endpoints `/metrics` dans les services. Le job Prometheus `microservices-metrics` et le fichier `prometheus/targets/app-metrics.yml` sont prets, mais aucune cible n'est active tant que les services n'exposent pas Prometheus.

## 10.5 Supervision et exploitation

Grafana provisionne les dashboards depuis `grafana/dashboards`. Les vues attendues couvrent:

- Disponibilite des services via `probe_success`.
- Latence P95/P99 via `probe_duration_seconds`.
- RPS applicatif via les compteurs HTTP exposes par `/metrics`.
- Erreurs 4xx/5xx via les labels HTTP `status` ou `code`.
- Saturation CPU, RAM et reseau via Node Exporter.

Les captures Grafana du rapport final doivent etre prises apres demarrage de la stack avec `docker compose up -d` et validation dans <http://localhost:9090/targets>. Les commandes de diagnostic et les requetes PromQL de reference sont centralisees dans `docs/runbook.md`.
