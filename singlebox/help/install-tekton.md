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
https://storage.googleapis.com/tekton-releases/triggers/latest/release-full.yaml
kubectl apply --filename \
https://storage.googleapis.com/tekton-releases/triggers/latest/interceptors.yaml
kubectl get pods --namespace tekton-pipelines --watch

# Install Tekton Dashboard
kubectl apply --filename https://storage.googleapis.com/tekton-releases/dashboard/latest/release.yaml

kubectl get pods --namespace tekton-pipelines --watch

## Access Dashboard with Ingress

To access the tekton dashboard with ingress, we need to have the ingress controller installed in the kubernetes. This is done by the ansible script that configure the kubernetes cluster as follows:

ansible-playbook -vv -e "ansible_ssh_user=papu"  -e "ansible_connection=local"  -i "localhost," ansible/test.yaml

Post to that , we can install the ingress for the tekton dashboard as follows:
k apply -f ingress.yaml -n tekton-pipelines
(ingress.yaml inside nginx-ingress)