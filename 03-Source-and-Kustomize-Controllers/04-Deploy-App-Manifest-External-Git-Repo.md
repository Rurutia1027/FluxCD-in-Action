# LAB 3 - Deploy Application Manifest - External Git Repo

For this lab, a single node cluster has been provisioned for you. We have already installed Flux CLI and also provisioned Gitea (a self-hosted Git service as an alternative to GitHub).


You can access your Gitea account using the following credentials:

```
Username: bob
Password: Bob_pass123
```

## Practice 1

In this step, we will configure Flux server. Setup the Flux server using the flux bootstrap command with the following specifications:

username: bob
password: Bob_pass123
Path: flux-clusters/dev-cluster
branch: master
url: https://git.example.com/bob/block-buster.git


```shell
flux bootstrap git \
--url=https://git.example.com/bob/block-buster.git \
--username=bob --password=Bob_pass123 \
--branch=master --token-auth=true \
--path=flux-clusters/dev-cluster \
--ca-file=/var/lib/gitea/custom/cert.pem
► cloning branch "master" from Git repository "https://git.example.com/bob/block-buster.git"
✔ cloned repository
► generating component manifests
✔ generated component manifests
✔ committed component manifests to "master" ("8836ef8d5ca09abe51b8d8029c82156333fc213e")
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
✔ committed sync manifests to "master" ("0e56fc1db20a7092520186cbb9e09f66ee8dfa8e")
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

controlplane ~ ➜  ls
bb-app-source  block-buster  newfolder

controlplane ~ ➜  cd block-buster/

controlplane block-buster on  master ➜  ls
flux-example.yaml

controlplane block-buster on  master ➜  git pull 
remote: Enumerating objects: 14, done.
remote: Counting objects: 100% (14/14), done.
remote: Compressing objects: 100% (8/8), done.
remote: Total 13 (delta 0), reused 0 (delta 0), pack-reused 0
Unpacking objects: 100% (13/13), 58.37 KiB | 776.00 KiB/s, done.
From https://git.example.com/bob/block-buster
   922e9dd..0e56fc1  master     -> origin/master
There is no tracking information for the current branch.
Please specify which branch you want to merge with.
See git-pull(1) for details.

    git pull <remote> <branch>

If you wish to set tracking information for this branch you can do so with:

    git branch --set-upstream-to=origin/<branch> master


controlplane block-buster on  master ✖ git pull origin master
From https://git.example.com/bob/block-buster
 * branch            master     -> FETCH_HEAD
Updating 922e9dd..0e56fc1
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

To configure flux to pull from the external "bb-app-source" repository, follow these steps:


Generate a flux source with the specified specifications:

Name: 2-demo-source-git-bb-app

URL: https://github.com/sid-demo/bb-app-source

Branch: Use the 2-demo branch.

Timeout: Set it to 10 seconds.

```shell 
controlplane ~ ➜  flux create source git 2-demo-source-git-bb-app \
>  --url=https://github.com/sid-demo/bb-app-source \
  --branch=2-demo \
  --interval=10s \
> --export > ~/block-buster/flux-
flux-clusters/     flux-example.yaml  
> --export > ~/block-buster/flux-
flux-clusters/     flux-example.yaml  
> --export > ~/block-buster/flux-clusters/dev-cluster/2-demo-source-git-bb-app.yml 

controlplane ~ ➜  cat /block-buster/flux-clusters/dev-cluster/2-demo-source-git-bb-app.yml 
cat: /block-buster/flux-clusters/dev-cluster/2-demo-source-git-bb-app.yml: No such file or directory

controlplane ~ ✖ cat ~/block-buster/flux-clusters/dev-cluster/2-demo-source-git-bb-app.yml 
---
apiVersion: source.toolkit.fluxcd.io/v1
kind: GitRepository
metadata:
  name: 2-demo-source-git-bb-app
  namespace: flux-system
spec:
  interval: 10s
  ref:
    branch: 2-demo
  url: https://github.com/sid-demo/bb-app-source
