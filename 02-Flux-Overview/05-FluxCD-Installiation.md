## FluxCD Installation && Practices 

### Practice 1

Now Flux is already installed in the lab env. Check it's version. 

```shell
controlplane ~ ✖ flux -v
flux version 2.4.0
```


### Practice 2

Check whether the Kubernetes version compatible with Flux. 

```shell 
controlplane ~ ➜   flux check --pre 
► checking prerequisites
✗ flux 2.4.0 <2.6.4 (new CLI version is available, please upgrade)
✔ Kubernetes 1.31.0 >=1.28.0-0
✔ prerequisites checks passed
```

### Practice 3 

In this step, let's will configure Flux server. Setup the Flux server  using the `flux bootstrap` command with the following specificiations. 

```
username: bob
password: Bob_pass123
Path: flux-clusters/dev-cluster 
branch: master
url: https://git.example.com/bob/block-buster.git
```

```shell
controlplane ~ ➜  flux bootstrap git \
--url=https://git.example.com/bob/block-buster.git \
--username=bob --password=Bob_pass123 \
--branch=master --token-auth=true \
--path=flux-clusters/dev-cluster \
--ca-file=/var/lib/gitea/custom/cert.pem 
► cloning branch "master" from Git repository "https://git.example.com/bob/block-buster.git"
✔ cloned repository
► generating component manifests
✔ generated component manifests
✔ committed component manifests to "master" ("f509c2e6978b6f10ef8e58952a0bd2875b986c60")
► pushing component manifests to "https://git.example.com/bob/block-buster.git"
► installing components in "flux-system" namespace
✔ installed components
✔ reconciled components
► determining if source secret "flux-system/flux-system" exists
► generating source secret
► applying source secret "flux-system/flux-system"
✔ reconciled source secret
► generating sync manifests
✔ generated sync manifests
✔ committed sync manifests to "master" ("9d249ba0e04960954ab35f1110c890b4619a9999")
► pushing sync manifests to "https://git.example.com/bob/block-buster.git"
► applying sync manifests
✔ reconciled sync configuration
◎ waiting for GitRepository "flux-system/flux-system" to be reconciled
✔ GitRepository reconciled successfully
◎ waiting for Kustomization "flux-system/flux-system" to be reconciled
✔ Kustomization reconciled successfully
► confirming components are healthy
✔ helm-controller: deployment ready
✔ kustomize-controller: deployment ready
✔ notification-controller: deployment ready
✔ source-controller: deployment ready
✔ all components are healthy
```

### Practice 4

The bootstrap command also deploys a `flux-system` namespace along with some resources in it. 
Explore the `flux-system` namespace and select the number of pods that are present in that namespace. 

```shell 
controlplane ~ ➜  kubectl get pods -n flux-system --no-headers | wc -l 
4

controlplane ~ ➜  kubectl get pods -n flux-system --no-headers 
helm-controller-7f788c795c-c8kvf           1/1   Running   0     3m34s
kustomize-controller-b4f45fff6-kxzhk       1/1   Running   0     3m34s
notification-controller-556b8867f8-rzw2t   1/1   Running   0     3m34s
source-controller-77d6cd56c9-x6qw4         1/1   Running   0     3m34s

```

### Practice 5

Bootstrap command also creates some CRDs in the flux-system namespace. 

Explore them and select the corresponding number of CRDs installed in that namespace. 

```shell 
controlplane ~ ✖ kubectl get crd --namespace flux-system 
NAME                                         CREATED AT
alerts.notification.toolkit.fluxcd.io        2025-08-08T11:57:51Z
buckets.source.toolkit.fluxcd.io             2025-08-08T11:57:52Z
gitrepositories.source.toolkit.fluxcd.io     2025-08-08T11:57:52Z
helmcharts.source.toolkit.fluxcd.io          2025-08-08T11:57:52Z
helmreleases.helm.toolkit.fluxcd.io          2025-08-08T11:57:52Z
helmrepositories.source.toolkit.fluxcd.io    2025-08-08T11:57:52Z
kustomizations.kustomize.toolkit.fluxcd.io   2025-08-08T11:57:52Z
ocirepositories.source.toolkit.fluxcd.io     2025-08-08T11:57:53Z
providers.notification.toolkit.fluxcd.io     2025-08-08T11:57:53Z
receivers.notification.toolkit.fluxcd.io     2025-08-08T11:57:53Z

controlplane ~ ➜  kubectl get crd --namespace flux-system  --no-headers | wc -l 
10
```