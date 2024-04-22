# Installation: 

### 1. Download source code 
```
git clone https://github.com/csongnr/otel-agent.git 
```

### 2. Update config [here](https://github.com/csongnr/otel-agent/blob/master/nr-k8s-otel-collector/values.yaml#L20-L24) to add a cluster name, and New Relic Ingest - License key
Example: 
```
licenseKey: "EXAMPLEINGESTLICENSEKEY345878592NRALL"
newRelicEndpoint: "https://otlp.nr-data.net"
cluster: "SampleApp" 
```

#### [Optional] Enable node-exporter (not required for New Relic Kubernetes monitoring experience) 
1. Run: 
```
helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
helm repo update
helm install nodeexporter prometheus-community/prometheus-node-exporter 
```
2. Comment out [these lines](https://github.com/csongnr/otel-agent/blob/master/nr-k8s-otel-collector/templates/daemonset-configmap.yaml#L277-L292) in the configuration. 


### 3. From root directory of this repository, run:
```
cd ~/otel-agent 
helm install otel-agent-release nr-k8s-otel-collector -n newrelic --create-namespace
```

## Confirm installation
### Watch pods spin up: 
```
kubectl get pods -A --watch 
```

### Check logs of opentelemetry pod that spins up: 
```
kubectl logs <otel-pod-name>
```

### Confirm data coming through in New Relic 
You should see data reporting into New Relic within a couple of seconds to the `InfrastructureEvent` table, `Metric` table, and `Log` tables.
```
FROM Metric SELECT * 
```
```
FROM InfrastructureEvent SELECT * 
```
```
FROM Log SELECT * 
```

## Development notes
### Iterating on otel config: 
1. Make changes to the [opentelemetry configuration](https://github.com/csongnr/otel-agent/blob/master/nr-k8s-otel-collector/templates/configmap.yaml#L6-L485) 
2. Upgrade the release:
```
helm upgrade otel-agent-release nr-k8s-otel-collector
```



