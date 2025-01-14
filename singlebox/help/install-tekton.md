<!-- TOC -->

- [Install Tekton](#install-tekton)
- [Install Trigger](#install-trigger)
- [Install Tekton Dashboard](#install-tekton-dashboard)
    - [Access Dashboard with Ingress](#access-dashboard-with-ingress)

<!-- /TOC -->
# Install Tekton

kubectl apply --filename https://storage.googleapis.com/tekton-releases/pipeline/latest/release.yaml

kubectl get pods --namespace tekton-pipelines --watch

# Install Trigger

kubectl apply --filename \
https://storage.googleapis.com/tekton-releases/triggers/latest/release.yaml
kubectl apply --filename \
https://storage.googleapis.com/tekton-releases/triggers/latest/interceptors.yaml
kubectl get pods --namespace tekton-pipelines --watch

# Install Tekton Dashboard
kubectl apply --filename https://storage.googleapis.com/tekton-releases/dashboard/latest/release.yaml

kubectl get pods --namespace tekton-pipelines --watch

## Access Dashboard with Ingress

First install a default certificate in kubernetes 
- Copy openssl config in temp 
cp openssl.cfg /tmp/openssl.cfg

- Generate SSL certificate
openssl req -x509 -sha256 -nodes -newkey rsa:4096 -keyout /tmp/key.pem -out /tmp/cert.pem -extensions v3_req -days 9001 -config /tmp/openssl.cfg

- name: Create tls secret in k8
kubectl create secret tls defaultcert --key /tmp/key.pem --cert /tmp/cert.pem


