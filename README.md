# Purpose
This is a small demo for monitoring and observability using Prometheus, Jaeger and Grafana

# Pre-requisites
Oracle virtualbox
Hashicorp vagrant

# Steps
Modify the Vagrantfile as per your requirements then run the following commands

# Install k8 single node machine
vagrant up
vagrant ssh
kubectl version

# Install Helm V3
curl https://raw.githubusercontent.com/helm/helm/master/scripts/get-helm-3 | bash

# Create namespace monitoring where we will install Prometheus and Grafana
kubectl create namespace monitoring

# Add Prometheus repo to Helm
helm repo add prometheus-community https://prometheus-community.github.io/helm-charts

# Add stable repos to Helm and update
helm repo add stable https://charts.helm.sh/stable
helm repo update

# Install Prometheus and Grafana using Helm charts
helm install prometheus prometheus-community/kube-prometheus-stack --namespace monitoring --kubeconfig /etc/rancher/k3s/k3s.yaml

# Verify installation
kubectl get all --namespace=monitoring

# Expose Grafana service on local-host port
kubectl port-forward service/prometheus-grafana --address 0.0.0.0 3000:80 -n monitoring

# Access the Grafana using the credentials
username: admin 
password: prom-operator

# Install Jaeger Operator: create observability namespace
kubectl create namespace observability
# Install Jaeger Operator: Please use the last stable version
export jaeger_version=v1.28.0 
# Install Jaeger Operator: jaegertracing.io_jaegers_crd.yaml
kubectl create -f https://raw.githubusercontent.com/jaegertracing/jaeger-operator/${jaeger_version}/deploy/crds/jaegertracing.io_jaegers_crd.yaml
# Install Jaeger Operator: service_account.yaml
kubectl create -n observability -f https://raw.githubusercontent.com/jaegertracing/jaeger-operator/${jaeger_version}/deploy/service_account.yaml
# Install Jaeger Operator: role.yaml
kubectl create -n observability -f https://raw.githubusercontent.com/jaegertracing/jaeger-operator/${jaeger_version}/deploy/role.yaml
# Install Jaeger Operator: role_binding.yaml
kubectl create -n observability -f https://raw.githubusercontent.com/jaegertracing/jaeger-operator/${jaeger_version}/deploy/role_binding.yaml
# Install Jaeger Operator: operator.yaml
kubectl create -n observability -f https://raw.githubusercontent.com/jaegertracing/jaeger-operator/${jaeger_version}/deploy/operator.yaml
# Enable cluster wide permission: cluster_role.yaml
kubectl create -f https://raw.githubusercontent.com/jaegertracing/jaeger-operator/${jaeger_version}/deploy/cluster_role.yaml
# Enable cluster wide permission: cluster_role_binding.yaml
kubectl create -f https://raw.githubusercontent.com/jaegertracing/jaeger-operator/${jaeger_version}/deploy/cluster_role_binding.yaml
# Deploy sample application for Jaeger
kubectl apply -f https://raw.githubusercontent.com/jaegertracing/jaeger-operator/${jaeger_version}/examples/business-application-injected-sidecar.yaml


