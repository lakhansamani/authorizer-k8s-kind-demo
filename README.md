# Authorizer K8S example

## Step 1: Create K8s Cluster

```sh
kind create cluster --name authorizer --config=cluster.yaml
```

## Step 2: Install external dependencies and wait for all pods to be alive

- **MetaLLB** for load balancing

```sh
kubectl apply -f https://raw.githubusercontent.com/metallb/metallb/v0.13.7/config/manifests/metallb-native.yaml
```

- **NGINX** for ingress

```sh
kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/main/deploy/static/provider/kind/deploy.yaml
```

- **Cert Manager** for https routing

```sh
kubectl apply -f https://github.com/cert-manager/cert-manager/releases/download/v1.12.0/cert-manager.yaml
```

## Step 3: Get IP Address Pool for your local machine and update `metallb.yaml`

```sh
docker network inspect -f '{{.IPAM.Config}}' kind
```

You will see response equivalent to following

```
[{172.18.0.0/16  172.18.0.1 map[]} {fc00:f853:ccd:e793::/64  fc00:f853:ccd:e793::1 map[]}]
```

Based on the above CIDR we can pick subclass range for IPAddressPool
example for above range could be `172.18.255.200-172.18.255.250`, Update this range in `metallb.yaml`

## Step 4: Add `ipaddress`

```sh
kubectl apply -f metallb.yaml
```

## Step 5: Create Namespace for Project

Note: replace namespace in all the files, `depl-id` is just for demo.

```sh
kubectl apply -f namespace.yaml
```

## Step 6: Create Redis statefulset & service

```sh
kubectl apply -f redis.yaml
```

## Step 7: Create Secrets for Database / other env variables

```sh
kubectl apply -f secrets.yaml
```

## Step 8: Create Authorizer Deployment & Service

```sh
kubectl apply -f authorizer.yaml
```

## Step 9: Add cert manager issuer

```sh
kubectl apply -f issuer.yaml
```

## Step 10: Add ingress

```sh
kubectl apply -f ingress.yaml
```

## Step 11: Update `/etc/hosts`

Add the domain you have used in `ingress.yaml` to `/etc/hosts` so that we can access this demo using that domain on current machin

```
127.0.0.1 depl-id.authorizer.dev
```
