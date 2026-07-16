# FluxCD Notes

> Personal Learning Notes
>
> Goal: Understand how FluxCD works internally and how it is used in production GitOps environments.

---

# Lesson 1 - Installing FluxCD & Bootstrap

## Concept

FluxCD is a **GitOps Continuous Delivery** tool for Kubernetes.

Instead of running `kubectl apply` or `helm install` manually, Flux continuously watches a Git repository and keeps the Kubernetes cluster synchronized with the desired state stored in Git.

**Git becomes the Single Source of Truth.**

---

## Install Flux CLI

Download and install the CLI.

Verify installation:

```bash
flux --version
```

---

## Pre-flight Checks

Before installing Flux, verify the cluster is ready.

```bash
flux check --pre
```

Checks include:

* Kubernetes version
* Cluster permissions
* Required APIs
* CRD compatibility

---

## Create Git Repository

Create a GitHub repository that will act as the GitOps repository.

Example:

```text
gitops-repository
```

---

## Create GitHub Personal Access Token (PAT)

Flux requires a GitHub PAT with permissions such as:

* Repository access
* Read / Write contents

This token is used only during bootstrap.

---

## Bootstrap Flux

Example:

```bash
flux bootstrap github \
  --owner=<github-user> \
  --repository=gitops-repository \
  --branch=main \
  --path=./clusters/staging
```

---

## What Bootstrap Does

Bootstrap performs the following:

1. Installs Flux controllers into the cluster.
2. Creates the `flux-system` namespace.
3. Generates Flux manifests.
4. Pushes the manifests into Git.
5. Configures the cluster to continuously watch Git.

---

## Important Notes

Bootstrap points the cluster to a **specific folder**.

Example:

```text
clusters/staging
```

Each Kubernetes cluster normally watches its own folder.

Example:

```text
clusters/

├── development
├── staging
└── production
```

---

## Branch Configuration

Bootstrap also specifies which Git branch this cluster follows.

Example:

```text
main
```

or

```text
staging
```

Only changes committed to the configured branch are reconciled.

---

## Reconciliation Interval

Flux continuously checks Git.

Typical interval:

```text
Every 1 minute
```

Example:

```yaml
interval: 1m
```

---

## Git Repository After Bootstrap

Bootstrap automatically commits a new folder.

```text
gitops/

└── clusters
    └── staging
        └── flux-system
```

Inside:

```text
gotk-components.yaml
gotk-sync.yaml
kustomization.yaml
```

These files describe the Flux installation and synchronization configuration.

---

## Architecture

```text
GitHub Repository
        │
        │ Bootstrap
        ▼
Flux Controllers Installed
        │
        ▼
flux-system Namespace
        │
        ▼
Cluster Watches
./clusters/staging
```

---

# Lesson 2 - Kustomize Controller

## Concept

Flux installs multiple controllers.

Important ones:

* source-controller
* kustomize-controller
* helm-controller
* notification-controller

---

## Cluster Permissions

The controllers are granted **cluster-admin** permissions (or another appropriate RBAC role depending on the deployment).

This allows them to:

* Create resources
* Update resources
* Delete resources
* Reconcile cluster state

when Git changes.

---

## Source Controller

The source-controller is responsible for downloading sources.

Examples:

* Git Repository
* Helm Repository
* OCI Repository

It continuously checks Git for changes.

---

## Kustomization Custom Resource

Flux introduces a CRD named:

```text
Kustomization
```

Example:

```yaml
apiVersion: kustomize.toolkit.fluxcd.io/v1
kind: Kustomization

metadata:
  name: staging

spec:
  interval: 1m

  sourceRef:
    kind: GitRepository
    name: flux-system

  path: ./clusters/staging

  prune: true

  wait: true
```

---

## sourceRef

The `sourceRef` tells Flux **where to fetch the manifests from**.

Example:

```yaml
sourceRef:
  kind: GitRepository
  name: flux-system
```

---

## Path

Specifies which folder to deploy.

Example:

```text
./clusters/staging
```

Only resources inside this folder are reconciled.

---

## Kustomize Build

The kustomize-controller internally performs an operation equivalent to:

```bash
kustomize build
```

This generates the final Kubernetes manifests by:

* Reading the base manifests
* Applying overlays
* Applying patches
* Producing the final desired state

It then compares that desired state with the actual cluster state and reconciles any differences.

---

## Base & Overlay Structure

Example:

```text
apps/

base/

    deployment.yaml
    service.yaml

overlays/

    development/
    staging/
    production/
```

---

