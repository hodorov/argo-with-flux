# Argo with Flux
This repository contains patches to bridge Flux controlled resource with Argo app.

# Example
## Without patches
* No resource tracking
* No custom actions (reconcile/suspend)
![without_awf](img/without_awf.png)

## Patched version
* Actual status of resource from helm-controller/kustomizaion-controller by Flux
  (In example HelmRelease suspended using the appropriate button)
* All common actions available in Flux CLI may be performed via UI
* All resource created by HelmRelease tracked properly
![img.png](img/with_awf.png)

# Important notice
**HelmRelease must be v2(without beta\* suffix)**, this CR version introduced in **Flux 2.3.0**, so you
need use **at least this version**(or above)

# Why just not use WeaveWorks GitOps?
If you use Flux **without** Argo it has its own third-party UI, WeaveWorks GitOps. But developer
company was [closed](https://www.crn.com/news/cloud/2024/aws-backed-kubernetes-company-weaveworks-closes-ceo-blames-failed-m-a)
at the beginning of 2024

If you use Flux **with** Argo - two different UI tools is not so many useful without integration
between them.

Basically, **all main functionality of WeaveWorks GitOps already ported to Argo with patches**
(CR status, custom actions, tree of controlled resources from app)

# Why just not use Argo without Flux?
Argo has a custom logic about helm chart applying. Before deploy Argo render Helm chart templates,
it broke some logic of complicated charts. For example - broken hooks lifecycle, unusable lookup
functionality and some other cases. Main purpose to use Argo with Flux together - Argo deploy
static manifests, Flux deploy Helm charts

# Why vanilla Argo can't work with Flux resources?
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

# Additional benefits of patched version
* Health status for Flux resource
* Actions for Flux resource - (force) reconcile/suspend/resume from Argo UI

# Versioning
Example tag: `v2.11.0.awf.g01.a01-main-bac623a5`
This tag represents a specific version with the following components:

- `v2.11.0`: Base version of ArgoCD
- `awf`: Static suffix indicates that build support "Argo with Flux" logic
- `g01`: Patch level for gitops-engine
- `a01`: Patch level for Argo
- `main`: Git branch where the changes are made
- `bac623a5`: Commit identifier for the specific changes made in this version
