
## Install custom resource definitions:

kubectl create -f https://download.elastic.co/downloads/eck/2.12.1/crds.yaml

## Install the operator with its RBAC rules:
kubectl apply -f https://download.elastic.co/downloads/eck/2.12.1/operator.yaml

## Monitor the operator logs:

kubectl -n elastic-system logs -f statefulset.apps/elastic-operator

## One Pod is in the process of being started:

kubectl get pods --selector='elasticsearch.k8s.elastic.co/cluster-name=quickstart'

## Retrieve the password to access

PASSWORD=$(kubectl get secret quickstart-es-elastic-user -o go-template='{{.data.elastic | base64decode}}')

## Do port forwarding so that you could access

kubectl port-forward service/quickstart-es-http 9200

## Accees the ES

curl -u "elastic:$PASSWORD" -k "https://localhost:9200"




