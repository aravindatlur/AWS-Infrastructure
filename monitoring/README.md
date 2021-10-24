This file contains the files related to monitoring setup. We will deploy prometheus on k8s cluster with node exporter using steps below. 

We will use charts (Helm’s packaging format) from the stable Helm repo to help getting started with monitoring Kubernetes.

Installing Helm
Add the stable repo to your Helm installation:

helm repo add stable https://kubernetes-charts.storage.googleapis.com/
helm repo update

Connect to K*S Cluster

create a custom namespace on our K8s cluster to manage all the monitoring stack:

kubectl create ns monitoring


helm install prometheus stable/prometheus --namespace monitoring

Then we can create a NodePort using K8s’ native imperative command which allows us to communicate directly to the pod from outside the cluster.


kubectl -n monitoring expose pod prometheus-server-9646d697-gl47z --type NodePort --name prometheus-server-np
service/prometheus-server-np exposed

kubectl get svc -n monitoring


Setup Node Exporter on Kubernetes


kubectl create -f daemonset.yaml

kubectl create -f service.yaml

kubectl get endpoints -n monitoring 


Add a below scrape config to the Prometheus config file to discover all the node-exporter pods.

      - job_name: 'node-exporter'
        kubernetes_sd_configs:
          - role: endpoints
        relabel_configs:
        - source_labels: [__meta_kubernetes_endpoints_name]
          regex: 'node-exporter'
          action: keep
