# GitOps Local Test environment

This repository contains some examples to setup a local Kubernetes GitOps test environment.

**Important**  
Do not use anything you find in the repository in production. This is purely a testing playground!
**Important**

## Prerequisites

The following software is required to test out the scenarios:

* minikube (Create and manage the local test clusters)
* helm (Install argocd to the test cluster)
* kubectl (Applying changes to the test cluster as well as some management tasks)
* go-task (Running the predefined tasks, similar to GNU make)

## Gettings started

* Create a test cluster with `task cluster:create`
* Bind the ArgoCD UI and the SCM Manager to your machine with `task argo:forward` and `task scm:forward`
* Start using it 🎉

### Credentials

|Service|Username|Password|
|--|--|--|
|ArgoCD|admin|Printed after creation of cluster, can also be retrieved with `task argo:password` later|
|SCM Manager|gitops|gitops|

## Integrated SCM

This playground includes an SCM manager so you can create local repositories and connect them to your GitOps operator
and to change the default example applications created and deployed to the cluster.

You may forward the SCM to your local system using `task scm:forward` and access it under http://localhost:8081.

You may login using the user `gitops` and the password `gitops`.

### Example repositories

#### ArgoCD

##### gitops/argocd

This repository contains the default ArgoCD bootstrap files.
This is used to install ArgoCD to the cluster.
To bootstrap the `argocd` chart is installed using helm and `projects/argocd.yaml` and `applications/bootstrap.yaml` files
are applied imperatively. This will then bootstrap everything else.

Be aware that the `argocd` chart itself will from then on be managed by ArgoCD itself, so updates will be applied automatically.

**Directories**

* applications - Contains all the default gitops enabled applications
* argocd - Contains an ArgoCD wrapper chart to install the operator
* projects - Contains all the ArgoCD projects to be created

##### gitops/cluster-addons

This repository contains a more normal GitOps layout which installs all the cluster addons (ingress controller, cert manager, 
other operators, ...) and could be owned by a platform team.

**Directories**

* apps - Contains all the applications for the cluster addons (manifests, charts, value files, ...)
* argocd - Contains all the required ArgoCD manifests (mainly the `Application` resources)
* misc - Contains miscellaneous manifests which don't belong to any application (eg. shared `ConfigMap`s, `NetworkPolicies`, ...)

##### gitops/team-a and gitops/team-b

Those repositories contain a more normal GitOps layout and represents different teams deploying end-user applications.

**Directories**

* apps - Contains all the applications of the team (manifests, charts, value files, ...)
* argocd - Contains all the required ArgoCD manifests (mainly the `Application` resources)
* misc - Contains miscellaneous manifests which don't belong to any application (eg. shared `ConfigMap`s, `NetworkPolicies`, ...)

#### FluxCD

Currently FluxCD can be installed to the cluster but there are currently no examples to be applied, sorry 😢

## go-task Targets

### cluster:create

Create a new test cluster

|Parameter|Default|
|--|--|
|cluster_name|gitops-test|
|cpus|4|
|disk_size|20000mb|
|enable_argo|true|
|enable_flux|true|
|flux_version|v2.2.3|
|scmm_version|2.48.3|
|kubernetes_version|1.29.2|

### cluster:delete

Delete a given test cluster

|Parameter|Default|
|--|--|
|cluster_name|gitops-test|

### cluster:status

Show the current status of the test cluster

|Parameter|Default|
|--|--|
|cluster_name|gitops-test|

### argo:init

Initialise sample repositories in SCM manager for ArgoCD and configure the operator to pull those changes.

|Parameter|Default|
|--|--|
|cluster_name|gitops-test|

### argo:forward

Forward the ArgoCD API Server in a given test cluster to a local port

|Parameter|Default|
|--|--|
|cluster_name|gitops-test|
|port|8080|

### argo:password

Print the initial ArgoCD admin password in a given test cluster

|Parameter|Default|
|--|--|
|cluster_name|gitops-test|

### flux:init

Initialise sample repositories in SCM manager for FluxCD and configure the operator to pull those changes.

|Parameter|Default|
|--|--|
|cluster_name|gitops-test|

### scm:forward

Forward the SCM Manager HTTP service in a given test cluster to a local port

|Parameter|Default|
|--|--|
|cluster_name|gitops-test|
|port|8081|