```

## Practice 4 

To apply the manifests from the external "bb-app-source" repository using Flux, you need to create a Flux Kustomization with the following specifications: 

Name: 2-demo-kustomize-git-bb-app

Source: Use the previously defined "2-demo-source-git-bb-app" to fetch the manifests.

Target Namespace: Set it to "2-demo" to specify the namespace where the resources will be applied.

Timeout: Set the timeout to 10 seconds

Path: Specify "manifests" as the path to the directory where the manifests are located within the "bb-app-source" repository.

Prune: Enable this option by setting it to "true".

Export Path: Export the Kustomization configuration to the following path: 
```
~/block-buster/flux-clusters/dev-cluster/2-demo-kustomize-git-bb-app.yml
```

```shell
flux create kustomization 2-demo-kustomize-git-bb-app \
 --source GitRepository/2-demo-source-git-bb-app \
 --prune true \
 --interval 10s \
 --target-namespace 2-demo \
 --path manifests  \
 --export > ~/block-buster/flux-clusters/dev-cluster/2-demo-kustomize-git-bb-app.yml


controlplane ~ ➜  ls ~/block-buster/flux-clusters/dev-cluster/
2-demo-kustomize-git-bb-app.yml  2-demo-source-git-bb-app.yml  flux-system
```


## Practice 5 

Now that we have added two new files to the local block-buster repo, Add, commit and push the files to the remote block-buster repository.

```shell
controlplane block-buster on  master [?] ➜  git status
On branch master
Untracked files:
  (use "git add <file>..." to include in what will be committed)
        flux-clusters/dev-cluster/2-demo-kustomize-git-bb-app.yml
        flux-clusters/dev-cluster/2-demo-source-git-bb-app.yml

nothing added to commit but untracked files present (use "git add" to track)

controlplane block-buster on  master [?] ➜  git add -A

controlplane block-buster on  master [+] ➜  git commit -m 'feat: update source and customize sources'
[master f11df81] feat: update source and customize sources
 2 files changed, 25 insertions(+)
 create mode 100644 flux-clusters/dev-cluster/2-demo-kustomize-git-bb-app.yml
 create mode 100644 flux-clusters/dev-cluster/2-demo-source-git-bb-app.yml

controlplane block-buster on  master ➜  git push origin 
fatal: The current branch master has no upstream branch.
To push the current branch and set the remote as upstream, use

    git push --set-upstream origin master


controlplane block-buster on  master ✖     git push --set-upstream origin master
Enumerating objects: 9, done.
Counting objects: 100% (9/9), done.
Delta compression using up to 16 threads
Compressing objects: 100% (5/5), done.
Writing objects: 100% (6/6), 833 bytes | 833.00 KiB/s, done.
Total 6 (delta 0), reused 0 (delta 0), pack-reused 0
remote: . Processing 1 references
remote: Processed 1 references in total
To https://git.example.com/bob/block-buster.git
   0e56fc1..f11df81  master -> master
Branch 'master' set up to track remote branch 'master' from 'origin'.
```


## Practice 6 

As soon as the manifests are pushed to the repository, Flux will pull manifests and reconcile the cluster to deploy all the resources (here resource manifests are from an external repo).


The manifests we pushed to repo will create a namespace (2-demo), a deployment, and a service.




```shell 
controlplane block-buster on  master ➜  kubectl get ns
NAME              STATUS   AGE
2-demo            Active   15s
default           Active   57m
flux-system       Active   18m
kube-flannel      Active   57m
kube-node-lease   Active   57m
kube-public       Active   57m
kube-system       Active   57m
minio-dev         Active   20m
```


## Practice 7

If Flux pulled manifests from external repo and reconciled the cluster successfully, you should be able to access the application on its NodePort.

```shell 
controlplane block-buster on  master ✖ kubectl get svc -n 2-demo 
NAME                   TYPE       CLUSTER-IP      EXTERNAL-IP   PORT(S)        AGE
block-buster-service   NodePort   172.20.47.127   <none>        80:30002/TCP   104s

```


```shell 
controlplane block-buster on  master ➜  kubectl get svc block-buster-service -n 2-demo 
NAME                   TYPE       CLUSTER-IP      EXTERNAL-IP   PORT(S)        AGE
block-buster-service   NodePort   172.20.47.127   <none>        80:30002/TCP   2m17s
```