# Authorizer K8S example

## Step 1: Create K8s Cluster

```sh
kind create cluster --name authorizer --config=cluster.yaml
```

## Step 2: Create Namespace for Project

Note: replace namespace in all the files, `my-auth` is just for demo.

```sh
kubectl apply -f namespace.yaml
```

## Step 3: Create Redis statefulset & service

```sh
kubectl apply -f redis.yaml
```

## Step 4: Create Secrets for Database / other env variables

```sh
kubectl apply -f secrets.yaml
```

## Step 5: Create Nginx ingress controller

Ref: https://kind.sigs.k8s.io/docs/user/ingress/#ingress-nginx
this only applies to kind cluster

```sh
kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/main/deploy/static/provider/kind/deploy.yaml

kubectl wait --namespace ingress-nginx \
  --for=condition=ready pod \
  --selector=app.kubernetes.io/component=controller \
  --timeout=90s

```

## Step 6: Create Authorizer Deployment & Service

```sh
kubectl apply -f authorizer.yaml
```
