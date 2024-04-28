
## install the mongo oprator
Add the MongoDB Helm Charts for Kubernetes repository to Helm by running the following command:
helm repo add mongodb https://mongodb.github.io/helm-charts

Deploy that operator
helm install community-operator mongodb/community-operator

if all goes well , operator will be installed as below:

![alt text](image-9.png)

## install the mongo instance
go to directory mongo
cd /opt/mycode/ptr/optimizer-product-deployment/singlebox/mongo

you will see a file as below:
mongodb.com_v1_mongodbcommunity_cr.yaml

![alt text](image-10.png)

Change the password that will act as admin password of mongo as below:
![alt text](image-11.png)

In the file , i used mongo version as 4.2.6 , that only works , the latest 6.0.5 dont work.

Now you can deploy the mongo instance 

kubectl apply -f mongodb.com_v1_mongodbcommunity_cr.yaml

If all goes well , you will see the mongo db instance will 

![alt text](image-12.png)

## How to use the database

![alt text](image-16.png)

You can see the secret as below
![alt text](image-13.png)

![alt text](image-14.png)

To get the values for connection 
![alt text](image-15.png)

The above values can be used to connect the database.

