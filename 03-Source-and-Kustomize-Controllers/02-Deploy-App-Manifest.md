# Lab2 - Deploy Application Manifest - Flux Repo 

## Background 

For this lab, a single node cluster has been provisioned for you. We have already installed Flux CLI and also provided Gitea (a self-hosted Git service as an alternative to GitHub).

You can access your Gitea account using the following credentials:

```
Username: bob

Password: Bob_pass123
```


## Practice 1

In this step, we will configure Flux server. Setup the Flux server using the `flux bootstrap` command with the following specifications: 

```
username: bob
password: Bob_pass123
Path: flux-clusters/dev-cluster
branch: master
url: https://git.example.com/bob/block-buster.git
```

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
✔ committed component manifests to "master" ("9a9e114cce22bb1070513ae0ba2e45ea5c453a2e")
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
✔ committed sync manifests to "master" ("2fcf3b558f8d6737661dbcc235b1caed03aaef5b")
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
On this lab machine, there is a cloned repository named `bb-app-source` containing manifests necessary for deploying an application. 

Inspect the application manifests and identify the image used in the deployment. 

__Application declaration detail is located in deployment.yml this yaml file.__

```shell 
controlplane ~ ✖ ls
bb-app-source  block-buster  newfolder

controlplane ~ ➜  cd bb-app-source/

controlplane bb-app-source on  1-demo ➜  ls
manifests  src

controlplane bb-app-source on  1-demo ➜  cd manifests/

controlplane bb-app-source/manifests on  1-demo ➜  ls
deployment.yml  namespace.yml  service.yml

controlplane bb-app-source/manifests on  1-demo ➜  cat deployment.yml 

controlplane bb-app-source/manifests on  1-demo ➜  cat deployment.yml | grep siddharth
          image:  siddharth67/block-buster-dev:7.1.0
```


## Practice 3

On this lab machine, there is a cloned repository named "block-buster", but some changes were made to the remote repository. 

Update the local "block-buster" repository with remote changes by performing a `Git pull` operation from `master` branch. 

Ensure the local repository reflects the latest updates from the remote repository. 

```shell 
controlplane ~ ➜  ls
bb-app-source  block-buster  newfolder

controlplane ~ ➜  cd block-buster/

controlplane block-buster on  master ➜  ls
flux-example.yaml

controlplane block-buster on  master ✖ git pull origin master 
From https://git.example.com/bob/block-buster
 * branch            master     -> FETCH_HEAD
Updating 418f7ae..2fcf3b5
Fast-forward
 flux-clusters/dev-cluster/flux-system/gotk-components.yaml | 12507 ++++++++++++++++++++++++++++++++++++++++++
 flux-clusters/dev-cluster/flux-system/gotk-sync.yaml       |    27 +
 flux-clusters/dev-cluster/flux-system/kustomization.yaml   |     5 +
 3 files changed, 12539 insertions(+)
 create mode 100644 flux-clusters/dev-cluster/flux-system/gotk-components.yaml
 create mode 100644 flux-clusters/dev-cluster/flux-system/gotk-sync.yaml
 create mode 100644 flux-clusters/dev-cluster/flux-system/kustomization.yaml
```

## Practice 4
For this step, Create a folder named 1-demo within the flux-clusters/dev-cluster directory of the repository "block-buster."


Also copy the manifests (deployment.yml, namespace.yml, and service.yml) from the "bb-app-source" repository into the newly created 1-demo folder. Ensure that the folder structure matches the specified format:

```shell 
controlplane flux-clusters/dev-cluster/1-demo on  master ➜  cp ../../../../bb-app-source/manifests/
deployment.yml  namespace.yml   service.yml     

controlplane flux-clusters/dev-cluster/1-demo on  master ➜  cp ../../../../bb-app-source/manifests/* . 

controlplane flux-clusters/dev-cluster/1-demo on  master [?] ➜  ls
deployment.yml  namespace.yml  service.yml
```


## Practice 5

Push the newly added manifests in block-buster repo to the master branch of remote repository. Verify that the push operation is successful by checking the remote repository for the updated files and folder structure.

```shell 
controlplane block-buster/flux-clusters/dev-cluster on  master [?] ➜  ls
1-demo  flux-system

controlplane block-buster/flux-clusters/dev-cluster on  master [?] ➜  git status
On branch master
Untracked files:
  (use "git add <file>..." to include in what will be committed)
        1-demo/

nothing added to commit but untracked files present (use "git add" to track)

controlplane block-buster/flux-clusters/dev-cluster on  master [?] ➜  git add -A

controlplane block-buster/flux-clusters/dev-cluster on  master [+] ➜  git commit -m 'update deployment yml files'
[master f63665c] update deployment yml files
 3 files changed, 74 insertions(+)
 create mode 100644 flux-clusters/dev-cluster/1-demo/deployment.yml
 create mode 100644 flux-clusters/dev-cluster/1-demo/namespace.yml
 create mode 100644 flux-clusters/dev-cluster/1-demo/service.yml

controlplane block-buster/flux-clusters/dev-cluster on  master ➜  git push origtin 
fatal: 'origtin' does not appear to be a git repository
fatal: Could not read from remote repository.

Please make sure you have the correct access rights
and the repository exists.

controlplane block-buster/flux-clusters/dev-cluster on  master ✖ git push origin 
fatal: The current branch master has no upstream branch.
To push the current branch and set the remote as upstream, use

    git push --set-upstream origin master


controlplane block-buster/flux-clusters/dev-cluster on  master ✖ git push --set-upstream origin master
Enumerating objects: 11, done.
Counting objects: 100% (11/11), done.
Delta compression using up to 16 threads
Compressing objects: 100% (7/7), done.
Writing objects: 100% (8/8), 1.16 KiB | 1.16 MiB/s, done.
Total 8 (delta 0), reused 0 (delta 0), pack-reused 0
remote: . Processing 1 references
remote: Processed 1 references in total
To https://git.example.com/bob/block-buster.git
   2fcf3b5..f63665c  master -> master
Branch 'master' set up to track remote branch 'master' from 'origin'.
```

## Practice 6

As soon as the manifests are pushed to the repository, Flux will pull manifests and reconcile the cluster to deploy all the manifests.


The manifests we pushed to repo will create a namespace (1-demo), a deployment, and a service.

```shell 
controlplane block-buster/flux-clusters/dev-cluster on  master ➜  kubectl get ns 
NAME              STATUS   AGE
1-demo            Active   40s
default           Active   63m
flux-system       Active   12m
kube-flannel      Active   63m
kube-node-lease   Active   63m
kube-public       Active   63m
kube-system       Active   63m

controlplane block-buster/flux-clusters/dev-cluster on  master ✖ kubectl get -n 1-demo svc block-buster-servic
e 
NAME                   TYPE       CLUSTER-IP       EXTERNAL-IP   PORT(S)        AGE
block-buster-service   NodePort   172.20.116.229   <none>        80:30001/TCP   9m52s
```
