# Helm Operator

# Install `HelmRelease` Custom Resource Definition

Installing this CRD will allow us to define a `HelmRelease` resource in our cluster.

```
kubectl apply -f https://raw.githubusercontent.com/fluxcd/helm-operator/1.2.0/deploy/crds.yaml
```

# Install Helm Operator

```
helm repo add fluxcd https://charts.fluxcd.io
helm upgrade -i helm-operator fluxcd/helm-operator --namespace flux --set helm.versions=v3
```

# Create HelmRelease resource

First step in automating Helm releases with Flux is to **create a Git repository with your chart's source code**. We will call this, our config repository.

This repository will also contain our HelmRelease resource:


```
apiVersion: helm.fluxcd.io/v1
kind: HelmRelease
metadata:
  name: helm-sample
spec:
  chart:
    repository: https://github.com/gonzalobarbitta/helm-operator
    ref: master
    path: charts/helm-sample
```