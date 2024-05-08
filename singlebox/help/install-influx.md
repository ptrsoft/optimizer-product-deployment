# Install influx

The best way to install influx on k8 is by installing via helm chart 
## add the repo
helm repo add influxdata https://helm.influxdata.com/

## Run the following command, providing a name for your InfluxDB release:

helm upgrade --install my-release influxdata/influxdb2


## To uninstall the my-release deployment, use the following command:

helm uninstall my-release


## You can access it by using the service name: my-release-influxdb2

To retrieve the password for the 'admin' user:

  echo $(kubectl get secret my-release-influxdb2-auth -o "jsonpath={.data['admin-password']}" --namespace default | base64 --decode)

Note: with enabled persistence, admin password is only set once during the initial deployment. The password is not changed when InfluxDB 2 is re-deployed with different password.