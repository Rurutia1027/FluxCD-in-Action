# FluxCD Concepts & Terminology 
- Bootstrap: Process of installing the Flux components in a GitOps manner. 

- Agent: Flux's controllers are "software agents" that implement a control loop.

- Controller: Flux controllers (Kubernetes controller pattern) use a control loop along with custom resource definitions to manage an aspect of cluster state. 

--- 

- Sources: Defines the origin of a repository containing the desired state of the system and the requirements to obtain it. 

- Kustomization: Represents a local set of kustomize overlay resources that Flux is supposed to reconcile in the cluster. 

---

- Reconciliation: Ensuring that a given state (e.g., application running in the cluster, infrastructure) matches a desired state declaratively defined in Git. 

- Suspend: Suspends the reconciliation of a resource. 

- Resume: Resume a suspended resource

--- 

- Prune: Any Kubernetes objects applied on the cluster but missing from the current source, are removed from cluster automatically if prune is set to true.  

- Image: Flux Image command works with image automation objects to update a Git repository when new container images are available. 