# admiral

## Overview

This Admiral repository will guide you on creating a captain (kubernetes cluster). You will need to follow these steps in this order:

1) Create the kubernetes infra for the desired clouder provider
2) Deploy ArgoCD onto the kubernetes cluster
3) Deploying the GlueOps-Platform onto the kubernetes cluster
4) Intialize/Unseal Vault
5) Configure Vault

Once you complete steps 1-5 you will have a captain that you can deploy your apps onto.

### Deploying your cluster

**IMPORTANT: You can only use 1 Cloud Provider**

| Cloud Providers                                                                                   |
|---------------------------------------------------------------------------------------------------|
| [Google Cloud Platform](https://github.com/GlueOps/terraform-module-cloud-gcp-kubernetes-cluster) |


### Deploying ArgoCD

https://github.com/GlueOps/argocd-install-docs/

### Deploying the GlueOps Platform

https://github.com/GlueOps/docs-glueops-platform/

### Vault Setup

#### Intialize Vault

https://github.com/GlueOps/terraform-module-kubernetes-hashicorp-vault-initialization

#### Configure Vault

https://github.com/GlueOps/terraform-module-kubernetes-hashicorp-vault-configuration

## Cheat Sheet

### Using the Cluster

- Service Locations
  - **ArgoCD** - `argocd.<captain_domain>`
  - **Vault** - `vault.<captain_domain>`
  - **Grafana** - `grafana.<captain_domain>`
- Accessing Services
  - GitHub OAuth - to confirm OAuth access was configured correctly
    - **ArgoCD** - Click `LOGIN VIA GITHUB SSO` and grant access to the relevant organization(s), which were configured in the `platform.yaml` at `dex.github.orgs`
    - **Vault**  - Click on `oidc` and then type in `reader` or `editor` depending on what role you want to use
    - **Grafana** - Click `Signin with GitHub SSO` and grant access to the relevant organization(s), which were configured in the `platform.yaml` at `dex.github.orgs`
  - Admin
    - **ArgoCD** - username: `admin`, retrieve the password using:
      
      ```bash
      kubectl -n glueops-core get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d
      ```

    - **Grafana** - username: `admin`, retrieve the password using:
      
      ```bash
      kubectl -n glueops-core-kube-prometheus-stack get secret kube-prometheus-stack-grafana -o jsonpath="{.data.admin-password}" | base64 -d
      ```

### Tips

- To do much of anything, you probably need to authenticate with the K8s cluster. See the Cloud specific Authentication details **_above_**.
- Whenever running terraform against vault you need a connection to vault: `kubectl -n glueops-core-vault port-forward svc/vault-ui 8200:8200`
- When making IaC updates to the Kubernetes cluster itself (ex., new node pools or updating cluster version, VPC peering, etc.) you must authenticate to that cloud provider and those instructions will be in the terraform module that you used to deploy the cluster in the `##### Deployment` section
- Remember all commands in this document assume you are "above" the admiral folder.
