## What is FluxCD ? 
- Flux is a tool maintaining Kubernetes clusters in sync with configurations defined on source repositories like Git, Heml, Container Image S3 buckets. 
- Continuously monitors running applications, comparing their live state to the desired state
- It reports the deviations to help developers manually or automatically synchronize the live state with the desired state 

## Why use FluxCD ? 
- It extends the benefits of declarative specifications and Git-based configuration management.
- It enables application deployment (CD) through automatic reconciliation and provides alerting, notifications.
- It can even scan image repositories and update container images automatically before pushing/commiting them back to Git repository.

## How FluxCD works? 
- It follows the **GitOps** pattern by using Git repositories as the source of truth for the desired state of application and infrastructure. 
- Bootstrap | Git Repository | Heml Chart / Repository | OCI Repository | S3 Buckets
- Based on the resources it is either going to use Helm Controller or Kustomize Controller to deploy and manage applications. 