# argocd-multiple-sources-same-repo
This repo is intended to validate if the ArgoCD upstream issue ([here](https://github.com/argoproj/argo-cd/issues/25605)) has been resolved.

## structure
each chart under the `charts` directory is a Helm umbrella chart; this means that the chart has a dependency to some upstream chart. In this repository `reloader` is a valid umbrella chart, however `umbrella-sample` is not. the `hack` folder contains manifests used to bootstrap a testing environment
```
> tree . -L 2 -d
.
├── charts
│   ├── reloader
│   └── umbrella-sample
└── hack
    └── applications
```

## testing/validating upstream issue
In order to properly test the upstream issue, we need to:
1. add this repository (public) to an ArgoCD instance
    * for testing, its recommended to set up an ArgoCD environment in accordance to [upstream docs](https://argo-cd.readthedocs.io/en/stable/developer-guide/development-environment/)
    * this can be added by running `kubectl apply -f hack/repository.yaml`
2. apply the ArgoCD application manifest for `reloader`
    * this can be added by running `kubectl apply -f hack/application/reloader.yml`

> Note: you can quickly bootstrap the repository and argocd application by running `kustomize build hack | kubectl apply -f -`

If the implementation of git worktree is working correctly, then the error `cannot reference a different revision of the same repository...` should no longer be seen, and the values applied for the `reloader` umbrella chart should show the resources requests/limits for the deployment as the below (these are the values from `charts/reloader/values-dev.yaml`):
```
resources:
  limits:
    memory: "256Mi"
  requests:
    cpu: "50m"
    memory: "256Mi"
```