## Kustomize Flow

```text
Git Repository
        │
        ▼
GitRepository Source
        │
        ▼
Kustomization
        │
        ▼
kustomize build
        │
        ▼
Final YAML
        │
        ▼
Kubernetes API Server
```

---

# Lesson 3 - Helm Controller

## Concept

Once Flux manages the cluster, you typically **stop running Helm commands manually** on the cluster.

Instead of:

```bash
helm install
helm upgrade
helm rollback
```

Flux's helm-controller performs these actions automatically.

---

## HelmRepository

A HelmRepository defines where Helm charts are stored.

Example:

```yaml
apiVersion: source.toolkit.fluxcd.io/v1
kind: HelmRepository

metadata:
  name: company-helm

spec:
  interval: 1m

  url: https://artifactory.company.com/helm
```

Examples:

* JFrog Artifactory
* Harbor
* ChartMuseum
* Bitnami Repository

---

## HelmRelease

HelmRelease tells Flux **which chart and version** to install.

Example:

```yaml
apiVersion: helm.toolkit.fluxcd.io/v2
kind: HelmRelease

metadata:
  name: nginx

spec:
  interval: 5m

  chart:

    spec:

      chart: nginx

      version: "1.2.0"

      sourceRef:

        kind: HelmRepository

        name: company-helm
```

---

## Deployment Flow

```text
Git
 │
 ▼
HelmRelease
 │
 ▼
HelmRepository
 │
 ▼
Artifactory / JFrog
 │
 ▼
Download Helm Chart
 │
 ▼
helm-controller
 │
 ▼
Helm Install / Upgrade
 │
 ▼
Kubernetes
```

---

## Important Note

Flux **does NOT watch Artifactory**.

Flux watches **Git**.

Even if a new Helm chart version is uploaded into Artifactory, Flux will continue deploying the version referenced in Git until Git changes (or unless you intentionally configure automated version selection).

**Git is always the Source of Truth.**

---

# Lesson 4 - Terraform / OpenTofu

Flux installation can also be automated using Infrastructure as Code.

Examples:

* Terraform
* OpenTofu

Example workflow:

```text
Terraform

↓

Create Kubernetes Cluster

↓

Install FluxCD

↓

Bootstrap Git Repository

↓

Flux takes over application deployments
```

---

# Production GitOps Workflow

```text
Developer
      │
      ▼
GitHub (Application Repository)
      │
      ▼
CI Pipeline (Jenkins / GitHub Actions)
      │
      ├── Build Docker Image
      │
      ├── Push Docker Image
      │        │
      │        ▼
      │     JFrog Docker Registry
      │
      ├── Package Helm Chart
      │
      └── Push Helm Chart
               │
               ▼
        JFrog Helm Repository
               │
               ▼
Update GitOps Repository
(Change HelmRelease Version)
               │
               ▼
Git Commit
               │
               ▼
Flux Detects Change
               │
               ▼
helm-controller
               │
               ▼
Helm Upgrade
               │
               ▼
Kubernetes Cluster
```

---

# Flux Controllers Summary

| Controller              | Purpose                                                                |
| ----------------------- | ---------------------------------------------------------------------- |
| source-controller       | Watches Git, Helm repositories, OCI repositories and downloads sources |
| kustomize-controller    | Builds Kustomize manifests and reconciles Kubernetes resources         |
| helm-controller         | Installs, upgrades and reconciles Helm releases                        |
| notification-controller | Sends alerts and handles events (Slack, Teams, Webhooks, etc.)         |

---

# Important Interview Notes

### Git is the Source of Truth

Flux never deploys because a Docker image or Helm chart exists.

Flux deploys because **Git changed**.

---

### HelmRepository

Represents the location of Helm charts.

Usually one HelmRepository points to one Artifactory/ChartMuseum repository and many applications share it.

---

### HelmRelease

Defines:

* Which chart
* Which version
* Which values
* Which namespace

to install.

---

### Kustomization

Defines:

* Which Git repository
* Which path
* Which interval

to reconcile.

---

### Reconciliation

Flux continuously compares:

```text
Desired State (Git)

VS

Actual State (Cluster)
```

If drift is detected, Flux automatically reconciles the cluster back to the desired state.

---

# Memory Tricks

**Flux = Watches Git**

**Kustomize = Builds YAML**

**Helm Controller = Runs Helm**

**Source Controller = Downloads Sources**

**Notification Controller = Sends Events**

**Git = Source of Truth**

**JFrog = Artifact Storage**

**Kubernetes = Runs Applications**
