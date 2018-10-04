---
title: Quickstart - Deploy a Managed OpenShift cluster
description: Quickly learn to create a Managed OpenShift cluster for Linux containers with the Azure CLI.
services: container-service

ms.service: container-service
ms.topic: quickstart
ms.date: 09/24/2018
---

# Quickstart: Deploy a Managed OpenShift cluster

# Prerequisites

In this quickstart, a Managed OpenShift cluster is deployed using the Azure CLI.

OpenShift on Azure is in private preview. For this reason we are using a custom build of the Azure CLI.

To use it, you have to make sure Docker is installed on your machine.

[More information on how to install Docker can be found here.](https://docs.docker.com/install/)

> **Warning** : This CLI is not supported at this time. Please open Issues on this repo if you have questions or concerns.

The build of this CLI is under the `julienstroheker/osa-cli` container.

To start using `az`, run `docker run --rm -it julienstroheker/osa-cli /bin/bash`. This command will give you a bash inside the container where the `az openshift` commands are available.

From this bash your should be able to run any `az` commands and the `az openshift` one.

![](./medias/OSA_AZ_CLI.png)

## Step 0: Log in to Azure

The first step will be to authenticate yourself by running the `az login` command. This command will start the authentication flow using you browser. 

If you have access to multiple subscriptions, make sure to use the correct one by running the command `az account list -o table`. Then just validate if the field `isDefault` is equal `True` at the same line of your subscription. 

If not, you can run `az account set -s <SubId>` by remplacing <SubId> with the correct one.

## Step 1: Register your subscription to the Managed OpenShift Preview

To register your subscription please run the following commmand :

```azurecli-interactive
az feature register --namespace Microsoft.ContainerService -n openshiftmanagedcluster
```

Output :

```json
{
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Features/providers/Microsoft.ContainerService/features/openshiftmanagedcluster",
  "name": "Microsoft.ContainerService/openshiftmanagedcluster",
  "properties": {
    "state": "Pending"
  },
  "type": "Microsoft.Features/providers/features"
}
```

To verify the status of the registration :

```azurecli-interactive
az feature show --namespace Microsoft.ContainerService -n openshiftmanagedcluster
```

Make sure you can see the `"state": "Registered"` in the payload before executing the `openshift` commands.

## Step 2: Create a resource group

Create a resource group with the `az group create` command. An Azure resource group is a logical group in which Azure resources are deployed and managed. When you create a resource group, you are asked to specify a location. This location is where your resources run in Azure.

The following example creates a resource group named *myOSACluster* in the *eastus* location.

```azurecli-interactive
OSA_CLUSTER_NAME=myOSACluster
LOCATION=eastus

az group create --name $OSA_CLUSTER_NAME --location $LOCATION
```

Output:

```json
{
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myOSACluster",
  "location": "eastus",
  "managedBy": null,
  "name": "myOSACluster",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "tags": null
}
```

## Step 3: Create an OpenShift cluster (in a default VNET)

Use the `az openshift create` command to create an OpenShift cluster. 
The following example creates a cluster named *myOSACluster* with four nodes.

```azurecli-interactive
OSA_FQDN=$OSA_CLUSTER_NAME.$LOCATION.cloudapp.azure.com

az openshift create --resource-group $OSA_CLUSTER_NAME --name $OSA_CLUSTER_NAME -l $LOCATION --fqdn $OSA_FQDN
```

After several minutes, the command completes and returns a JSON-formatted information about the cluster.

> Note : If not specified, this command will automatically create an AAD Application for you to allow the cluster to run the authentication against Azure AD. If you want more details on how manage yourself this part, you can read more about in [this separated documentation.](./aad-application-configuration.md)

### Option: Create an OpenShift cluster in a custom VNET

The first step is to obtain the identifier of the VNET you wants to peer with.

For example, if your VNET name is `my-custom-vnet` inside the `my-custom-vnet-rg` resource group, you will have to run the following commands :

```
PEER_VNET_NAME=my-custom-vnet
PEER_VNET_RG=my-custom-vnet-rg
PEER_VNET_ID=$(az network vnet show -n $PEER_VNET_NAME -g $PEER_VNET_RG --query id -o tsv)
```

Use the `az openshift create` command to create an OpenShift on Azure cluster. 
The following example creates a cluster named *myOSACluster* with four nodes and peer it to a custom VNet.

```azurecli-interactive
OSA_FQDN=$OSA_CLUSTER_NAME.$LOCATION.cloudapp.azure.com

az openshift create --resource-group $OSA_CLUSTER_NAME --name $OSA_CLUSTER_NAME -l $LOCATION --fqdn $OSA_FQDN --vnet-peer-id $PEER_VNET_ID
```

## Step 4: Connect to the cluster

After your deployment is done, you should be able to open your browser to the `fqdn` that you choose during the creation of your cluster.

For example : `https://myOSACluster.eastus.cloudapp.azure.com`

> You will have a `NET::ERR_CERT_AUTHORITY_INVALID` error from your browser, you will have to validate the connexion manually.

Click on `Azure AD`

![](./medias/OSA_Auth.png)

![](./medias/OSA_Console.png)

## Step 5: Using OC CLI
Click on the upper right corner (profile name) to get the CLI login information. 

![](./medias/OSA_CLI.png)

You need the OC CLI which can be downloaded from https://github.com/openshift/origin/releases

> Using MacOS, you can easly install it with homebrew `brew install openshift-cli
`
 
Login using OC CLI by copying the command above:
```
oc login <FQDN> --token=<YOUR_TOKEN>
```


<!-- LINKS - external -->
[OpenShift CLI]: https://github.com/openshift/origin/releases

<!-- LINKS - internal -->
[az-group-create]: /cli/azure/group#az-group-create
[az-group-delete]: /cli/azure/group#az-group-delete
[azure-cli-install]: /cli/azure/install-azure-cli
[azure-portal]: https://portal.azure.com