# Installation: 

### 1. Download source code 
```
git clone https://github.com/csongnr/otel-agent.git 
```

### 2. Add kube-state-metrics to your cluster: 
 **Note:** This will be added to the chart soon 
```
helm repo add prometheus-community \ https://prometheus-community.github.io/helm-charts 
helm repo update 
helm install kube-state-metrics prometheus-community/kube-state-metrics
```

### 3. Update config [here](https://github.com/csongnr/otel-agent/blob/master/nr-k8s-otel-collector/values.yaml#L13-L18) to add a cluster name, and New Relic Ingest - License key
Example: 
```
newRelic:
  apiKey: "INGESTLICENSEKEY3458278592NRALL"
  endpoint: "https://otlp.nr-data.net:4318"
  
cluster:
  name: "SampleApp" 
```

#### [Optional] Enable node-exporter (not required for New Relic Kubernetes monitoring experience) 
1. Run: 
```
helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
helm repo update
helm install nodeexporter prometheus-community/prometheus-node-exporter 
```
2. Comment out [these lines](https://github.com/csongnr/otel-agent/blob/master/nr-k8s-otel-collector/templates/configmap.yaml#L277-L292) in the configuration. 


### 4. From root directory of this repository, run:
```
cd ~/otel-agent 
helm install otel-agent-release nr-k8s-otel-collector
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
### Iterating on config changes: 
Because the config is mounted on a configmap, any changes to the [opentelemetry configuration](https://github.com/csongnr/otel-agent/blob/master/nr-k8s-otel-collector/templates/configmap.yaml#L6-L485) at this time require you to either uninstall the release and re-install the release, or upgrade the release and then kill the pod so it spins up w/ the latest changes.

**Note:** This will be updated soon so that you can just upgrade the release to reflect changes. 
 
Uninstall the release and re-install the release:
```
helm uninstall otel-agent-release 
helm install otel-agent-release nr-k8s-otel-collector
```
**OR:**
Upgrade the release and then kill the pod:
```
helm upgrade otel-agent-release nr-k8s-otel-collector
kubectl delete pod <pod-name> 
```



