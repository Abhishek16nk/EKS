# EKS

helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
helm repo update
 
helm install prometheus prometheus-community/prometheus


kubectl apply -f https://openebs.github.io/charts/openebs-operator.yaml
Delete storage class openebs-hostpath & create a openebs-hostpath.yaml
 
