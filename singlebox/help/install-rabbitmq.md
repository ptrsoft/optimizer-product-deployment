# install rabbimq through operator
Ref -- https://www.rabbitmq.com/kubernetes/operator/quickstart-operator

## Install the RabbitMQ Cluster Operator

kubectl apply -f "https://github.com/rabbitmq/cluster-operator/releases/latest/download/cluster-operator.yml"

namespace/rabbitmq-system created
customresourcedefinition.apiextensions.k8s.io/rabbitmqclusters.rabbitmq.com created
serviceaccount/rabbitmq-cluster-operator created
role.rbac.authorization.k8s.io/rabbitmq-cluster-leader-election-role created
clusterrole.rbac.authorization.k8s.io/rabbitmq-cluster-operator-role created
rolebinding.rbac.authorization.k8s.io/rabbitmq-cluster-leader-election-rolebinding created
clusterrolebinding.rbac.authorization.k8s.io/rabbitmq-cluster-operator-rolebinding created
deployment.apps/rabbitmq-cluster-operator created

## verify the oprator installation 

The Cluster Operator deployment is created in rabbitmq-system namespace.

kubectl get all -n rabbitmq-system

NAME                                             READY   STATUS    RESTARTS   AGE
pod/rabbitmq-cluster-operator-54f948d8b6-k79kd   1/1     Running   0          2m10s

NAME                                        READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/rabbitmq-cluster-operator   1/1     1            1           2m10s

NAME                                                   DESIRED   CURRENT   READY   AGE
replicaset.apps/rabbitmq-cluster-operator-54f948d8b6   1         1         1   

## verify all the CRD's

kubectl get customresourcedefinitions.apiextensions.k8s.io

NAME                                             CREATED AT
...
rabbitmqclusters.rabbitmq.com                    2021-01-14T11:12:26Z
...

## install the cluster, a hello world example cluster

kubectl apply -f https://raw.githubusercontent.com/rabbitmq/cluster-operator/main/docs/examples/hello-world/rabbitmq.yaml

## verify that its running
kubectl get rabbitmqclusters.rabbitmq.com

NAME          AGE    STATUS
hello-world   4m1s

## view the logs

kubectl logs hello-world-server-0
...

  ##  ##      RabbitMQ 3.12.1
  ##  ##
  ##########  Copyright (c) 2005-2024 Broadcom. All Rights Reserved. The term "Broadcom" refers to Broadcom Inc. and/or its subsidiaries.
  ######  ##
  ##########  Licensed under the MPL 2.0. Website: https://rabbitmq.com

  Erlang:      26.0.1 [jit]
  TLS Library: OpenSSL - OpenSSL 1.1.1u  30 May 2023
  Release series support status: supported

  Doc guides: https://www.rabbitmq.com/documentation.html
  Support:    https://www.rabbitmq.com/contact.html
  Tutorials:  https://www.rabbitmq.com/tutorials.html
  Monitoring: https://www.rabbitmq.com/monitoring.html

...

## do performance test

username="$(kubectl get secret hello-world-default-user -o jsonpath='{.data.username}' | base64 --decode)"

password="$(kubectl get secret hello-world-default-user -o jsonpath='{.data.password}' | base64 --decode)"

service="$(kubectl get service hello-world -o jsonpath='{.spec.clusterIP}')"

kubectl run perf-test --image=pivotalrabbitmq/perf-test -- --uri amqp://$username:$password@$service


pod/perf-test created

## We can now view the perf-test logs by running:

kubectl logs --follow perf-test
...
id: test-141948-895, time: 16.001s, sent: 25651 msg/s, received: 25733 msg/s, min/median/75th/95th/99th consumer latency: 1346110/1457130/1495463/1529703/1542172 µs
id: test-141948-895, time: 17.001s, sent: 26933 msg/s, received: 26310 msg/s, min/median/75th/95th/99th consumer latency: 1333807/1411182/1442417/1467869/1483273 µs
id: test-141948-895, time: 18.001s, sent: 26292 msg/s, received: 25505 msg/s, min/median/75th/95th/99th consumer latency: 1329488/1428657/1455482/1502191/1518218 µs
id: test-141948-895, time: 19.001s, sent: 23727 msg/s, received: 26055 msg/s, min/median/75th/95th/99th consumer latency: 1355788/1450757/1480030/1514469/1531624 µs
id: test-141948-895, time: 20.001s, sent: 25009 msg/s, received: 25202 msg/s, min/median/75th/95th/99th consumer latency: 1327462/1447157/1474394/1509857/1521303 µs
id: test-141948-895, time: 21.001s, sent: 28487 msg/s, received: 25942 msg/s, min/median/75th/95th/99th consumer latency: 1350527/1454599/1490094/1519461/1531042 µs




