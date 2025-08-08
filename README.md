# FluxCD-in-Action

## Description

GitOps is a framework where the entire code delivery process is controlled via a Git repository. GitOps operators such as Flux automatically pull the desired state from Git and apply it to Kubernetes clusters, enabling continuous deployment and infrastructure management.

---

## Features of Flux

- Provides GitOps for both applications and infrastructure  
- Automates deployments: just push to Git and Flux does the rest  
- Designed with security in mind; supports encrypting/decrypting secrets  
- Integrates with Kubernetes tools like Kustomize, Helm, RBAC, and OPA policies  
- Supports alerts and notifications to third-party systems  

---

## Topics Covered

- Source Controller  
- Kustomize Controller — Concepts introduction && Usage Practice Demo 
- Helm Controller  - Concepts introduction && Usage Practice Demo 
- Notification Controller  — Concepts introduction && Usage Practice Demo 
- Image Reflector Controller  — Concepts introduction && Usage Practice Demo 
- Image Automation Controller  — Concepts introduction && Usage Practice Demo 
- Monitoring and User Interfaces  
- Secret Management & Sign Verification  

---

## Intended Audience

- Cluster Operators  
- Developers and Architects  
- DevOps and SRE Engineers  
- Anyone interested in automating application deployment  

---

## About FluxCD-in-Action

This repository contains notes and key concepts around FluxCD and GitOps workflows, covering installation, configuration, and usage of various Flux controllers. It serves as a reference for understanding Flux’s components and how they contribute to GitOps automation.


---

## References 
- [KodeKloud-GitOps with FluxCD](https://learn.kodekloud.com/user/courses/gitops-with-fluxcd)