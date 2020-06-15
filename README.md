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

For a cluster administrator, create another flux to monitor and create applications

```
kubectl create namespace flux-apps

fluxctl install \
    --git-url=git@github.com:${GHUSER}/${REPO}.git \
    --git-path=apps \
    --git-readonly \
    --namespace=flux-apps | kubectl apply -f -
# Above command is the same as: kubectl apply -f ./bootstrap/02-apps-flux.yaml

# Find the SSH key generated when starting flux
fluxctl identity --k8s-fwd-ns flux-apps

# Again add this as a readonly deployment key in GitHub
```

Create a folder underneath `apps` for example `./apps/podinfo/` that will contain manifests related to the deployment of the app

Create a matching namespace at the cluster level, for example: `./cluster/namespaces/<name>.yaml`