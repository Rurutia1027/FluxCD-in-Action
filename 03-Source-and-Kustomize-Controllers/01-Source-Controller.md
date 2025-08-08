# Source Controller 

The Source Controller in FluxCD fetches and monitors external sources like Git repositories and Helm charts, making their content available inside the cluster. It ensures Flux stays updated by tracking changes and syncing source data for deployment controllers. 


## Demo for Source Controller YAML 

### GitRepository Source 

This resource tells the Source Controller where to fetch the Git repository from. It defines the repo URL, branch, sync interval, and authentication method. The Source Controller uses this to clone and keep the repo content updated inside the cluster. 

```yml 
apiVersion: source.toolkit. fluxcd.io/v1beta2
kind: GitRepository
metadata:
  name: flux-system         # name of the GitRepository resource
  namespace: flux-system    # namespce where Flux components run 
spec:                       
  interval: 10s             # how often Flux syncs with the Git repo
  ref:                      # git branch to track 
    branch: main            
  secretRef:
    name: flux-system       # secret containing SSH credentials for access 
  url: ssh://git@github.com/sidd-harth-2/block-buster   # git repo URL 

```


### Kustomization 

This resource instructs Flux how to apply Kubernetes manifests from a specific path within a source (like a GitRepository). It defines where to find the manifests (path), how often to sync (interval), and which source to use (sourceRef). it also enables pruning of resources that are removed from Git. 

```yml
apiVersion: kustomize.toolkit.fluxcd.io/v1beta2
kind: Kustomization
metadata: 
  name: flux-system             # name of the Kustomization resource
  namespace: flux-system        # namespace 
spec:
  interval: 10s                              # frequency to reconcile and apply changes 
  path: ./flux-cluster/dev-cluster           # path inside the Git repo with manifests 
  prune: true                                # enable removal of resources not in manifests
  sourceRef:
    kind: GitRepository                      # reference to the source kind 
    name: flux-system                        # name of the GitRepository to use 
```

