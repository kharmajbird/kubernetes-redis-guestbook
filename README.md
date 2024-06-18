#!/bin/bash

## Kubernetes Guestbook app
## + minikube + PHP + Redis
##
## localhost:8080



# Deploy Redis controller for a single replica pod

kubectl apply -f\
     https://k8s.io/examples/application/guestbook/redis-leader-deployment.yaml


# Create Redis leader service
# Defines a policy to access pods via a proxy

kubectl apply -f\
    redis-leader-service.yaml


# Set Redis followers/replicas

kubectl apply -f\
    redis-follower-deployment.yaml


# Create Redis follower service

kubectl apply -f\
    redis-follower-service.yaml


# Expose the guestbook frontend
# The PHP frontend balances between the leader and follower(s) via a
#   JSON interface serving as a jQuery-AJAX UI

kubectl apply -f\
    frontend-deployment.yaml

kubectl get pods \
    -l app=guestbook \
    -l tier=frontend

echo "Wait until pods are built before continuing"
echo
echo "kubectl get pods \"
echo "-l app=guestbook \"
echo "-l tier=frontend"
echo
sh


# Create the frontend service

kubectl apply -f\
    frontend-service.yaml


# view the frontend service
# this will appear to hang until CTRL-C

kubectl port-forward svc/frontend 8080:80


exit
# cleanup

kubectl delete deployment -l app=redis
kubectl delete service -l app=redis
kubectl delete deployment frontend
kubectl delete service frontend
