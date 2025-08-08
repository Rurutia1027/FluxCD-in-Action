# Kustomization Controller 

The Kustomize Controller in FluxCD applies Kubernetes manifests from a specified source using Kustomize overlays.
It continuously reconciles the cluster state with the manifests, ensuring resources match the desired configuration.

In Kustomize, an overlay is like a customized layer you put on top of a base configuration — a bit like adding toppings to a pizza without changing the dough recipe. 

**Kustomize overlays are conceptually similar to Maven's profiles.**