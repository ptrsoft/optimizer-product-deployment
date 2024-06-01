# Running on kubernetes

Prerequisites

You need a working k8s cluster and kubectl configured.

## Deploy the objects:

kubectl apply -f pod.yml

Attach to the pod:

kubectl attach --namespace=postgresql-client -ti postgresql-client

Connect to a PostgreSQL database:

psql -h <host_ip_address> -p <port> -U <user> -W

psql postgresql://postgres:postgres@cluster-example-full-rw.cnpg-system.svc.cluster.local

## How to access from outside world

![alt text](image.png)

Just edit the cluster-example-full-rw service from clusterIp to NodePort and u can access the service from outside.

psql -h 213.210.36.2 --port 31543 -U postgres