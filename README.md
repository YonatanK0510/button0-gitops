# Button0 – GitOps Repository (ArgoCD)

## Overview

This repository contains the **GitOps layer** of the Button0 project.

Its responsibility is to define **what is deployed to the Kubernetes cluster**, not how the cluster is created and not how the application is built.

Deployment is handled using **ArgoCD**, following GitOps principles:
- Git is the single source of truth
- Cluster state continuously reconciles to Git state
- No manual `kubectl apply` in production workflows

---

## What This Repository Does

This repository:
- Defines ArgoCD Applications
- Controls which Helm charts are deployed
- Controls which environments are active
- Orchestrates frontend and backend deployments

This repository does NOT:
- Provision AWS infrastructure
- Build Docker images
- Contain application source code

---

## GitOps Architecture

GitHub (button0-gitops)
↓
ArgoCD
↓
Helm Charts
↓
Kubernetes (EKS)
↓
Running Application

Any change committed to this repository is automatically picked up by ArgoCD and applied to the cluster.

---

## Repository Structure

button0-gitops/
├── environments/
│ └── dev/
│ ├── root-app.yaml
│ └── apps/
│ ├── backend.yaml
│ └── frontend.yaml

---

## Root Application (App of Apps Pattern)

### root-app.yaml

The root application is the **entry point** for ArgoCD.

It uses the **App of Apps** pattern:
- One ArgoCD Application manages other Applications
- Enables clean environment separation
- Scales well for dev / staging / prod

Responsibilities:
- Registers the environment
- Deploys child applications
- Enables automated sync

---

## Child Applications

### Backend Application

- Deploys the backend Helm chart
- Pulls images from Amazon ECR
- Targets the `button0` namespace
- Automatically syncs on Git changes

### Frontend Application

- Deploys the frontend Helm chart
- Configures Ingress routing
- Exposes the application externally via ALB
- Automatically syncs on Git changes

---

## Environments

Currently supported:
- `dev`

Planned:
- `staging`
- `prod`

Each environment will have:
- Its own root app
- Its own namespace
- Its own configuration values
- Optional separate AWS accounts

---

## Sync Policy

All applications use:

- Automated sync
- Self-healing enabled
- Pruning enabled

This ensures:
- Drift is automatically corrected
- Deleted resources are removed
- Cluster state always matches Git

---

## Deployment Flow

1. Developer pushes code to `button0` repository
2. CI builds Docker images and pushes to ECR
3. GitOps repo references updated image tag
4. ArgoCD detects change
5. Kubernetes cluster is updated automatically

No manual intervention is required.

---

## Why GitOps?

GitOps provides:
- Full audit trail
- Reproducible deployments
- Easy rollback
- Clear separation of responsibilities
- Safer production workflows

It is the industry standard for Kubernetes-based platforms.

---

## Relationship to Other Repositories

| Repository | Responsibility |
|----------|----------------|
| button0 | Application code |
| button0-infra | AWS & Kubernetes infrastructure |
| button0-gitops | Kubernetes deployments |

Each repository has a **single responsibility**.

---

## Summary

This repository represents the **deployment brain** of Button0.

It demonstrates:
- GitOps best practices
- ArgoCD App of Apps pattern
- Environment-based deployments
- Production-grade Kubernetes workflows
