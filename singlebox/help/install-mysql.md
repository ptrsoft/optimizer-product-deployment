helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
helm repo update

helm install [RELEASE_NAME] prometheus-community/kube-prometheus-stack

helm install prometheus-stack prometheus-community/kube-prometheus-stack


kubectl apply -f https://github.com/cert-manager/cert-manager/releases/download/v1.14.5/cert-manager.yaml

helm repo add mariadb-operator https://mariadb-operator.github.io/mariadb-operator
helm install mariadb-operator mariadb-operator/mariadb-operator \
  --set metrics.enabled=true --set webhook.cert.certManager.enabled=true

Clone the operator 

https://github.com/mariadb-operator/mariadb-operator.git

install the following configuration manifests that will be referenced by the CRDs further:
kubectl apply -f examples/manifests/config

Next, you can proceed with the installation of a MariaDB instance:
kubectl apply -f examples/manifests/mariadb.yaml

Check the following :

kubectl get mariadbs
NAME      READY   STATUS    PRIMARY POD     AGE
mariadb   True    Running   mariadb-0       3m57s

kubectl get statefulsets
NAME      READY   AGE
mariadb   1/1     2m12s

kubectl get services
NAME         TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)             AGE
mariadb      ClusterIP   10.96.235.145   <none>        3306/TCP,9104/TCP   2m17s

Up and running , we can now create our first logical database and grant access to users:

kubectl apply -f examples/manifests/database.yaml
kubectl apply -f examples/manifests/user.yaml
kubectl apply -f examples/manifests/grant.yaml
kubectl get databases
NAME        READY   STATUS    CHARSET   COLLATE           AGE
data-test   True    Created   utf8      utf8_general_ci   22s

kubectl get users
NAME              READY   STATUS    MAXCONNS   AGE
user              True    Created   20         29s

kubectl get grants
NAME              READY   STATUS    DATABASE   TABLE   USERNAME          GRANTOPT   AGE
user              True    Created   *          *       user              true       36s

At this point, we can run our database initialization scripts:

Note : Please note that original repo contains some field podMetaData in sqljob_01-users.yaml,
That is no longer supported , and we need to delete that in sqljob_01-users.yaml.

kubectl apply -f examples/manifests/sqljobs
kubectl get sqljobs
NAME       COMPLETE   STATUS    MARIADB   AGE
01-users   True       Success   mariadb   2m47s
02-repos   True       Success   mariadb   2m47s
03-stars   True       Success   mariadb   2m47s

kubectl get jobs
NAME                  COMPLETIONS   DURATION   AGE
01-users              1/1           10s        3m23s
02-repos              1/1           11s        3m13s
03-stars-28067562     1/1           10s        106s

kubectl get cronjobs
NAME       SCHEDULE      SUSPEND   ACTIVE   LAST SCHEDULE   AGE
03-stars   */1 * * * *   False     0        57s             2m33s

