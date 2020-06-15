# GitOps

## Cluster Level Management

Basic set up

```
export GHUSER=jasoncabot-ms
export REPO=aks-arc-gitops
```

We start with a cluster level flux that will manage the creation of cluster level resources, such as RBAC, service meshes, namespaces, ingress controllers, monitoring and observability hooks.

This repository would traditionally be maintained by an operations team

```
# Create a namespace for the pods that will monitor the Git repository
kubectl create namespace flux-cluster

fluxctl install \
    --git-user=${GHUSER} \
    --git-email=${GHUSER}@users.noreply.github.com \
    --git-url=git@github.com:${GHUSER}/${REPO}.git \
    --git-path=cluster \
    --git-readonly \
    --namespace=flux-cluster | kubectl apply -f -
# Above command is the same as: kubectl apply -f ./bootstrap/01-cluster-flux.yaml

# Find the SSH key generated when starting flux
fluxctl identity --k8s-fwd-ns flux-cluster

# This key needs to be added to your GitHub repository since GitHub doesn't allow anonymous SSH clones, even of public repositories
```

## Application Management



