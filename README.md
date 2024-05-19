# Argo with Flux
This repository contains patches to bridge Flux controlled resource with Argo app.

# Why vanilla Argo can't work with Flux resources
By default, **Argo** show nested resource(implicitly created by some manifest from app) **using
ownerReference metadata** from cluster (as main path) and some corner case logic for custom
kind of resource. Unfortunately **Flux don't use this logic** because child of Flux resource
(HelmRelease, as example) may be namespace-scoped OR cluster-wide, but ownerReference can't
refer between namespaces or between cluster-scoped and namespace-scoped resource. Instead of
ownerReference Flux use `helm.toolkit.fluxcd.io/name`/`kustomize.toolkit.fluxcd.io/name`
labels to track parent resource(Kustomization/HelmRelease).

To solve this problem we need
* Patch gitops-engine(core of Argo) to implement owner referencing by Flux labels
* Recompile Argo with local version of gitops-engine

# Versioning
Example tag: `v2.11.0.awf.g01.a01-main-bac623a5`
This tag represents a specific version with the following components:

- `v2.11.0`: Base version of ArgoCD
- `awf`: Static suffix indicates that build support "Argo with Flux" logic
- `g01`: Patch level for gitops-engine
- `a01`: Patch level for Argo
- `main`: Git branch where the changes are made
- `bac623a5`: Commit identifier for the specific changes made in this version
