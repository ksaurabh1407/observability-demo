# Update Helm, add Prometheus repos
helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
helm repo add stable https://charts.helm.sh/stable
# Create namespace monitoring, yaml file is present in manifest folder
kubectl create -f namespace-monitoring.yaml
# Install Prometheus
helm install prometheus prometheus-community/kube-prometheus-stack --namespace monitoring 
# Check Prometheus status
kubectl --namespace monitoring get pods -l "release=prometheus"
# Create namespace observability, yaml file is present in manifest folder
kubectl create -f namespace-observability.yaml
# Install Jaeger operator in observability namespace
kubectl create -f jaeger-operator.yaml --namespace=observability
# Enable ingress addon for Minikube
minikube addons enable ingress
# Create jaeger allinone instance
kubectl apply -f jaeger-instance.yaml
# Create NodePort service to access grafana dashboard
kubectl create -f grafana-service.yaml
# Access Grafana
username: admin
password: prom-operator
# Grafana Data Source
click on Configuration > Datasources >> Add Datasource
Select Jaeger under distributed tracing
Under HTTP in URL textbox put the following
simpletest-query.observability.svc.cluster.local:16686
# Create k8 cluster monitoring dashboard in Grafana
import kubernetes-cluster-monitoring-via-prometheus_rev3.json dashboard from grafana folder



