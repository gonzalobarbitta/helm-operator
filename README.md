# Helm Operator

### Create the flux namespace

```
kubectl create ns flux
```

### Install flux


```
export GHUSER="gonzalobarbitta"
helm upgrade -i flux fluxcd/flux --wait --namespace flux --set git.url=git@github.com:${GHUSER}/helm-operator
```

# Install `HelmRelease` Custom Resource Definition

Installing this CRD will allow us to define a `HelmRelease` resource in our cluster.

```
kubectl apply -f https://raw.githubusercontent.com/fluxcd/helm-operator/1.2.0/deploy/crds.yaml
```

# Install Helm Operator

```
helm upgrade -i helm-operator fluxcd/helm-operator --namespace flux --set helm.versions=v3
```

# Create HelmRelease resource

First step in automating Helm releases with Flux is to **create a Git repository with your chart's source code**. We will call this, our config repository.

This repository will also contain our HelmRelease resource:


```
apiVersion: helm.fluxcd.io/v1
kind: HelmRelease
metadata:
  name: helm-operator
spec:
  chart:
    repository: https://github.com/gonzalobarbitta/helm-operator
    ref: master
    path: charts/helm-operator
  values:
    image:
      repository: gonzalobarbitta/cloud-training
```

### Give write access

At startup, flux generates a SSH key. SSH public key can be found running `fluxctl identity --k8s-fwd-ns flux`.
Copy this key and create a deploy key with write access on your GitHub repository.

### Sync cluster with Git repository

```
fluxctl sync --k8s-fwd-ns flux
```

# Promoting releases between environments

Config repository would have following structure:

```
├── charts
│   └── helm-operator
│       ├── Chart.yaml
│       ├── README.md
│       ├── templates
│       └── values.yaml
├── namespaces
│   ├── dev.yaml
│   └── sbx.yaml
└── releases
    ├── dev
    │   └── helm-operator.yaml
    └── sbx
        └── helm-operator.yaml
```

Organizing our code this way would allow us to have a different configuration per environments. This is specially useful if  for instance, our sbx and dev accounts sync to different Git repositories, if there is some sort of automated load testing in dev account, or in case we do not want to promote versions to production that include breaking changes without manual intervention first.