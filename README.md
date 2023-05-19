# ArgoCD Local Test environment

This repository contains some test scenarios to setup a Kubernetes cluster from scratch in a local environment.

*Important*
Do not use anything you find in the repository in production. This is purely a testing playground!
*Important*

## Prerequisites

The following software is required to test out the scenarios:

* kind (Create and manage the local test clusters)
* helm (Install argocd to the test cluster)
* kubectl (Applying changes to the test cluster as well as some management tasks)
* go-task (Running the predefined tasks, similar to GNU make)

## go-task Targets

|Command|Description|Parameters|
|--|--|--|
|cluster:create|Create a new kind cluster with ArgoCD installed|cluster_name (default: argotest)|
|cluster:delete|Delete a kind test cluster|cluster_name (default: argotest)|
|argo:install|Installs or upgrades ArgoCD on the given test cluster|cluster_name (default: argotest)|
|argo:forward|Forward the ArgoCD API Server in a given test cluster to a local port|cluster_name (default: argotest), port (default: 8080)|
|argo:password|Print the initial ArgoCD admin password in a given test cluster|cluster_name (default: argotest)|
|scenario:helm|Install the helm chart scenario to the given test cluster|cluster_name (default: argotest)|
|scenario:helm-multisource|Install the helm chart scenario with seperate source for values to the given test cluster|cluster_name (default: argotest)|
|scenario:helm-multisource-full-bootstrap|Install the helm chart scenario with seperate source for values and also bootstrap argocd and some apps to the given test cluster|cluster_name (default: argotest)|
|scenario:manifests|Install the raw manifest scenario to the given test cluster|cluster_name (default: argotest)|
|scenario:manifests-multisource|Install the raw manifest scenario with seperate source for values to the given test cluster|cluster_name (default: argotest)|

