# Introduction

This repo has been used to understand [Kustomize](https://kustomize.io/) with [Devspace](https://devspace.sh/).

Kustomize is a tool for customizing Kubernetes configurations. It has the following features to manage application configuration files:
* generating resources from other sources.
* using patches to introduce environment specific changes on an already existing standard config file without disturbing it.
* composing and customizing collections of resources.

# Usage

#### Kustomize file structure for this project
```
.k8s
└── kustomize
    └── base
        ├── deployment.yaml
        ├── kustomization.yaml
    └── overlays
        └── prod
            ├── deployment.yaml
            ├── kustomization.yaml
        └── stage
            ├── deployment.yaml
            ├── kustomization.yaml
```

#### Base and overlays
Kustomize has the concepts of **bases** and **overlays**.
* A **base** is a directory with a `kustomization.yaml`, which contains a set of resources and associated customization. A base could be either a local directory or a directory from a remote repo, as long as a kustomization.yaml is present inside.
* An **overlay** is a directory with a `kustomization.yaml` that refers to other kustomization directories as its bases. A **base** has no knowledge of an overlay and can be used in multiple overlays. An **overlay** may have multiple bases and it composes all resources from bases and may also have customization on top of them.

#### Understand the project
`.k8s/kustomize/base/deployment.yaml` will deploy an alpine Docker image from [Docker Hub](https://hub.docker.com/_/alpine/) called **my-app** with an environment variable  `SHARED_VARIABLE`.

In that same **base** folder, we could also set up a `service.yaml`.

Each **prod** and **stage** have a different `deployment.yaml` from each other, as they deploy a separated environment variable `CONTAINERPRO_VARIABLE` and `CONTAINERSTAGE_VARIABLE`.

`patchesStrategicMerge:` from L5 `.k8s/kustomize/overlays/*/kustomization.yaml` is required as we have `deployment.yaml` from **overlay** and **base** called **my-app**.<br/>
This feature helps merging the two `deployment.yaml` together.

`resources:` would be used to include an `.yaml` file that isn't merged with another one.<br/>

### Devspace with kustomize
Once you have your file structure organized, it's time to create a `devspace.yaml` dedicated to the environment you want to deploy to. <br/>
In our case **prod** and **stage**, each refers to their own manifests.

Run a specific `devspace.yaml`:
```
devspace deploy --config devspace-stage.yaml
```

Purge your deployment:
```
devspace purge --config devspace-stage.yaml
```

# Useful resources
https://kubernetes.io/docs/tasks/manage-kubernetes-objects/kustomization/

https://kubectl.docs.kubernetes.io/guides/introduction/kustomize/
