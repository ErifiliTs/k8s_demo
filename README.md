## DESCRIPTION 

Demo Deployment of a kubernetes cluster for a MongoDB and MongoExpress application

---

## PRE-REQUISITES

This project requires docker, minikube and kubectl to be installed.

## INFORMATION ABOUT MINIKUBE, KUBECTL AND YAML FILES

### CREATE A MINICUBE CLUSTER

#### with docker
```
minikube start --driver=docker --v=1 –alsologtostderr
```
#### with hyperkit

```
minikube start --driver=hyperkit --v=1 –alsologtostderr 
```

### MINIKUBE CLUSTER

```
minikube get status
minikube stop
minikube delete
minikube service mongo-express-service
minikube logs
```

### KUBECTL COMMANDS

```
kubectl create deployment [NAME] [--image=image]
kubectl create deployment nginx–depl --image=nginx
kubectl create deployment mongo-depl --image=mongo
kubectl delete deployment [pod name] [--image=image]

kubectl get deployment

kubectl edit deployment [pod name]
kubectl edit deployment nginx-depl

kubectl apply -f mongo.yaml
kubectl apply -f mongo-configmap.yaml
kubectl apply -f mongo-express.yaml
kubectl apply -f mongo-secret.yaml

kubectl get pod
kubectl get pod --watch
kubectl get pod -o wide

kubectl get all
kubectl get all | grep mongodb

kubectl get nodes
kubectl get service
kubectl get secret
kubectl get replicaset

kubectl logs [pod name]
kubectl exec -it [pod name] -- bin/bash

kubectl describe pod [pod name]
kubectl describe service mongodb-service
```

### STORING DATA IN A SECRET COMPONENT

Secret must be created before the Deployment	

```
echo -n 'username' | base64 
echo -n 'password' | base64 
```
---
---
## STRUCTURE OF YAML FILES

Each configuration file declares:

- whether it is a Deployment or a service
- the metadata which is the name of the app
- the attributes (spec) which are specific to the kind 
  - has its own metadata and spec section
  - it is a blueprint of a pod
- the status which is generated automatically by kubernetes

## BLUEPRINT FOR PODS

Deployment manages a Replicaset and the Replicaset manages a pod which is the smallest unit of a kubernetes cluster.

There is an abstraction layer over the pods that is called deployment. This is what is being created and that's what is going to create the pods underneath. Instead of working with pods we are working with deployments.

## Connecting Deployment with pods

## Labels & Selectors

Pods get the label through the template.

## Connecting Services to Deployments

The selector in the service is matched with the label in the Deployment.

## Ports in the Service and Pod

The targetPort in the service should match the ContainertPort in the deployment.

## MongoDB Internal Service

Deployment & Service combined together in mongo.yaml

## Mongo Express Deployment & Service ConfigMap

Requires 3 types of information 

- a database to connect
- a mongoDB Address/Internal Service
- credentials to authenticate the connection

## ConfigMap

- external configuration
- centralized
- other components can use this configuration

configmap.yaml must already be in the k8s cluster when referencing it in the mongo-express.yaml

The metadata and the data of the configmap are being referenced in the environment variables of mongo-express.yaml

## Mongo Express External Service

In the service the type of the specification should be LoadBbalancer
to accept external requests by assigning the service an external IP address (Internal Service or Cluster IP is default)

Pending means it doesn't have a public external IP address yet.
After creating the monog-express-service assign external service a public IP address

---
## BROWSER REQUEST THROUGH KUBERNETES CLUSTER

When the request comes from the browser goes to the external service of the MongoExpress which then will be forwarded to the MongoExpress Pod. The Pod will then connect to internal service of MongoDB (database_url) and it will be forwarded to MongoDB Pod
which will authenticate the request using the credentials.