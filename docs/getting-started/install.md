# CAPE quickstart

This guide shows you how to install and setup CAPE on a single Kubernetes cluster.

## Prerequisites

CAPE uses Helm charts to install components, verify you have version 3.0.0 or higher with the following command:

```shell
helm version --short
```

## Add Helm repositories

CAPE uses the NGINX ingress to manage access, first add it's Helm repository:

```shell
helm repo add ingress-nginx https://kubernetes.github.io/ingress-nginx
```

Add the Helm respositories for the CAPE resources:

```shell
helm repo add cape https://charts.cape.sh/
helm repo update
```

Verify that Helm added the repositories:

```shell
helm repo list
```

## Install Ingress

Install the NGINX ingress to the cluster with the following command:

```shell
helm upgrade --install ingress-nginx ingress-nginx \
  --repo https://kubernetes.github.io/ingress-nginx \
  --namespace ingress-nginx --create-namespace \
  --set controller.replicaCount=2
```

Verify that Helm installed the ingress:

```shell
helm list -A
```

Get the public IP address of the Ingress controller and note it for later steps:

```shell
kubectl get svc -n ingress-nginx
```

## Install CAPE

Install the CAPE components to the cluster with the following command:

<!-- TODO: Explain further -->

```shell
helm upgrade --install   -f ./cape/values.yaml cape  ./cape \
--set acceptTOS=true \
--set ingress.hostname={EXTERNAL_IP}  \
--set ingress.scheme=https \
--set scheme=https \
 --set licence="free10nodes" \
 --version 0.5.1
```

Verify that Helm installed the CAPE components:

```shell
kubectl get pods -n cape
```

## Select organization

<!-- TODO: Screenshot -->

CAPE supports multiple organization profiles, when you connect, select the one you wish to access from the dropdown, or create a new organization.

## Connect cluster

CAPE supports multiple Kubernetes providers, including:

- AliCloud ACK
- Amazon EKS
- Azure AKS
- DigitalOcean K8s
- Google GKE
- Huawei CCE
- RedHat OCP
- Kubernetes running on private clusters and on-premises

In each case you need to set a name for the cluster and a region.

You can connect to the cluster by either uploading a kubeconfig secret file, or by running a kubectl command, which takes the following form:

```shell
curl -ksfL https://{EXTERNAL_IP}/v1/agent/connect/{ORGANIZATION}:{CLUSTER_NAME}-maltkdsxinsytsw | kubectl apply -f-
```

When the Pods are ready, the cluster shows in the list with a "Healthy" state.

<!-- TODO: SCREENSHOT -->

## Create a federation

A federation is one or more clusters that…

You create a federation from the _Cluster Management_ -> _Federations_ menu item.

You first give the federation a name and then select the cluster to host the control plane for all the other clusters in the federation. Then you can add the other clusters to form the federation.

When the Pods are ready, the federation shows in the list with a "Success" state.

<!-- TODO: Order and structure -->

## Connect repository

Connect a repository from the _Application Management_ -> _Repositories_ menu item. You can select any public or private git-based repositories or helm charts, supplying a name and a URL.

If you need to define credentials for private repositories or Helm charts, add them from the _Application Management_ -> _Repositories_ menu item first using a user ID and password or private key.

## Create application

Create an application from the _Application Management_ -> _Applications_ menu item.

You first give the application a name, a repository and branch to use, and the name space to create the application in.

In the next step, select the folder in the repository that contains the Kubernetes manifests that define the application. This folder is relative to the repository root.

CAPE supports three deployment methods: Helm, Kustomize, and Manifest files. Select the one relevant for your application.

## Create a deployment

Create a deployment from the _Application Management_ -> _Deployments_ menu item.

You first give the deployment a name, an application to deploy, the namespace to deploy the application to, and git branch or tag to pull the application definitions from.

In the next step, select the federation and the members of the federation to deploy the application to.

If you now use `kubectl get namespaces` you can see the CAPE created namespace for the deployment, and if you list pods in the namespace with `kubectl get pods -n {NAMESPACE}` you can see the CAPE created pods for the deployment.

You can also click the _Details_ -> _See details_ link of any deployment to see a visual overview of Kubernetes resources in the deployment.

<!-- TODO: Access application? -->
