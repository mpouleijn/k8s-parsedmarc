# Kubernetes 1.21.1 parseDMARC Guide

Create a cluster with [kind](https://kind.sigs.k8s.io/docs/user/quick-start/)
```
kind create cluster --name prometheus --image kindest/node:v1.21.1
```

**Note:** The K8s storageclass, pvc manifest files are based on Azure AKS, if you use an other cloud or bare metal than you should update the manifests.

## K8s pre-install

This project will be managed by the GitOps principles. This means that the entire state off the application will be stored in git.

### Argo CD installation

```bash
kubectl create namespace argocd
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
```

### Sealed Secret Installation

Client side - Install client-side tool into /usr/local/bin/:

```Bash
wget https://github.com/bitnami-labs/sealed-secrets/releases/download/v0.16.0/kubeseal-linux-amd64 -O kubeseal
sudo install -m 755 kubeseal /usr/local/bin/kubeseal
```

Cluster side - Install SealedSecret CRD, server-side controller into kube-system namespace.

```bash
kubectl apply -f https://raw.githubusercontent.com/mpouleijn/parsedmarc/k8s/manifests/sealed-secrets-app.yaml
```

## Set/update application config

Create a encrypted secret for the GeoIP license keys
```shell
kubectl -n parsedmarc create secret generic geoip --dry-run=client --from-literal account_id=xxxxxx  --from-literal license_key=xxxxx --from-literal frequency=24 --output json | kubeseal | tee k8s/manifests/geoip/secret.yaml
```

## Install parseDMARC related apps
Finally we can deploy all releated parseDMARC applications to the K8s cluster \0/
```shell
kubectl apply -f https://raw.githubusercontent.com/mpouleijn/parsedmarc/k8s/manifests/apps.yaml
```