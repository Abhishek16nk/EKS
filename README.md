# EKS

loki:

helm repo add grafana https://grafana.github.io/helm-charts
helm repo update
helm upgrade --install loki grafana/loki-stack

-------------------------------------------

helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
helm repo update
 
helm install prometheus prometheus-community/prometheus


kubectl apply -f https://openebs.github.io/charts/openebs-operator.yaml

Delete storage class openebs-hostpath & 

create a openebs-hostpath.yaml

-------------------------------------

Setup Prometheus:

helm repo add prometheus-community https://prometheus-community.github.io/helm-charts

helm repo update

helm upgrade --install prometheus prometheus-community/prometheus \
    --set server.persistentVolume.enabled=false \
    --namespace monitoring --create-namespace

kubectl expose service prometheus-server \
    --type=NodePort \
    --target-port=9090 \
    --name=prometheus-server-nodeport \
    -n monitoring

----------------------------------------------------------------------------------------------------------
Setup Grafana:

helm repo add grafana https://grafana.github.io/helm-charts
helm repo update

helm upgrade --install grafana grafana/grafana \
    --set persistence.enabled=false \
    --namespace monitoring --create-namespace

username: admin

To get Password:
kubectl get secret --namespace monitoring grafana -o jsonpath="{.data.admin-password}" | base64 --decode ; echo

kubectl expose service grafana --type=NodePort --target-port=3000 --name=grafana-nodeport -n monitoring

Grafana Dashboard:
https://grafana.com/grafana/dashboards/
https://grafana.com/grafana/dashboards/3119-kubernetes-cluster-monitoring-via-prometheus/

ID: 6417, 7249, 315

Cleanup:

helm uninstall grafana prometheus -n monitoring
----------------------------------------------------------------------------------------------------------
ELK Cluster:

helm repo add elastic https://helm.elastic.co

helm upgrade --install elasticsearch elastic/elasticsearch \
    --set persistence.enabled=false  \
    --set readinessProbe.initialDelaySeconds=100 \
    --namespace elk --create-namespace

helm upgrade --install kibana elastic/kibana \
    --set resources.requests.cpu="1000m" \
    --set resources.requests.memory="1024Mi" \
    --set resources.limits.cpu="1000m" \
    --set resources.limits.memory="1024Mi" \
    --set readinessProbe.initialDelaySeconds=100 \
    --namespace elk --create-namespace

helm upgrade --install filebeat elastic/filebeat \
    --namespace elk --create-namespace

kubectl expose service kibana-kibana -n elk --type=NodePort --target-port=5601 --name=kibana-nodeport

Username: elastic

Password:
kubectl get secrets --namespace=elk elasticsearch-master-credentials -ojsonpath='{.data.password}' | base64 -d

Cleanup:

helm uninstall elasticsearch kibana filebeat -n elk
----------------------------------------------------------------------------------------------------------
Force remove namespace stuck in Terminating stage

kubectl get namespace "elk" -o json \
  | tr -d "\n" | sed "s/\"finalizers\": \[[^]]\+\]/\"finalizers\": []/" \
  | kubectl replace --raw /api/v1/namespaces/elk/finalize -f -

Option 2:

kubectl delete namespace ingress-nginx --grace-period=0 --force

Option 3:

kubectl get apiservice 

Delete the apiservice that is in False state

kubectl patch ns ingress-nginx -p '{"metadata":{"finalizers":null}}'



 
