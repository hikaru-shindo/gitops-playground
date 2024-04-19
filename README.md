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
* Bind the ArgoCD UI and Gitea to your machine with `task argo:forward` and `task scm:forward`
* Initialise either ArgoCD (`task argo:init`) or FluxCD (`task flux:init`) to apply the example suite to the cluster
* Start experimenting 🎉

### Credentials

| Service      | Username | Password                                                                                 |
|--------------|----------|------------------------------------------------------------------------------------------|
| ArgoCD       | admin    | Printed after creation of cluster, can also be retrieved with `task argo:password` later |
| Gitea Admin  | gitops   | gitops                                                                                   |
| Gitea ArgoCD | argocd   | argocd                                                                                   |
| Gitea FluxCD | flux2    | flux2                                                                                    |

### CLI

If you install ArgoCD and/or FluxCD special CLI pods will be created providing the respective tools in an environment 
with all the required privileges to manage the GitOps operator.

#### ArgoCD

For ArgoCD we can appropriate the deployment itself to get a shell.  
This pod was authenticated on installation automatically to ArgoCD so we do not need to install the ArgoCD CLI on our 
local machine.

```shell
# Check tool version
kubectl exec -n argocd deploy/argocd-server -- argocd version
# Start an interactive shell
kubectl exec -n argocd -it deploy/argocd-server -- sh
```

#### FluxCD

For FluxCD we can use the special `flux-cli` pod to get a shell.  
This pod has a `ServiceAccount` with the `ClusterAdmin` role attached to it, so we can use the flux CLI to bootstrap and 
manage FluxCD without installing the flux CLI on our local machine.

```shell
# Check tool version
kubectl exec -n flux-system pods/flux-cli -- flux version
# Start an interactive shell
kubectl exec -n flux-system -it pods/flux-cli -- sh
```

## Integrated SCM

This playground includes Gitea so you can create local repositories and connect them to your GitOps operator
and to change the default example applications created and deployed to the cluster.

You may forward the SCM to your local system using `task scm:forward` and access it under http://localhost:8081.

You may login using the user `gitops` and the password `gitops` as the admin user has access to all repositories.

### SSH

If you want to be able to use SSH with your git client and the integrated Gitea you need to do a few things:

* Upload your SSH public key to the Gitea instance via the UI
* Bind the Gitea SSH port to a port on your machine with `kubectl port-forward --context gitops-test -n gitea service/gitea-ssh 2222:ssh`
* Use the bound port for your clone / push commands

### Example repositories

#### ArgoCD

##### argocd/argocd

This repository contains the default ArgoCD bootstrap files.
This is used to install ArgoCD to the cluster.
To bootstrap the `argocd` chart is installed using helm and `projects/argocd.yaml` and `applications/bootstrap.yaml` files
are applied imperatively. This will then bootstrap everything else.

Be aware that the `argocd` chart itself will from then on be managed by ArgoCD itself, so updates will be applied automatically.

**Directories**

* applications - Contains all the default gitops enabled applications
* argocd - Contains an ArgoCD wrapper chart to install the operator
* projects - Contains all the ArgoCD projects to be created

##### argocd/cluster-addons

This repository contains a more normal GitOps layout which installs all the cluster addons (ingress controller, cert manager, 
other operators, ...) and could be owned by a platform team.

**Directories**

* apps - Contains all the applications for the cluster addons (manifests, charts, value files, ...)
* argocd - Contains all the required ArgoCD manifests (mainly the `Application` resources)
* misc - Contains miscellaneous manifests which don't belong to any application (eg. shared `ConfigMap`s, `NetworkPolicies`, ...)

##### argocd/team-a and argocd/team-b

Those repositories contain a more normal GitOps layout and represents different teams deploying end-user applications.

**Directories**

* apps - Contains all the applications of the team (manifests, charts, value files, ...)
* argocd - Contains all the required ArgoCD manifests (mainly the `Application` resources)
* misc - Contains miscellaneous manifests which don't belong to any application (eg. shared `ConfigMap`s, `NetworkPolicies`, ...)

#### FluxCD

##### flux2/flux2

This repository is used to bootstrap FluxCD and cluster addons to the cluster.
It would be managed by a platform team.

The bootstrap happens with the `flux bootstrap` command.

**Directories**

* clusters - contains the basic configuration for the clusters
  * */flux-system - contains the fluxcd installation itself, it will be updated by the `flux bootstrap` command
* infrastructure - contains the infrastructure components like the ingress controller, cert manager, ...
* tenants - contains the configuration for specific tenants (eg. rbac, namespaces, configuration for team repositories, ...)

##### flux2/team-a and flux2/team-b

Those repositories contain a more normal GitOps layout and represents different teams deploying end-user applications.

**Directories**

* base - contains basic kustomization files for the deployments and releases
* production - contains kustomization files to patch the base for production
* staging - contains kustomization files to patch the base for staging

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

Configure ArgoCD with the sample repositories from Gitea to apply the desired state to the cluster.  
**Warning** Do not apply at the same time as FluxCD - this could cause things to break.

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

Configure FluxCD with the sample repositories from Gitea to apply the desired state to the cluster.  
**Warning** Do not apply at the same time as ArgoCD - this could cause things to break.

|Parameter|Default|
|--|--|
|cluster_name|gitops-test|

### scm:forward

Forward Gitea HTTP service in a given test cluster to a local port

|Parameter|Default|
|--|--|
|cluster_name|gitops-test|
|port|8081|
