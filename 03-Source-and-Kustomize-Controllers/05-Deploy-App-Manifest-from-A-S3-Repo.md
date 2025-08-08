# LAB 4 - Deploy Application Manifest from a S3 Repo

## Practice 1 

In this step, we will configure Flux server. Setup the Flux server using the flux bootstrap command with the following specifications:

username: bob
password: Bob_pass123
Path: flux-clusters/dev-cluster
branch: master
url: https://git.example.com/bob/block-buster.git


```shell controlplane ~ ➜  flux bootstrap git \
--url=https://git.example.com/bob/block-buster.git \
--username=bob --password=Bob_pass123 \
--branch=master --token-auth=true \
--path=flux-clusters/dev-cluster \
--ca-file=/var/lib/gitea/custom/cert.pem
► cloning branch "master" from Git repository "https://git.example.com/bob/block-buster.git"
✔ cloned repository
► generating component manifests
✔ generated component manifests
✔ committed component manifests to "master" ("ff1917111698c9d9ce117120cca818f2c8112011")
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
✔ committed sync manifests to "master" ("fcdb53178b70eb94407af2654194299ad845aaac")
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

## Practice 2 

On this lab machine, there is a cloned repository named block-buster, but some changes were made to the remote repository.


Update the local "block-buster" repository with remote changes by performing a Git pull operation from master branch. Ensure the local repository reflects the latest updates from the remote repository.

```shell 
controlplane block-buster on  master ✖ git pull origin master 
From https://git.example.com/bob/block-buster
 * branch            master     -> FETCH_HEAD
Updating 9d0e8e6..fcdb531
Fast-forward
 .../dev-cluster/flux-system/gotk-components.yaml          | 12507 +++++++++++++++++++++++
 flux-clusters/dev-cluster/flux-system/gotk-sync.yaml      |    27 +
 flux-clusters/dev-cluster/flux-system/kustomization.yaml  |     5 +
 3 files changed, 12539 insertions(+)
 create mode 100644 flux-clusters/dev-cluster/flux-system/gotk-components.yaml
 create mode 100644 flux-clusters/dev-cluster/flux-system/gotk-sync.yaml
 create mode 100644 flux-clusters/dev-cluster/flux-system/kustomization.yaml

```


## Practice 3 

In order to enable Flux to connect to MinIO and retrieve the manifests, we need to set up authentication. This involves creating a Kubernetes Secret that will store the necessary credentials for accessing MinIO.


Your task is to create K8S Secret with the following specifications:

Secret Name: minio-crds

Secret Type: Opaque

Namespace: flux-system

Secret Literals:

accesskey: minioadmin

secretkey: minioadmin


```shell 
echo -n "minioadmin" | base64
```

-  minio-secret.yaml 
```yml 
apiVersion: v1
kind: Secret
metadata:
  name: minio-crds
  namespace: flux-system
type: Opaque
data:
  accesskey: bWluaW9hZG1pbg==
  secretkey: bWluaW9hZG1pbg==
```

```shell 
kubectl apply -f  minio-secret.yaml 

controlplane ~ ➜  touch minio-secret.yaml 

controlplane ~ ➜  vim minio-secret.yaml 

controlplane ~ ➜  kubectl apply -f minio-secret.yaml 
secret/minio-crds created

controlplane ~ ➜  kubectl get secrets 
No resources found in default namespace.

controlplane ~ ➜  kubectl get secrets -n flux-system 
NAME          TYPE     DATA   AGE
flux-system   Opaque   3      9m3s
minio-crds    Opaque   2      18s

```


## Practice 4

Create a flux source to pull from Mino S3 Bucket Generate a flux source with the following spec: 

Name: 4-demo-source-minio-s3-bucket-bb-app

Bucket Name: bucket-bb-app

Secret Ref: minio-crds

Endpoint: minio.minio-dev.svc.cluster.local:9000

Provider: generic

Insecure:true

Export Path: `~/block-buster/flux-clusters/dev-cluster/4-demo-source-minio-s3-bucket-bb-app.yml`


```shell 
flux create source bucket 4-demo-source-minio-s3-bucket-bb-app \
  --bucket-name bucket-bb-app \
  --secret-ref minio-crds \  # this is the secret we just created 
  --endpoint minio.minio-dev.svc.cluster.local:9000  \
  --provider generic \
  --insecure \
  --export > ~/block-buster/flux-clusters/dev-cluster/4-demo-source-minio-s3-bucket-bb-app.yml

  controlplane ~ ➜  cat ~/block-buster/flux-clusters/dev-cluster/4-demo-source-minio-s3-bucket-bb-app.yml
---
apiVersion: source.toolkit.fluxcd.io/v1
kind: Bucket
metadata:
  name: 4-demo-source-minio-s3-bucket-bb-app
  namespace: flux-system
spec:
  bucketName: bucket-bb-app
  endpoint: minio.minio-dev.svc.cluster.local:9000
  insecure: true
  interval: 1m0s
  provider: generic
  secretRef:
    name: minio-crds
```


## Practice 5

For this step, create a flux kustomization to apply the manifests.
Generate a flux kustomization with the following spec:

Name: 4-demo-kustomize-minio-s3-bucket-bb-app

Source: Bucket/4-demo-source-minio-s3-bucket-bb-app

Target Namespace: 4-demo

Timeout: 10s

Path: manifests

Prune: true

Export Path: `~/block-buster/flux-clusters/dev-cluster/4-demo-kustomize-minio-s3-bucket-bb-app.yml`


```shell
controlplane ~ ➜   flux create kustomization 4-demo-kustomize-minio-s3-bucket-bb-app \
  --source Bucket/4-demo-source-minio-s3-bucket-bb-app \
  --target-namespace 4-demo \
  --path ./manifests \
  --prune true \
  --export > ~/block-buster/flux-clusters/dev-cluster/4-demo-kustomize-minio-s3-bucket-bb-app.yml

controlplane ~ ➜  cat ~/block-buster/flux-clusters/dev-cluster/4-demo-kustomize-minio-s3-bucket-bb-app.yml
---
apiVersion: kustomize.toolkit.fluxcd.io/v1
kind: Kustomization
metadata:
  name: 4-demo-kustomize-minio-s3-bucket-bb-app
  namespace: flux-system
spec:
  interval: 1m0s
  path: ./manifests
  prune: true
  sourceRef:
    kind: Bucket
    name: 4-demo-source-minio-s3-bucket-bb-app
  targetNamespace: 4-demo
```