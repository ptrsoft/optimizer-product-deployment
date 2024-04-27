
Install custom resource definitions:

kubectl create -f https://download.elastic.co/downloads/eck/2.12.1/crds.yaml

Install the operator with its RBAC rules:
kubectl apply -f https://download.elastic.co/downloads/eck/2.12.1/operator.yaml

Monitor the operator logs:

kubectl -n elastic-system logs -f statefulset.apps/elastic-operator


