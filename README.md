Project scaffold: frontend + backend + Kubernetes + ELK + observability

Quick overview
- Frontend: simple static page that calls backend
- Backend: Node.js + Express with Prometheus metrics, pino logs, and OpenTelemetry tracing (OTLP)
- Kubernetes: manifests to deploy frontend, backend, OpenTelemetry Collector, Jaeger, Prometheus
- ELK: Elasticsearch (single-node), Kibana, Fluent Bit DaemonSet to ship container logs to Elasticsearch

Quick commands (assuming Docker + kubectl + kind):

1. Create a kind cluster:

```bash
kind create cluster --name obser
```

2. Apply k8s manifests (no local image builds required):

```bash
# create namespace and apply manifests; backend/frontend run from public images using ConfigMaps
kubectl apply -f k8s/namespace.yaml
kubectl apply -f k8s -n obser
kubectl apply -f elastic -n obser
```

4. Port-forward dashboards (run in separate terminals):

```bash
# Frontend
kubectl port-forward svc/frontend 8080:80 -n obser

# Kibana
kubectl port-forward svc/kibana 5601:5601 -n obser

# Jaeger UI
kubectl port-forward svc/jaeger-query 16686:16686 -n obser

# Prometheus
kubectl port-forward deployment/prometheus 9090:9090 -n obser
```

Notes / next steps
- Backend and frontend run directly from public images (`node:18-alpine`, `nginx:alpine`) and use ConfigMaps/initContainers to install and run application code at pod start. No Docker builds are necessary.
- This is a minimal scaffold to demonstrate metrics, logs, and traces in-cluster; adjust for production (persistence, security, resource limits).
